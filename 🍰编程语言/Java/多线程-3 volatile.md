# 多线程 - volatile



保证线程内存可见性

- MESI 缓存一致性协议



，防止指令重排





volatile 的意思是可见的，常用来修饰某个共享变量，意思是当共享变量的值被修改后，会及时通知到其它线程上，其它线程就能知道当前共享变量的值已经被修改了。



我们再说原理之前，先说下基础知识。就是在多核 CPU 下，为了提高效率，线程在拿值时，是直接和 CPU 缓存打交道的，而不是内存。主要是因为 CPU 缓存执行速度更快，比如线程要拿值 C，会直接从 CPU 缓存中拿， CPU 缓存中没有，就会从内存中拿，所以线程读的操作永远都是拿 CPU 缓存的值。

这时候会产生一个问题，CPU 缓存中的值和内存中的值可能并不是时刻都同步，导致线程计算的值可能不是最新的，共享变量的值有可能已经被其它线程所修改了，但此时修改是机器内存的值，CPU 缓存的值还是老的，导致计算会出现问题。



这时候有个机制，就是内存会主动通知 CPU 缓存。当前共享变量的值已经失效了，你需要重新来拉取一份，CPU 缓存就会重新从内存中拿取一份最新的值。



volatile 关键字就会触发这种机制，加了 volatile 关键字的变量，就会被识别成共享变量，内存中值被修改后，会通知到各个 CPU 缓存，使 CPU 缓存中的值也对应被修改，从而保证线程从 CPU 缓存中拿取出来的值是最新的。



### JMM

在JMM中，我们把多个线程间通信的共享内存称之为主内存，⽽在并发编程中多个线程都维护了⼀个⾃⼰的本地内存（这是个抽 象概念），其中保存的数据是主内存中的数据拷⻉。⽽JMM主要是控制本地内存和主内存之间的数据交互的。 

![image.png](H:/akane-note/🍰编程语言/Java/images/java3.png)



我们画了一个图来说明一下：

![image.png](images/java13.png)

- 并不能保证线程安全性，保证了可见性，但不保证原子性。
- count++不具备原子性，实际上包含了三个操作：获取值，自增，赋值。



### 7.1什么是重排序？

计算机在执行程序时，为了提高性能，编译器和处理器常常会对指令做重排。



指令重排一般分为以下三种：

- **编译器优化重排**

编译器在**不改变单线程程序语义**的前提下，可以重新安排语句的执行顺序。

- **指令并行重排**

现代处理器采用了指令级并行技术来将多条指令重叠执行。如果**不存在数据依赖性**(即后一个执行的语句无需依赖前面执行的语句的结果)，处理器可以改变语句对应的机器指令的执行顺序。

- **内存系统重排**

由于处理器使用缓存和读写缓存冲区，这使得加载(load)和存储(store)操作看上去可能是在乱序执行，因为三级缓存的存在，导致内存与缓存的数据同步存在时间差。

**指令重排可以保证串行语义一致，但是没有义务保证多线程间的语义也一致**。所以在多线程下，指令重排序可能会导致一些问题。



### 8.4 volatile的内存定义



在Java中，volatile关键字有特殊的内存语义。volatile主要有以下两个功能：

- 保证变量的**内存可见性**
- 禁止volatile变量与普通变量**重排序**（JSR133提出，Java 5 开始才有这个“增强的volatile内存语义”）

**指令重排可以保证串行语义一致，但是没有义务保证多线程间的语义也一致**。所以在多线程下，指令重排序可能会导致一些问题。



### 8.5 禁止重排序

在JSR-133之前的旧的Java内存模型中，是允许volatile变量与普通变量重排序的。那上面的案例中，可能就会被重排序成下列时序来执行：

1. 线程A写volatile变量，step 2，设置flag为true；
2. 线程B读同一个volatile，step 3，读取到flag为true；
3. 线程B读普通变量，step 4，读取到 a = 0；
4. 线程A修改普通变量，step 1，设置 a = 1；



可见，如果volatile变量与普通变量发生了重排序，虽然volatile变量能保证内存可见性，也可能导致普通变量读取错误。

所以在旧的内存模型中，volatile的写-读就不能与锁的释放-获取具有相同的内存语义了。为了提供一种比锁更轻量级的**线程间的通信机制**，**JSR-133**专家组决定增强volatile的内存语义：严格限制编译器和处理器对volatile变量与普通变量的重排序。



编译器还好说，JVM是怎么还能限制处理器的重排序的呢？它是通过**内存屏障**来实现的。



什么是内存屏障？硬件层面，内存屏障分两种：读屏障（Load Barrier）和写屏障（Store Barrier）。内存屏障有两个作用：

1. 阻止屏障两侧的指令重排序；
2. 强制把写缓冲区/高速缓存中的脏数据等写回主内存，或者让缓存中相应的数据失效。

注意这里的缓存主要指的是CPU缓存，如L1，L2等



编译器在**生成字节码时**，会在指令序列中插入内存屏障来禁止特定类型的处理器重排序。编译器选择了一个**比较保守的JMM内存屏障插入策略**，这样可以保证在任何处理器平台，任何程序中都能得到正确的volatile内存语义。这个策略是：

- 在每个volatile写操作前插入一个StoreStore屏障；
- 在每个volatile写操作后插入一个StoreLoad屏障；
- 在每个volatile读操作后插入一个LoadLoad屏障；
- 在每个volatile读操作后再插入一个LoadStore屏障。





再介绍一下volatile与普通变量的重排序规则:

