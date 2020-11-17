# 2 Linux相关笔记



### 1 防火墙

```
systemctl start firewalld  # 开启防火墙
systemctl stop firewalld   # 关闭防火墙
systemctl status firewalld # 查看防火墙开启状态，显示running则是正在运行
firewall-cmd --reload      # 重启防火墙，永久打开端口需要reload一下

# 添加开启端口，--permanent表示永久打开，不加是临时打开重启之后失效
firewall-cmd --permanent --zone=public --add-port=8888/tcp

# 查看防火墙，添加的端口也可以看到
firewall-cmd --list-all
```

切换root

```
sudo su
```



### 2 Linux环境下如何查找哪个线程使用CPU最长

1. 获取项目的pid，jps或者ps -ef | grep java，这个前面有讲过
2. top -H -p pid，顺序不能改变



这样就可以打印出当前的项目，每条线程占用CPU时间的百分比。注意这里打出的是LWP，也就是操作系统原生线程的线程号。



使用"top -H -p pid"+"jps pid"可以很容易地找到某条占用CPU高的线程的线程堆栈，从而定位占用CPU高的原因，一般是因为不当的代码操作导致了死循环。



最后提一点，"top -H -p pid"打出来的LWP是十进制的，"jps pid"打出来的本地线程号是十六进制的，转换一下，就能定位到占用CPU高的线程的当前线程堆栈了。



### 3 查看最繁忙的线程

```
processid=$(top -bn 1 | grep java | head -n1 | awk '{ print $1}');threadid=$(top -Hbn 1 -p ${processid} | grep java | head -n1 | awk '{ print $1}' | xargs printf  '%x');sudo /usr/local/src/jdk1.8.0_152/bin/jstack $processid | grep "0x$threadid" -A10 --color;
```

