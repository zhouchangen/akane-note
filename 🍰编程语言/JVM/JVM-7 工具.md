## 4. JVM工具

在这里介绍一下JVM调优会使用的一些工具。



### 4.1 javap

java自带的反编译工具，命令：

```
javap -c xxx.class
```

从图中可以看到Code下面的数字就是程序计数器，用于了解Java是如何解释工作的

![image.png](H:/akane-note/🍰编程语言/JVM/images/java24.png)

其它：javac -verbose Test.class



### 4.2 Java平台的结构图

可以从图中看到javap就是我们是jdk自带的tool

![image.png](H:/akane-note/🍰编程语言/JVM/images/java25.png)





### 4.3 jvisualvm

jvisualvm：JDK自带的JVM工具

命令：

jvisualvm

![image.png](H:/akane-note/🍰编程语言/JVM/images/java26.png)

输入以上命令，则会自动打开该工具，从图中左侧栏目可以看到当前运行的一些线程。**应用程序-本地**

可能你打开的时候看不到图中Visual GC这个菜单

**这是因为Java VisualVM默认没有安装Visual GC插件，需要手动安装**

<[**Java程序性能分析工具Java VisualVM（Visual GC）—程序员必备利器**](https://www.cnblogs.com/linghu-java/p/5689227.html) https://www.cnblogs.com/linghu-java/p/5689227.html>

![image.png](H:/akane-note/🍰编程语言/JVM/images/java27.png)



这个工具可以查看一些内存和CPU信息，系统配置，当然从图中也可以了解JVM堆内存模型





### 4.4 MAT(Memory Analyzer Tool)

介绍：

MAT是Memory Analyzer tool的缩写。Eclipse开发的分析工具。

Eclipse的内存分析器是一种快速，功能丰富的Java堆分析工具，帮助你查找内存泄漏和减少内存消耗。

下载地址：https://www.eclipse.org/mat/downloads.php

基础配置：

安装目录下的MemoryAnalyzer.ini文件内容**Xmx配置要大于dump文件，否者无法对dump文件进行分析**

打开后，配置Windows ->  Preferences ->  Memory Analyzer -> General configuration for Memory Analyzer中的Keep unreachable objects勾选，如果不勾选的话，只会给你分析当前JVM栈用对象，Bytes display选择MB

更多介绍见我的笔记：<JVM内存溢出分析 http://note.youdao.com/noteshare?id=d54be24d4db0df59959933c5270cc963>





### 4.5 JDK自带工具

更多工具见java安装目录下的**/bin/**

例如：jvisualvm、jconsole、jps

**![image.png](H:/akane-note/🍰编程语言/JVM/images/java28.png)**