1. 如果第一个操作是volatile读，那无论第二个操作是什么，都不能重排序；
2. 如果第二个操作是volatile写，那无论第一个操作是什么，都不能重排序；
3. 如果第一个操作是volatile写，第二个操作是volatile读，那不能重排序。





## 八 volatile

### 8.1 内存可见性

**内存可见性，指的是线程之间的可见性，当一个线程修改了共享变量时，另一个线程可以读取到这个修改后的值**。



### 8.2 重排序

为优化程序性能，对原有的指令执行顺序进行优化重新排序。重排序可能发生在多个阶段，比如编译重排序、CPU重排序等。







### 8.6 用途

从volatile的内存语义上来看，volatile可以保证内存可见性且禁止重排序。

在保证内存可见性这一点上，volatile有着与锁相同的内存语义，所以可以作为一个“轻量级”的锁来使用。但由于volatile仅仅保证对单个volatile变量的读/写具有原子性，而锁可以保证整个**临界区代码**的执行具有原子性。所以**在功能上，锁比volatile更强大；在性能上，volatile更有优势**。



例子：双重锁检验 单例模式







### 6.3 volatile  和 synchronized

Java中的volatile关键字可以保证多线程操作共享变量的可见性以及禁止指令重排序，synchronized关键字不仅保证可见性，同时也保证了原子性（互斥性）。在更底层，JMM通过内存屏障来实现内存的可见性以及禁止重排序。为了程序员的方便理解，提出了happens-before，它更加的简单易懂，从而避免了程序员为了理解内存可见性而去学习复杂的重排序规则以及这些规则的具体实现方法。这里涉及到的所有内容后面都会有专门的章节介绍。





## 六 Java内存模型基础知识



### 6.1 并发编程模型的两个关键问题

- 线程间如何通信？即：线程之间以何种机制来交换信息
- 线程间如何同步？即：线程以何种机制来控制不同线程间操作发生的相对顺序

有两种并发模型可以解决这两个问题：

- 消息传递并发模型
- 共享内存并发模型

这两种模型之间的区别如下表所示：

![image.png](H:/akane-note/🍰编程语言/Java/images/java7.png)



**在Java中，使用的是共享内存并发模型**。



### 6.2 既然堆是共享的，为什么在堆中会有内存不可见问题？

![image.png](H:/akane-note/🍰编程语言/Java/images/java3.png)

从图中可以看出：

1. 所有的共享变量都存在主内存中。
2. 每个线程都保存了一份该线程使用到的共享变量的副本。
3. 如果线程A与线程B之间要通信的话，必须经历下面2个步骤：

4. 1. 线程A将本地内存A中更新过的共享变量刷新到主内存中去。
   2. 线程B到主内存中去读取线程A之前已经更新过的共享变量。



**所以，线程A无法直接访问线程B的工作内存，线程间通信必须经过主内存。**



注意，根据JMM的规定，**线程对共享变量的所有操作都必须在自己的本地内存中进行，不能直接从主内存中读取**。



所以线程B并不是直接去主内存中读取共享变量的值，而是先在本地内存B中找到这个共享变量，发现这个共享变量已经被更新了，然后本地内存B去主内存中读取这个共享变量的新值，并拷贝到本地内存B中，最后线程B再读取本地内存B中的新值。





单例模式-Double Check Lock

不加volatile可以吗？

大部分时候是不存在问题，但是如果说有指令重排，就会存在问题



volatile不能保证原子性

synchronized不能禁止重排序



对象**还没初始化完毕**的时候，第二个线程发现对象已经不为null，就会拿来用，调用里面的方法或者属性就会有问题。



```
/**
 * volatile不支持原子性
 */
public class T {

    volatile int count = 0;
    void sum(){
        for (int i = 0; i < 10000; i++) {
            count++;
        }
    }

    public static void main(String[] args) {

        T t = new T();
        List<Thread> threads = new ArrayList<>(10);

        for (int i = 0; i < 10; i++) {
            threads.add(new Thread(t::sum, "thread-" + i));
        }
        threads.forEach(o -> o.start());

        threads.forEach(o -> {
            try {
                o.join();
            }catch (InterruptedException e){}
        });

        System.out.println("sum:" + t.count); // != 100000
    }
}
```



```java
/**
 * 指令重排序测试
 */
public class JustTest {

    public static void main(String[] args) {
        Object o = new Object();
    }
}
// 查看字节码
// class version 52.0 (52)
// access flags 0x21
public class com/example/thread/other/JustTest {

  // compiled from: JustTest.java

  // access flags 0x1
  public <init>()V
   L0
    LINENUMBER 6 L0
    ALOAD 0
    INVOKESPECIAL java/lang/Object.<init> ()V
    RETURN
   L1
    LOCALVARIABLE this Lcom/example/thread/other/JustTest; L0 L1 0
    MAXSTACK = 1
    MAXLOCALS = 1

  // access flags 0x9
  public static main([Ljava/lang/String;)V
   L0
    LINENUMBER 9 L0
    NEW java/lang/Object
    DUP
    INVOKESPECIAL java/lang/Object.<init> ()V
    ASTORE 1
   L1
    LINENUMBER 10 L1
    RETURN
   L2
    LOCALVARIABLE args [Ljava/lang/String; L0 L2 0
    LOCALVARIABLE o Ljava/lang/Object; L1 L2 1
    MAXSTACK = 2
    MAXLOCALS = 2
}
// 分析：先NEW
    LINENUMBER 9 L0
    NEW java/lang/Object
    DUP
    INVOKESPECIAL java/lang/Object.<init> ()V
    ASTORE 1
```