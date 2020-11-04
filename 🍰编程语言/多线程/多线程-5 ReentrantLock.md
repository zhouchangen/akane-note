# 多线程 - ReentrantLock



## ReentrantLock可重入锁

ReentrantLock：重入锁的意思

ReentrantLock和synchronized一样，是可重入锁，但是却提供了更多特性，用于代替synchronized。



## 示例

### 示例1：手动释放锁

说明一点，ReentranLock必须使用try-finally手动释放锁，而synchronized不需要，synchronized遇到异常也会释放锁。

```java
/**
 * ReentrantLock 示例
 */
public class ReentrantLockDemo {

    private final Lock lock = new ReentrantLock();

    public String getTask() throws InterruptedException{
        
        try{
            lock.lock();
        }finally {
            lock.unlock();
        }
    }
}
```

### 示例2：尝试获取锁

ReentranLock还可以尝试获取锁，你可以选择wait还是不wait。另外可以设置等待的时长。

```
public boolean tryLock(long timeout, TimeUnit unit)
```

```java
/**
 * ReentrantLock 示例
 */
public class ReentrantLockDemo {

    private final Lock lock = new ReentrantLock();

    public String getTask() throws InterruptedException{
        boolean locked = false;
        try{
            locked = lock.tryLock();
            if (locked){
                
            }
        }finally {
            if (locked){
                lock.unlock();
            }
        }
    }
}
```



### 示例3：提供中断等待锁的线程机制

ReentrantLock可以通过lock.lockInterruptibly()，实现中断等待锁的线程机制。

注：如果是用lock()获取不到锁则会一直阻塞，也不能中断线程。而如果是lockInterruptibly，则可以通过Thread.interrupt()中断等待锁的线程。

```java
    public static void main(String[] args) throws InterruptedException{
        final Lock lock = new ReentrantLock();
        // 获取锁
        lock.lock();
        Thread.sleep(2000);
        Thread t1 = new Thread( () -> {
//            lock.lock();

            try{
               lock.lockInterruptibly();
               System.out.println("获取锁");
            }catch (InterruptedException e){
               System.out.println("interrupted");
            }

        });
        t1.start();
        Thread.sleep(1000);
        t1.interrupt();
    }
```

结果：interrupted



### 示例4：可指定公平锁和非公平锁

ReentrantLock可以指定为公平锁和非公平锁，⽽synchronized只能是⾮公平锁。ReentrantLock默认情况是⾮公平锁。

公平锁：先等待的线程先获得锁

```java
private final Lock lock = new ReentrantLock(true);


    /**
     * Creates an instance of {@code ReentrantLock}.
     * This is equivalent to using {@code ReentrantLock(false)}.
     * 默认非公平锁
     */ 
    public ReentrantLock() {
        sync = new NonfairSync();
    }

    /**
     * Creates an instance of {@code ReentrantLock} with the
     * given fairness policy.
     *
     * @param fair {@code true} if this lock should use a fair ordering policy
     */
    public ReentrantLock(boolean fair) {
        sync = fair ? new FairSync() : new NonfairSync();
    }

```



## ReentrantLock实现原理

本质是CAS，底层调用的是Unsafe的park方法加锁

```java
/**
 * Sync object for non-fair locks
 */
static final class NonfairSync extends Sync {
    private static final long serialVersionUID = 7316153563782823691L;

    /**
     * Performs lock.  Try immediate barge, backing up to normal
     * acquire on failure.
     */
    final void lock() {
        if (compareAndSetState(0, 1))
            setExclusiveOwnerThread(Thread.currentThread());
        else
            // 获得锁
            acquire(1);
    }

    protected final boolean tryAcquire(int acquires) {
        return nonfairTryAcquire(acquires);
    }
}
// --------------------------------------------------------------------------------------------------------------------
    public final void acquire(int arg) {
        if (!tryAcquire(arg) &&
            acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
            selfInterrupt();
    }
// --------------------------------------------------------------------------------------------------------------------
// 等待队列
    /**
     * Acquires in exclusive uninterruptible mode for thread already in
     * queue. Used by condition wait methods as well as acquire.
     *
     * @param node the node
     * @param arg the acquire argument
     * @return {@code true} if interrupted while waiting
     */
    final boolean acquireQueued(final Node node, int arg) {
        boolean failed = true;
        try {
            boolean interrupted = false;
            // 死循环
            for (;;) {
                final Node p = node.predecessor();
                if (p == head && tryAcquire(arg)) {
                    setHead(node);
                    p.next = null; // help GC
                    failed = false;
                    return interrupted;
                }
                // 失败后是否需要park
                if (shouldParkAfterFailedAcquire(p, node) &&
                    parkAndCheckInterrupt())
                    interrupted = true;
            }
        } finally {
            if (failed)
                cancelAcquire(node);
        }
    }
// --------------------------------------------------------------------------------------------------------------------
    /**
     * Convenience method to park and then check if interrupted
     *
     * @return {@code true} if interrupted
     */
    private final boolean parkAndCheckInterrupt() {
        LockSupport.park(this);
        return Thread.interrupted();
    }
// --------------------------------------------------------------------------------------------------------------------
//  LockSupport.park(this);最终调用Unsafe的park()
//  在CAS中也有介绍过Unsafe，而park与unpark用于线程挂起和恢复
    public static void park(Object blocker) {
        Thread t = Thread.currentThread();
        setBlocker(t, blocker);
        UNSAFE.park(false, 0L);
        setBlocker(t, null);
    }

```

