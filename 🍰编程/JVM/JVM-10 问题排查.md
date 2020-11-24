# JVM - 问题排查

### 常用整理

```
watch
top -Hbn 1 | grep java
top -Hp
ps -ef | grep java
ps -aux
lscpu
free -h
free -m

df -lh
df -a
du -h


# 查看最繁忙的线程
processid=$(top -bn 1 | grep java | head -n1 | awk '{ print $1}');
threadid=$(top -Hbn 1 -p ${processid} | grep java | head -n1 | awk '{ print $1}' | xargs printf  '%x');
sudo $JAVA_HOME/bin/jstack $processid | grep "0x$threadid" -A100 --color;


# 查看最繁忙进程的GC情况
processid=$(top -bn 1 | grep java | head -n1 | awk '{print $1}');
sudo $JAVA_HOME/bin/jstat -gc $processid 3000 2;


# 查看最繁忙进程的heap情况
processid=$(top -bn 1 | grep java | head -n1 | awk '{print $1}');
sudo $JAVA_HOME/bin/jmap -heap $processid;


# 查看最繁忙进程的 对象情况
processid=$(top -bn 1 | grep java | head -n1 | awk '{print $1}');
sudo $JAVA_HOME/bin/jmap -histo $processid | head -50;

# 下载文件
jmap –dump:format=b,file=/tmp/xxx.hprof pid**
```

