# ReadWriteLock



ReadWriteLock其实是一个共享锁和排他锁的概念。



用途：读多写少

读：多个线程可以同时获取，不互斥

写：排他锁，只能有一个线程写



例如，一个论坛的帖子，回复可以看做写入操作，它是不频繁的，但是，浏览可以看做读取操作，是非常频繁的，这种情况就可以使用ReadWriteLock。

```java
public class ReentrantReadWriteLockDemo {

    private static final ReentrantReadWriteLock rwLock = new ReentrantReadWriteLock();
    private static final Lock readLock = rwLock.readLock();
    private static final Lock writeLock = rwLock.writeLock();
    private static int value ;

    /**
     * 写
     */
    public static void read(Lock lock){
        try {
            lock.lock();
            Thread.sleep(1000);
            System.out.println("read over!" + value);
        }catch (InterruptedException e){
        } finally {
            lock.unlock();
        }

    }

    /**
     * 读
     */
    public static void write(Lock lock, int v){
        try{
            lock.lock();
            Thread.sleep(1000);
            value = v;
            System.out.println("write over!");
        }catch (InterruptedException e){
        } finally {
            lock.unlock();
        }
    }

    public static void main(String[] args) {
        // 共享锁
        Runnable read = () -> read(readLock);
        // 排他锁
        Runnable write = () -> write(writeLock, new Random().nextInt(100));

        for (int i = 0; i < 10; i++) {
            new Thread(read).start();
        }

        for (int i = 0; i < 2; i++) {
            new Thread(write).start();
        }
    }
}

```



结果：随机

从结果可以看出，读不堵塞，共享锁。但是写排斥，排他锁

```
read over!0
read over!0
read over!0
read over!0
read over!0
read over!0
read over!0
write over!
read over!85
read over!85
read over!85
write over!
```

