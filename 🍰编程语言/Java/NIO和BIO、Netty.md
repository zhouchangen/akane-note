# NIO和BIO、Netty

### BIO

- 阻塞式
- 面向流
- 线程多
- 性能差

Tomcat7.0是BIO



InputStream、OutputStream



### NIO

- 非阻塞式
- 面向管道(双向)
- 线程少
- 性能好

Tomcat8.0是NIO

![image.png](images/nio.png)



channel

多路复用选择器



Input Output

文件系统、网络服务、内存