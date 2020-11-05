# 多线程 - AQS（CLH)



各种JUC同步锁

ReentrantLock

CountDownLatch

CyclicBarrier

Phaser

ReadWriteLock - StampedLock

Semaphore

Exchanger

LockSupoort





**AQS**是AbstractQueuedSynchronizer的简称，即抽象队列同步器，从字面意思上理解:

- 抽象：抽象类，只实现一些主要逻辑，有些方法由子类实现；
- 队列：使用先进先出（FIFO）队列存储数据；
- 同步：实现了同步的功能。

那AQS有什么用呢？AQS是一个用来构建锁和同步器的框架，使用AQS能简单且高效地构造出应用广泛的同步器，比如我们提到的ReentrantLock，Semaphore，ReentrantReadWriteLock，SynchronousQueue，FutureTask等等皆是基于AQS的。

当然，我们自己也能利用AQS非常轻松容易地构造出符合我们自己需求的同步器，只要之类实现它的几个protected方法就可以了，在下文会有详细的介绍。



### 11.2  AQS的数据结构

AQS核⼼思想是，如果被请求的共享资源空闲，则将当前请求资源的线程设置为有效的⼯作线程，并且 将共享资源设置为锁定状态。如果被请求的共享资源被占⽤，那么就需要⼀套线程阻塞等待以及被唤醒 时锁分配的机制，这个机制AQS是⽤CLH队列(**Craig,Landin,and Hagersten 三人的简称)队列是⼀个虚拟的双向队列**)锁实现的，即将暂时获取不到锁的线程加⼊到队列中。



它内部使用了一个先进先出（FIFO）的双端队列（双向链表，并使用了两个指针head和tail用于标识队列的头部和尾部。其数据结构如图：

资源：state ，获取到锁

![image.png](H:/akane-note/🍰编程语言/多线程/images/java36.png)





### 11.3 资源共享模式

资源有两种共享模式，或者说两种同步方式：

- 独占模式（Exclusive）：资源是独占的，一次只能一个线程获取。如ReentrantLock。
- 共享模式（Share）：同时可以被多个线程获取，具体的资源个数可以通过参数指定。如Semaphore/CountDownLatch。

一般情况下，子类只需要根据需求实现其中一种模式，当然也有同时实现两种模式的同步类，如ReadWriteLock。



### 11.4 AQS源码分析

采用的是模板方法设计模式

![image.png](H:/akane-note/🍰编程语言/多线程/images/java37.png)









### 11.6 ReadWriteLock

ReadWriteLock可以解决多线程同时读，但只有一个线程能写的问题。

读锁共享，写锁独占。



### 11.7 StampedLock - 乐观锁和悲观锁

前面介绍的ReadWriteLock可以解决多线程同时读，但只有一个线程能写的问题。

如果我们深入分析ReadWriteLock，会发现它有个潜在的问题：如果有线程正在读，写线程需要等待读线程释放锁后才能获取写锁，即读的过程中不允许写，这是一种悲观的读锁。



要进一步提升并发执行效率，Java 8引入了新的读写锁：StampedLock。

StampedLock和ReadWriteLock相比，改进之处在于：读的过程中也允许获取写锁后写入！这样一来，我们读的数据就可能不一致，所以，需要一点额外的代码来判断读的过程中是否有写入，这种读锁是一种乐观锁。



可见，StampedLock把读锁细分为乐观读和悲观读，能进一步提升并发效率。但这也是有代价的：一是代码更加复杂，二是StampedLock是不可重入锁，不能在一个线程中反复获取同一个锁。



### 11.8 BlockingQueue- 阻塞队列

BlockingQueue的意思就是说，当一个线程调用这个TaskQueue的getTask()方法时，该方法内部可能会让线程变成等待状态，直到队列条件满足不为空，线程被唤醒后，getTask()方法才会返回。





java.util.Collections工具类还提供了一个旧的线程安全集合转换器，可以这么用：

Map unsafeMap = new HashMap(); Map threadSafeMap = Collections.synchronizedMap(unsafeMap);

但是它实际上是用一个包装类包装了非线程安全的Map，然后对所有读写方法都用synchronized加锁，这样获得的线程安全集合的性能比java.util.concurrent集合要低很多，所以不推荐使用。





## 十二 线程池原理



### 12.1 为什么要使用线程池

使用线程池主要有以下三个原因：

1. 创建/销毁线程需要消耗系统资源，线程池可以**复用已创建的线程**。
2. **控制并发的数量**。并发数量过多，可能会导致资源消耗过多，从而造成服务器崩溃。（主要原因）
3. **可以对线程做统一管理**。



