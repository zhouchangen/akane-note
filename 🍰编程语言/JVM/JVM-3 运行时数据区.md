# JVM-运行时数据区和常用指令



## 面试题

```java
public class TestPlus {

    public static void main(String[] args) {
        int i = 1;
//        i = i++; // 1
        i = ++i; // 2
        System.out.println(i);
    }
}
```



## 运行时数据区

1. 程序计数器 Program Counter ： 存放指令位置
2. 堆Heap
3. 本地方法栈 native method stacks
4. 虚拟机栈 JVM stacks
5. 直接内存 Direct Memory (JVM可以直接访问的内核空间的内存，提高效率，实现零拷贝)
6. 方法区（**方法区是一种定义或概念。所谓的永久代或元空间是其一种实现机制**）

1.7：有永久代，但逐步去”永久代“，常量池在堆
1.8及之后：无永久代，字符串常量池在堆中，运行时常量池在元空间

运行常量池



### 栈

栈里面的是栈帧，栈帧包含以下内容：

1. 局部变量表
2. 操作数栈
3. 动态链接
4. 返回地址（或叫方法出口）

基于栈的指令集

基于寄存器的指令集

iload入栈

istore出栈

