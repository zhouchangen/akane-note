# JVM - 入门



## 基础知识

JVM：Java Virtual Machine，Java虚拟机



class





### 字节码

工具：

- javap
- jclasslib Bytecode Viewer

https://plugins.jetbrains.com/plugin/9248-jclasslib-bytecode-viewer

可以用直接输入javap，查看可选参数

常用的是：javap -v *.class    （注：-v输出附加信息)

```txt
C:\Users\AkaneMurakawa>javap
用法: javap <options> <classes>
其中, 可能的选项包括:
  -help  --help  -?        输出此用法消息
  -version                 版本信息
  -v  -verbose             输出附加信息
  -l                       输出行号和本地变量表
  -public                  仅显示公共类和成员
  -protected               显示受保护的/公共类和成员
  -package                 显示程序包/受保护的/公共类
                           和成员 (默认)
  -p  -private             显示所有类和成员
  -c                       对代码进行反汇编
  -s                       输出内部类型签名
  -sysinfo                 显示正在处理的类的
                           系统信息 (路径, 大小, 日期, MD5 散列)
  -constants               显示最终常量
  -classpath <path>        指定查找用户类文件的位置
  -cp <path>               指定查找用户类文件的位置
  -bootclasspath <path>    覆盖引导类文件的位置
```



\1. 魔数**:** 确定这个⽂件是否为⼀个能被虚拟机接收的 Class ⽂件。

\2. **Class** ⽂件版本 ：Class ⽂件的版本号，保证编译正常执⾏。

\3. 常量池 ：常量池主要存放两⼤常量：字⾯量和符号引⽤。

\4. 访问标志 ：标志⽤于识别⼀些类或者接⼝层次的访问信息，包括：这个 Class 是类还是接⼝，

是否为 public 或者 abstract 类型，如果是类的话是否声明为 final 等等。

\5. 当前类索引**,**⽗类索引 ：类索引⽤于确定这个类的全限定名，⽗类索引⽤于确定这个类的⽗类的

全限定名，由于 Java 语⾔的单继承，所以⽗类索引只有⼀个，除了 java.lang.Object 之

外，所有的 java 类都有⽗类，因此除了 java.lang.Object 外，所有 Java 类的⽗类索引

都不为 0。

\6. 接⼝索引集合 ：接⼝索引集合⽤来描述这个类实现了那些接⼝，这些被实现的接⼝将

按 implents (如果这个类本身是接⼝的话则是 extends ) 后的接⼝顺序从左到右排列在接⼝索引集合中。

\7. 字段表集合 ：描述接⼝或类中声明的变量。字段包括类级变量以及实例变量，但不包括在⽅法

内部声明的局部变量。

\8. ⽅法表集合 ：类中的⽅法。

\9. 属性表集合 ： 在 Class ⽂件，字段表，⽅法表中都可以携带⾃⼰的属性表集合。

https://blog.csdn.net/weixin_43519048/article/details/104448697