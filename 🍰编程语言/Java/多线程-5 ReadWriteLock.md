# ReadWriteLock



其实是一个共享锁和排他锁的概念。



用途：读多写少



/**
 * ReadWriteLock 示例
 *
 * 只允许一个线程写入（其他线程既不能写入也不能读取）；
 * 没有写入时，多个线程允许同时读（提高性能）。
 *
 *
 * 1.把读写操作分别用读锁和写锁来加锁，在读取时，多个线程可以同时获得读锁，这样就大大提高了并发读的执行效率。
 * 2. 使用ReadWriteLock时，适用条件是同一个数据，有大量线程读取，但仅有少数线程修改。
 *
 * 例如，一个论坛的帖子，回复可以看做写入操作，它是不频繁的，但是，浏览可以看做读取操作，是非常频繁的，这种情况就可以使用ReadWriteLock。
 */



```java
import java.util.Arrays;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

public class ReentrantReadWriteLockDemo {

    private final ReadWriteLock rwLock = new ReentrantReadWriteLock();
    private final Lock readLock = rwLock.readLock();
    private final Lock writeLock = rwLock.writeLock();
    private int[] counts = new int[10];

    /**
     * 写
     */
    public void addTask(int index){
        writeLock.lock();
        try {
            counts[index] += 1;
        }finally {
            writeLock.unlock();
        }

    }

    /**
     * 读
     */
    private int[] getTask(){
        readLock.lock();
        try{
            return Arrays.copyOf(counts, counts.length);
        }finally {
            readLock.unlock();
        }
    }
}
```