**执⾏execute()⽅法和submit()⽅法的区别**

1. execute() ⽅法⽤于提交**不需要返回值的任务**，所以⽆法判断任务是否被线程池执⾏成功与 否；
2. submit() ⽅法⽤于提交需要返回值的任务。**线程池会返回⼀个 Future 类型的对象**，通过这个 Future 对象可以判断任务是否执⾏成功，并且可以通过 Future 的 get() ⽅法来获取 返回值， get() ⽅法会阻塞当前线程直到任务完成，⽽使⽤ get（long timeout， TimeUnit unit） ⽅法则会阻塞当前线程⼀段时间后⽴即返回，这时候有可能任务没有执⾏ 完。



### 12.2 线程池参数

涉及到5~7个参数，我们先看看必须的5个参数是什么意思：

- **int corePoolSize**：该线程池中**核心线程数最大值**

核心线程：线程池中有两类线程，核心线程和非核心线程。核心线程默认情况下会一直存在于线程池中，即使这个核心线程什么都不干（铁饭碗），而非核心线程如果长时间的闲置，就会被销毁（临时工）。

- **int maximumPoolSize**：该线程池中**线程总数最大值** 。

该值等于核心线程数量 + 非核心线程数量。

- **long keepAliveTime**：**非核心线程闲置超时时长**。

非核心线程如果处于闲置状态超过该值，就会被销毁。如果设置allowCoreThreadTimeOut(true)，则会也作用于核心线程。

- **TimeUnit unit**：keepAliveTime的单位。

TimeUnit是一个枚举类型 ，包括以下属性：

NANOSECONDS ： 1微毫秒 = 1微秒 / 1000 MICROSECONDS ： 1微秒 = 1毫秒 / 1000 MILLISECONDS ： 1毫秒 = 1秒 /1000 SECONDS ： 秒 MINUTES ： 分 HOURS ： 小时 DAYS ： 天

- **BlockingQueue workQueue**：阻塞队列，维护着**等待执行的Runnable任务对象**。



常用的几个阻塞队列：

1. 1. **LinkedBlockingQueue**

链式阻塞队列，底层数据结构是链表，默认大小是Integer.MAX_VALUE，也可以指定大小。

1. 1. **ArrayBlockingQueue**

数组阻塞队列，底层数据结构是数组，需要指定队列的大小。

1. 1. **SynchronousQueue**

同步队列，内部容量为0，每个put操作必须等待一个take操作，反之亦然。

1. 1. **DelayQueue**

延迟队列，该队列中的元素只有当其指定的延迟时间到了，才能够从队列中获取到该元素 。



好了，介绍完5个必须的参数之后，还有两个非必须的参数。

**ThreadFactory threadFactory**

创建线程的工厂 ，用于批量创建线程，统一在创建线程时设置一些参数，如是否守护线程、线程的优先级等。如果不指定，会新建一个默认的线程工厂



### RejectedExecutionHandler handler

**拒绝处理策略**，线程数量大于最大线程数就会采用拒绝处理策略，四种拒绝处理的策略为 ：

1. **ThreadPoolExecutor.AbortPolicy**：**默认拒绝处理策略**，丢弃任务并抛出RejectedExecutionException异常。
2. **ThreadPoolExecutor.DiscardPolicy**：丢弃新来的任务，但是不抛出异常。
3. **ThreadPoolExecutor.DiscardOldestPolicy**：丢弃队列头部（最旧的）的任务，然后重新尝试执行程序（如果再次失败，重复此过程）。
4. **ThreadPoolExecutor.CallerRunsPolicy**：由调用线程处理该任务。



### 12.3 线程池主要的任务处理流程

1. 线程总数量 < corePoolSize，无论线程是否空闲，都会新建一个核心线程执行任务（让核心线程数量快速达到corePoolSize，在核心线程数量 < corePoolSize时）。**注意，这一步需要获得全局锁。**
2. 线程总数量 >= corePoolSize时，新来的线程任务会进入任务队列中等待，然后空闲的核心线程会依次去缓存队列中取任务来执行（体现了**线程复用**）。
3. 当缓存队列满了，说明这个时候任务已经多到爆棚，需要一些“临时工”来执行这些任务了。于是会创建非核心线程去执行这个任务。**注意，这一步需要获得全局锁。**
4. 缓存队列满了， 且总线程数达到了maximumPoolSize，则会采取上面提到的拒绝策略进行处理。
5. ![image.png](H:/akane-note/🍰编程语言/多线程/images/java38.png)



### 