总的来说，ReentrantLock是基于CAS的，不同的是多了线程的挂起和恢复，而这个操作是通过Unsafe的park和unpark实现的。



## ReentrantLock和synchronized区别

1. 和synchronized一样，ReentrantLock是支持可重入锁。**本质上ReentrantLock是CAS，synchronized是锁对象头，锁升级的概念。**
2. 和synchronized不同的是，ReentrantLock可以尝试获取锁，并且可以设置获取锁等待的时长。ReentrantLock需手动释放锁。
3. ReentrantLock提供了一种**中断等待锁的线程**机制。
4. ReentrantLock可以指定为公平锁和非公平锁，⽽synchronized只能是⾮公平锁。





③可实现选择性通知（锁可以绑定多个条件）



**synchronized关键字与wait()和notify()/notifyAll()⽅法相结合可以实现等待/通知机制**， ReentrantLock类当然也可以实现，但是需要借助于**Condition**接⼝与newCondition() ⽅法。 Condition是JDK1.5之后才有的，它具有很好的灵活性，⽐如可以实现多路通知功能也就是在⼀ 个Lock对象中可以创建多个Condition实例（即对象监视器），线程对象可以注册在指定的 Condition中，从⽽可以有选择性的进⾏线程通知，在调度线程上更加灵活。 在使⽤ notify()/notifyAll()⽅法进⾏通知时，被通知的线程是由 JVM 选择的，⽤ReentrantLock类结 合Condition实例可以实现“选择性通知” ，这个功能⾮常重要，⽽且是Condition接⼝默认提供 的。⽽synchronized关键字就相当于整个Lock对象中只有⼀个Condition实例，所有的线程都注 册在它⼀个身上。如果执⾏notifyAll()⽅法的话就会通知所有处于等待状态的线程这样会造成 很⼤的效率问题，⽽Condition实例的signalAll()⽅法 只会唤醒注册在该Condition实例中的所 有等待线程。



示例：

```java
/**
 * 可重入锁 ReentrantLock 示例
 * Re entrant Lock
 *
 *
 * 说明：Java的synchronized锁是可重入锁
 * JVM允许同一个线程重复获取同一个锁，这种能被同一个线程反复获取的锁，就叫做可重入锁
 */
public class ReentrantLockDemo {

    // 1. ReentrantLock是可重入锁，它和synchronized一样，一个线程可以多次获取同一个锁。
    // 2. 和synchronized不同的是，ReentrantLock可以尝试获取锁：
    // 3. 使用ReentrantLock比直接使用synchronized更安全，线程在tryLock()失败的时候不会导致死锁
    private final Lock lock = new ReentrantLock();

    private final Condition condition = lock.newCondition();
    private Queue<String> queue = new LinkedList<>();



    public void addTask(String s){
        lock.lock();
        try{
            queue.add(s);
            // 相当于 synchronized 中的 notifyAll();
            condition.signalAll();
        }finally {
            lock.unlock();
        }
    }

    public String getTask() throws InterruptedException{
        try{
        	lock.lock();
            while (queue.isEmpty()){
                // 相当于 synchronized 中的 wait();
                // 会释放当前锁，进入等待状态；
                condition.await();
            }
            return queue.remove();
        }finally {
            lock.unlock();
        }
    }
    
   public static void main(String[] args) {

    }
}
```









