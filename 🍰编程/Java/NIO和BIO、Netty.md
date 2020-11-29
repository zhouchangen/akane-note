# NIO和BIO、Netty

### BIO

- 阻塞式
- 面向流
- 线程多
- 性能差

Tomcat7.0是BIO

InputStream、OutputStream

![image-20201129013017809](images\image-20201129013017809.png)



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



![image-20201129013058293](images\image-20201129013058293.png)