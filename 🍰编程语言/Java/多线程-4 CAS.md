# 多线程 - CAS



## 自旋锁

虽然用锁或 synchronized 关键字可以实现原子操作，但是加锁或使用 synchronized 关键字带来的性能损耗较大。

因此又出现了一种锁，叫做自旋锁，号称无锁优化 。



自旋锁：在循环里不断的尝试获取锁。



## CAS

自旋锁是一种概念，而CAS 是实现自旋锁的基础，CAS 利用 CPU 指令保证了操作的原子性，以达到锁的效果。CAS可以实现乐观锁，它实际上是直接利用了 CPU 层面的指令，所以性能很高。



CAS的全称是：比较并交换（Compare And Swap）。在CAS中，有这样三个值：

- V：要更新的变量(var)
- E：预期值(expected) 
- N：新值(new)



比较并交换的过程如下：

> cas(Value, Expected, NewValue)
>
> if V == E 
>
> ​	V= New
>
> otherwise try again or fail

判断V是否等于E，如果等于，将V的值设置为New；

如果不等，说明已经有其它线程更新了V。

这里的**预期值E本质上指的是“旧值”**。



那有没有可能在判断了`V == E `之后，正准备更新时，被其它线程更改了V的值呢？

答：不会的。因为CAS是一种原子操作，系统级别的。它是一种系统原语，是一条CPU的原子指令，从CPU层面保证它的原子性。





java.util.concurrent.atomic下乐观锁就是CAS的一种实现





- 



### 10.3 Java实现CAS - Unsafe类

前面提到，CAS是一种原子操作。那么Java是怎样来使用CAS的呢？我们知道，在Java中，如果一个方法是native的，那Java就不负责具体实现它，而是交给底层的JVM使用c或者c++去实现。

在Java中，有一个Unsafe类，它在sun.misc包中。它里面是一些native方法，其中就有几个关于CAS的：

```
boolean compareAndSwapObject(Object o, long offset,Object expected, Object x);
boolean compareAndSwapInt(Object o, long offset,int expected,int x);
boolean compareAndSwapLong(Object o, long offset,long expected,long x);
```



### 10.4 原子操作-AtomicInteger类源码简析

Atomic： 原子性



AtomicInteger 类主要利⽤ **CAS (compare and swap) + volatile 和 native** ⽅法来保证原⼦操作， 从⽽避免 synchronized 的⾼开销，执⾏效率⼤为提升。 CAS的原理是拿期望的值和原本的⼀个值作⽐᫾，如果相同则更新成新的值。UnSafe 类的 objectFieldOffset() ⽅法是⼀个本地⽅法，这个⽅法是⽤来拿到“原来的值”的内存地址，返回值是 valueOffset。另外 value 是⼀个volatile变量，在内存中可⻅，因此 JVM 可以保证任何时刻任何线 程总能拿到该变量的最新值。

```
public class AtomicInteger extends Number implements java.io.Serializable {
    private static final long serialVersionUID = 6214790243416807050L;

    // setup to use Unsafe.compareAndSwapInt for updates
    private static final Unsafe unsafe = Unsafe.getUnsafe();
    private static final long valueOffset;

    static {
        try {
            valueOffset = unsafe.objectFieldOffset
                (AtomicInteger.class.getDeclaredField("value"));
        } catch (Exception ex) { throw new Error(ex); }
    }

    private volatile int value;
```





## CAS三大问题

- ABA问题
- 循环时间长开销大
- 只能保证一个共享变量的原子操作





#### CAS缺点

- 。⽐如说⼀个线程one从内存位置V中取出A，这时候另⼀个线程two也从内存中取出A，并且two进⾏了⼀些操作变成了B，然 后two⼜将V位置的数据变成A，这时候线程one进⾏CAS操作发现内存中仍然是A，然后one操作成功。尽管线程one的CAS 操作成功，但可能存在潜藏的问题。从Java1.5开始JDK的atomic包⾥提供了⼀个类AtomicStampedReference来解决 ABA问题。
- 。CAS自旋的概率大，从而浪费更多的CPU资源
- 



### ABA问题

所谓ABA问题，就是一个值原来是A，变成了B，又变回了A。这个时候使用CAS是检查不出变化的，但实际上却被更新了两次。



### ABA问题解决

ABA问题的解决思路是在变量前面追加上**版本号或者时间戳**。（在MySQL中MVCC也有类似的思想。

> 例如：
>
> A 1.0
>
> B 2.0
>
> A 3.0

如果是基本数据类型，没什么问题，但是对象引用就可能出现问题





从JDK 1.5开始，JDK的atomic包里提供了一个类AtomicStampedReference（stamped：时间戳）类来解决ABA问题





这个类的compareAndSet方法的作用是首先检查**当前引用是否等于预期引用**，并且检查当前标志是否等于预期标志，如果二者都相等，才使用CAS设置为新的值和标志。







**循环时间长开销大**

CAS多与自旋结合。如果自旋CAS长时间不成功，会占用大量的CPU资源。

解决思路是让JVM支持处理器提供的**pause指令**。

pause指令能让自旋失败时cpu睡眠一小段时间再继续自旋，从而使得读操作的频率低很多,为解决内存顺序冲突而导致的CPU流水线重排的代价也会小很多。



**只能保证一个共享变量的原子操作**

这个问题你可能已经知道怎么解决了。有两种解决方案：

1. 使用JDK 1.5开始就提供的AtomicReference类保证对象之间的原子性，把多个变量放到一个对象里面进行CAS操作；
2. 使用锁。锁内的临界区代码可以保证只有当前线程能操作。



cas原理：

unsafe = C C++的指针

直接操作内存

allocateMemory putXX freeMemory pageSize

直接生成类的实例

allocateInstance

直接操作类或实例变量

objectFileldOffset

getInt

getObject



1.8 

```
compareAndSwapObject
```



