# JVM - 垃圾回收器

## 垃圾回收器分类

注：红线表示能组合

垃圾回收组合：

- Serial + Serial Old
- **PS+PO（默认）**
- ParNew + CMS

![image-20201121171018752](images\image-20201121171018752.png)

****



### 1、Serial GC

> a stop-the-world, copying collector which uses a single GC thread.

**串行单线程GC**，会STW。

- 单CPU效率最高
- jvm参数指定-client时默认的垃圾收集器

![image-20201121174902931](images\image-20201121174902931.png)



### 2、Serial Old GC

> a stop-the-world, mark-sweep-compact collector that uses a single GC thread.

同上，只不过用的是mark-sweep-compact算法



### 3、Parallel Scavenge GC

> a stop-the-world, copying collector which uses a multiple GC thread.

术语简称PS，默认。

并发多线程GC, 它关心的是**吞吐量**，用户代码time/(用户time+ GC time)；

![image-20201121175136813](images\image-20201121175136813.png)



### 4、Parallel Old GC

> a compacting collector that uses multiple GC threads.

术语简称PO

同上，只不过用的是整理算法



### 5、ParNew GC (Parallel New)

> a stop-the-world, copying collector which uses multiple GC thread.
>
> It differs from "Parallel Scavenge" in that it has enhancements that make it usable with CMS
>
> For example, "ParNew" does the synchronization needed so that it can run during the concurrent phases of CMS.

**串行多线程GC**，会STW

和Parallel Scavenge GC（PS）差不多，是PS的变种。只不过做了增强，为了更好的和CMS结合使用。



### 6、CMS（Concurrent Mark Sweep）

注：为了配合CMS，诞生了PN，CMS是1.4版本后期引入，CMS开启了并发回收的过程，但是CMS问题较多，因此目前没有任何一个JDK版本默认为CMS。



并发标记扫描GC，占用cpu，空间碎片，并发异常，

CMS收集器关注点是**响应时间，低延迟**，**关注的是用户体验**；



1、CMS（ **Concurrent Mark Sweep**）
阶段一：初始标记（CMS initial mark），会STW

阶段二：并发标记（CMS concurrent mark）

阶段三：重新标记（CMS remark），会STW

阶段四：并发清除（CMS concurrent sweep）



初始标记： 暂停所有的其他线程，并记录下直接与root相连的对象，速度很快 ；



并发标记： **同时开启GC和⽤户线程**，⽤⼀个闭包结构去记录可达对象。但在这个阶段结束，这

个闭包结构并不能保证包含当前所有的可达对象。因为⽤户线程可能会不断的更新引⽤域，所以

GC线程⽆法保证可达性分析的实时性。所以这个算法⾥会跟踪记录这些发⽣引⽤更新的地⽅。



重新标记： 重新标记阶段就是为了修正并发标记期间因为⽤户程序继续运⾏⽽导致标记产⽣变

动的那⼀部分对象的标记记录，这个阶段的停顿时间⼀般会⽐初始标记阶段的时间稍⻓，远远⽐

并发标记阶段时间短



并发清除： 开启⽤户线程，同时GC线程开始对为标记的区域做清扫。





### 7、G1(Garbage-First)

 并发和并行，分代，空间整理，可预测停顿)(G1为jdk1.7实验性gc，为java9默认的gc)；



### 8、ZGC(jdk11)

