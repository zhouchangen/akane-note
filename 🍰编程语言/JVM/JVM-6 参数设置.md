# JVM - 参数设置

## 如何设置JVM内存分配？

```
$ java -Xms128m -Xmx256m -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=256m -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/usr/local/xxxx.hprof" -jar xxx.jar
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/usr/local/xxxx.hprof -jar
-Xms1500m -Xmx1500m
-XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=512m
-XX:+PrintGCDetails
```



## 参数设置说明

- -标准参数，例如：-Xms
- -X非标准，例如：

- -XX不稳定参数，不同版本可能会不支持，例如：-XX:PermSize、-XX:MaxPermSize

```
注：-表示去掉不适用，+表示使用，这个+-指参数前面的+-，例如：+DoEscapeAnalysis、-DoEscapeAnalysis 
-XX:-EliminateAllocations  // 标量替换
-XX:-DoEscapeAnalysis      // 逃逸分析
-XX:-UseTLAB               // 线程专有对象分配  

-XX:+DoEscapeAnalysis
```



### 如何查看设置的参数

`java -XX:+PrintFlagsFinal -version | grep NewSize`



### 分配说明

例如：`java -Xms 3072M -Xmx3072M`

表示分配的最大最小堆的大小为3G。下面来看看各个分区的分配情况：

- Young和Old默认是1：2，因此Old占2/3，也就是2G；
- Young = Eden+s0+s1，因此Yound占1/3，也就是1G;
- Eden和s0，s1为8：1：1，因此Eden占8/10，也就是800M;
- s0和s1，因此占1/10, 都是100M。

**所以增大年轻代(-Xmn)后，将会减小年老代(Xmx-Xmn)大小。此值对系统性能影响较大。**



## jvm参数主要包括三种

- 堆和栈空间设置
- 垃圾收集器设置（包括并发gc）
- 辅助配置（统计跟踪、逃逸分析)



参考 <IntelliJ IDEA设置JVM运行参数 https://blog.csdn.net/sdujava2011/article/details/50086933>

<jvm参数详解 https://blog.csdn.net/kid_2412/article/details/52633739>



### 性能优化参数说明

| 参数                       |                                | 说明                                                         |
| -------------------------- | ------------------------------ | ------------------------------------------------------------ |
| -Xms                       | Java Heap初始值                | JVM最好将-Xms和-Xmx设为相同值（为了避免在运行时频繁调整Heap的大小） |
| -Xmx                       | Java Heap最大值                | 默认值为物理内存的1/4最佳设值应该视物理内存大小及计算机内其他内存开销而定 |
| -Xmn                       | Java Heap中Young区大小         | 堆内存 = 年轻代+年老代+元空间元空间一般固定大小为64m，**默认年轻代和年老代是1/3：2/3的关系。**所以增大年轻代后，将会减小**年老代**大小。此值对系统性能影响较大，Sun官方推荐配置为整个堆的3/8。 |
| -Xss                       | 每个线程的堆栈大小             |                                                              |
| -Xoss                      | 本地方法栈大小                 | 对hotspot无效                                                |
| XX:NewRatio                | Young年轻代与Old老年代的比例   | 例如：-XX:NewRatio=4，即年轻代：年老代=1：4                  |
| -XX:SurvivorRatio          | Eden与survivor的比例           | 例如：-XX:SurvivorRatio=4，即Eden:Survivor=4:2默认为8，即Eden:Survivor=8:2。也就是我们看JVM内存模型常看到的，Eden占新生代的8/10，From幸存区和To幸存区各占新生代的1/10 |
| -XX:MaxMetaspaceSize       | 最大元空间                     |                                                              |
| -XX:MaxTenuringThreshold   | 年轻代最大gc分代年龄           | 如果超过这个阈值会直接接入老年代，如果设置为0，年轻代不经过survivor空间直接进入老年代 |
| -XX:PretenureSizeThreshold | 设置大对象直接进入老年代的阈值 | 当大对象大小超过该值将会直接在老年代分配，默认值是0，意思是不管多大都是先在eden中分配内存 |
| -XX: TargetSurvivorRatio   | 对象动态年龄判断               | 当前放对象的Survivor区域里，一批对象的总大小大于这块Survivor区域内存大小的50%(-XX: TargetSurvivorRatio可以指定)，那么此时**大于等于**这批对象年龄最大值的对象，就可以直接进入老年代了。例如：Survivor区域里现有一批对象，年龄1+年龄2+年龄n的多个年龄对象总和超过了Survivor区域的50%,此时就会把年龄n(含以上)的对象都放入老年代。这个规则其实是希望那些可能是长期存活的对象，尽早进入老年代。**对象动态年龄判断机制一般是在minor gc之后触发的。** |

