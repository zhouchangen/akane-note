# 多线程 - ThreadLocal



## ThreadLocal

作用：进行线程隔离，为每个线程提供一个私有变量。



问：那为什么不直接声明一个私有变量？

答：如果将类的某个**静态变量**（user ID或者transaction ID）与线程状态关联，声明变量的形式就不行了，因此有了ThreaLocal

```java
private static ThreadLocal<DateFormat> threadLocal = new ThreadLocal<>();
```



### ThreadLocal实现原理

ThreadLocal是一种以空间换时间的做法，在每个Thread里面维护了一个以开地址法实现ThreadLocal.ThreadLocalMap，把数据进行隔离，数据不共享。

ThreadLocal 内部维护的是⼀个类似 Map 的 ThreadLocalMap 数据结构， key 为当前对象的 Thread 对象，值为 Object 对象。

```java
public class Thread implements Runnable {
 ......
//与此线程有关的ThreadLocal值。由ThreadLocal类维护
ThreadLocal.ThreadLocalMap threadLocals = null;
//与此线程有关的InheritableThreadLocal值。由InheritableThreadLocal类维护
ThreadLocal.ThreadLocalMap inheritableThreadLocals = null;
 ......
}

public void set(T value) {
     Thread t = Thread.currentThread();
     ThreadLocalMap map = getMap(t);
     if (map êX null)
        map.set(this, value);
     else
        createMap(t, value);
     }
     ThreadLocalMap getMap(Thread t) {
     return t.threadLocals;
 }
```



### ThreadLocal 内存泄露问题

ThreadLocalMap 中使⽤的 key 为 ThreadLocal 的弱引⽤,⽽ value 是强引⽤。



key：弱引用。如果 ThreadLocal 没有被外部强引⽤，在垃圾回收的时候，key可能被清理掉，但value不会。这样ThreadLocalMap 中就会出现**key为null的Entry**。value 永远⽆法被GC 回收，产生内存泄漏。



### ThreadLocalMap泄漏解决办法

在调⽤ set() 、 get() 、 remove() ⽅法的时候，会清理掉 key 为 null 的记录。

**使⽤完 ThreadLocal ⽅法后 最好⼿动调⽤ remove() ⽅法**

```java
    static class ThreadLocalMap {

        /**
         * The entries in this hash map extend WeakReference, using
         * its main ref field as the key (which is always a
         * ThreadLocal object).  Note that null keys (i.e. entry.get()
         * == null) mean that the key is no longer referenced, so the
         * entry can be expunged from table.  Such entries are referred to
         * as "stale entries" in the code that follows.
         */
        static class Entry extends WeakReference<ThreadLocal<?>> {
            /** The value associated with this ThreadLocal. */
            Object value;

            Entry(ThreadLocal<?> k, Object v) {
                super(k);
                value = v;
            }
        }
```





