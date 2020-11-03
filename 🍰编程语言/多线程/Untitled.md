问题：

实现一个容器，提供两个方法，add，size。

写两个线程，线程1添加10个元素到容器中，线程2实现监控元素的个数，当个数到5个时，线程2给出提示并结束。



写一个固定容量同步容器，拥有put和get方法，以及getCount方法，能够支持2个生产者线程以及10个消费者线程阻塞调用



## ThreadLocal

由CAS引发的

可以看到，虽然两个线程使用的同一个ThreadLocal实例（通过构造方法传入），但是它们各自可以存取自己当前线程的一个值。

那ThreadLocal有什么作用呢？如果只是单纯的想要线程隔离，在每个线程中声明一个私有变量就好了呀，为什么要使用ThreadLocal？

如果开发者希望将类的某个静态变量（user ID或者transaction ID）与线程状态关联，则可以考虑使用ThreadLocal。



简单说ThreadLocal就是一种以空间换时间的做法，在每个Thread里面维护了一个以开地址法实现的ThreadLocal.ThreadLocalMap，把数据进行隔离，数据不共享，自然就没有线程安全方面的问题了



**ThreadLocal原理**

ThreadLocal 内部维护的是⼀个类似 Map 的 ThreadLocalMap 数据结构， key 为当前对象的 Thread 对象，值为 Object 对象。

```
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

**ThreadLocal 内存泄露问题**

ThreadLocalMap 中使⽤的 key 为 ThreadLocal 的弱引⽤,⽽ value 是强引⽤。所以，如果 ThreadLocal 没有被外部强引⽤的情况下，在垃圾回收的时候，**key 会被清理掉，⽽ value 不会被 清理掉**。这样⼀来， ThreadLocalMap 中就会出现**key为null的Entry**。假如我们不做任何措施的话， value 永远⽆法被GC 回收，这个时候就可能会产⽣内存泄露。ThreadLocalMap实现中已经考虑了这种 情况，在调⽤ set() 、 get() 、 remove() ⽅法的时候，会清理掉 key 为 null 的记录。**使⽤完 ThreadLocal ⽅法后 最好⼿动调⽤ remove() ⽅法**

```
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