- -XX:+UseSerialGC：在新生代和老年代使用串休gc
- -XX:+UseParNewGc：在新生代使用并行gc
- -XX:+UseParallelOldGC：在老年代使用并行gc
- **-XX:ParallelGCThread：**设置Parallel gc的垃圾PretenureSizeThreshold回收线程数，通常与cpu数量相同
- **-XX:MaxGCPauseMillis：**设置最大垃圾收集停顿时间，垃圾回收器会尽量控制回收的时间在该值范围内
- -XX:GCPauseIntervalMillis：设置停顿时间间隔
- **-XX:GCTimeRatio：**设置吞吐量大小，0~100之间的整数。若该值为n，那么jvm将会花费不超过1/(1+n)的时间用于垃圾回收。
- **-XX:+UseAdaptiveSizePolicy：**开启自适应gc策略，jvm会根据运行时吞吐量等信息自动调整eden、old等空间大小以及晋升老年代年龄
- **-XX:SurvivorRatio=8** Eden与survivor的比例.默认为8，即Eden:Survivor=8:2**。**也就是我们看JVM内存模型常看到的，Eden占新生代的8/10，From幸存区和To幸存区各占新生代的1/10
- **-XX:PretenureSizeThreshold**   当大对象大小超过该值将会直接在老年代分配
- **-XX:MaxTenuringThreshold 年轻代最大gc分代年龄, 如果超过这个阈值会直接接入老年代，如果设置为0，年轻代不经过survivor空间直接进入老年代**
- -XX:+UseConcMarkSweepGC：新生代使用ParNew，老年代使用CMS和Serial，其中老年代的Serial用于作为CMS失败时调用的备选gc
- -XX:+ParallelCMSThreads：CMS线程数量
- -XX:CMSInitiatingOccupancyFraction：设置老年代空间被使用多少后触发CMS gc，默认为68%
- -XX:CMSFullGCsBeforeCompaction：设置多少次CMS回收后，进行一次内存压缩
- -XX:+CMSClassUnloadingEnabled：在类卸载后进行CMS回收
- -XX:+CMSParallelRemarkEnabled：启用并行重标记
- -XX:CMSInitiatingPermOccupancyFraction：当永久代空间被使用多少后触发CMS gc，百分比（在使用时CMSClassUnloadingEnabled必须被配置）UseCMSInitiatingOccupancyOnly：只有当gc达到配置的阈值时才进行回收
- XX:+CMSIncrementalMode：使用增量模式，适合单CPU
- XX:+UserG1GC：使用G1回收器，与G1相关的虚拟机参数都只能在jdk1.7以上使用
- XX:+UnlockExperimentalVMOptions：允许使用实验性参数



### 行为参数说明

| 参数及其默认值            | 描述                                                      |
| ------------------------- | --------------------------------------------------------- |
| -XX:-DisableExplicitGC    | 禁止调用System.gc()；但jvm的gc仍然有效                    |
| -XX:+MaxFDLimit           | 最大化文件描述符的数量限制                                |
| -XX:+ScavengeBeforeFullGC | 新生代GC优先于Full GC执行                                 |
| -XX:+UseGCOverheadLimit   | 在抛出OOM之前限制jvm耗费在GC上的时间比例                  |
| -XX:-UseConcMarkSweepGC   | 对老生代采用并发标记交换算法进行GC                        |
| -XX:-UseParallelGC        | 启用并行GC                                                |
| -XX:-UseParallelOldGC     | 对Full GC启用并行，当-XX:-UseParallelGC启用时该项自动启用 |
| -XX:-UseSerialGC          | 启用串行GC                                                |
| -XX:+UseThreadPriorities  | 启用本地线程优先级                                        |



### 调试参数说明

![image.png](H:/akane-note/🍰编程语言/JVM/images/java29.png)



## IDEA优化

设置合适的参数，让IDEA不再卡顿

```
# custom IntelliJ IDEA VM options

-Xms1024m
-Xmx1024m
-XX:ReservedCodeCacheSize=240m
-XX:+UseConcMarkSweepGC
-XX:SoftRefLRUPolicyMSPerMB=50
-ea
-Dsun.io.useCanonCaches=false
-Djava.net.preferIPv4Stack=true
-Djdk.http.auth.tunneling.disabledSchemes=""
-XX:+HeapDumpOnOutOfMemoryError
-XX:-OmitStackTraceInFastThrow
```

