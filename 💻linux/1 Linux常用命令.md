# 1 Linux常用命令

在这里对Linux常用命令整理，分几个方面：

1. 日志查询

https://www.linuxcool.com/?s=top



一般来说我们的命令都是这样的形式：

```
command [options] [arguments] 
说明：options选项，arguments参数
```



## 准备

第一步就是连接跳板机，可以用工具，例如：Xshell，也可以直接在web上去连接。



## 如何查看日志位置

如果你不知道日志存储的位置，在Java项目中，都会配置自己的日志存储路径，如果是用log4j2的话，你应该可以到对应的配置文件(log4j2.xml)，找到日志存储的位置。



## 常用的简单命令

#### **简单命令**

示例：

```
pwd：print working directory 查看自己所在的当前路径，当前目录

whereis：当然你也可以通过这个命令查找文件所在位置

clear：清除

history：查看命令历史，学习他人用到的命令
```



#### **切换目录**

https://wangchujiang.com/linux-command/c/cd.html

- cd：change directory 接着进入到对应的目录下，在这里可以使用**绝对路径 | 相对路径**
- 选项
  - **.：当前目录**
  - **..：上级目录**
  - **~：用户主目录**
  - **\- ：返回此目录之前的所在的目录**
  - **/ ：根目录**

示例：

```
cd /
cd ./logs/
cd ..
cd -
cd ~
```



#### **当前目录展示**

https://wangchujiang.com/linux-command/c/ls.html

- ls：list directory content 如果我们不知道当前目录下有什么，我们就可以用这个命令列出

- **选项：**
  - **-a ：隐藏的文件也会列出来**
  - **-l ：所有输出信息用单列的格式输出，而不是多列，一般我们用这个就看看文件的修改时间、大小**
  - **-t ：最近修改的文件显示在最上面，文件比较多的时候好用。**

示例：

```
ls -a
ll  注：等价于ls -l
ls -t

```





## Vim

linux上的编辑器，简单了解即可。

要想使用vi，首先就需要了解其工作模式：命令模式、编辑模式、末行模式

![image.png](images/vim.png)



基本上了解上面的一些就ok，另外就是如何编辑和退出。

当一开始进入的时候是**命令模式**，接着我们可以按下键盘上的**iao**任意一个，就会进入到编辑模式了。

编辑完后，我们可以看**ESC**回到命令模式，最后输入**：**进入到末行模式，也就是上图的**底线命令模式**，最后输入**wq!**保存退出即可。



### **常用操作**

注意：是末行模式、底线命令模式

- **:! 强制**
- **:w 保存**
- **:q 退出**
- **:q!强制退出**
- **:wq!**
- **:/搜索**



## 日志查询相关命令

上面的简单了解即可，接下来才是我们查询日志。在linux终端模式下并没有界面打开，因此我们第一个问题就是：如果查看文件内容？

上诉所讲的vi也是可以的，但是对我们来说使用太不友好了。下面就是我们用到的一些用于查看的命令。

在学习之前我们可以先想一下我们有什么需求：首先我们希望可以去搜索关键字，其次最好颜色也像上面一样可以标记颜色，最后可以看看查询内容的上下文。Ok，下面你的问题都将会得到解答。

### grep / zgrep / egrep

https://wangchujiang.com/linux-command/c/grep.html

grep：**G**lobal search **R**egular **E**xpression(RE) and **P**rint out the line

介绍：一个全面搜索表达式并把行打印出来的命令



命令：grep / zgrep / egrep

- grep：基本搜索 【常用】
- zgrep：压缩文件搜索，例如xxx-2020-11-23-1.log.gz【常用】
- egrep：正则表示式搜索， 等价于 grep -E 【常用】

示例：

```
grep "test" *.log
zgrep "test" xxx-2020-11-23-1.log.gz
egrep 'ing:[0-9]{4,}' *.log --color
```



#### **颜色标记**

-    --color=auto ：标记匹配颜色（能快速找到匹配的内容，高亮)【常用】

说明：不只是grep命令可以搭配，很多参数都可以，例如：tail、head、cat等

示例：

```
grep "test" *.log --color=auto
```



#### **ABC神器**

- -A 除了显示符合范本样式的那一行之外，并显示该行之后的内容【常用】
- -B 除了显示符合范本样式的那一行之外，并显示该行之前的内容【常用】
- -C 除了显示符合范本样式的那一行之外，并显示该行前后后的内容【常用】

- A：After后、B：Before前、C：前后

示例：有时候我们不仅是想看到符合的内容，**还想看到其上下文，这时候就得借助ABC选项了。**

```
grep "aes-256-gcm" shadowsocks.log -B2
grep "aes-256-gcm" shadowsocks.log -C2
grep "aes-256-gcm" shadowsocks.log -A2
```



#### **正则查询**

- -E ：使用正则表达式【常用】

示例：查询超时

```
cat /usr/local/logs/requestTime.log | grep -E "ing:[0-9]{4,}" -C 5
注：grep -E 等价于 egrep， 例如：egrep 'ing:[0-9]{4,}' *.log --color
```

 

#### **反转查找，致敬韦神**

- 输出除之外的所有行 **-v** 选项

示例：查询除了grep以外的行

```
grep -v "grep\|bash" 
```



### tail

介绍：一个查询文本命令，tail即尾部的意思，从尾部查找



#### **查看启动日志**

- -f 显示文件最新追加的内容【常用】

示例：在启动项目的时候，日志是一点点打印，通过这个参数-f就能一直查看追加的内容

````
tailf other.log 注：等价于tail -f other.log
````



#### **查看日志后500行**

- -n 输出文件的尾部N（N位数字）行内容 【常用】
- 其它
  - tail file ：(默认显示文件file的最后10行)
  - tail +20 file ：(显示文件file的内容，从第20行至文件末尾)
  - tail -c 10 file ：（显示文件file的最后10个字符）

示例：当报错误时，可以马上到服务器上查看日志，直接查看后面多少行内容基本上就是对应的报错信息了

```
tail -n500 info.log
```



### head

介绍：一个查询文本命令，head即头部的意思，从头部查找。与tail相对



#### **查看日志前500行**

- -n 输出文件的尾部N（N位数字）行内容 【常用】

示例：

```
head -n500 info.log
```



### cat/zcat

介绍：连接多个文件并打印到标准输出



### 管道

介绍：管道并不是命令，而是对于多个命令的连接

示例： 

```
grep "test" *.log | more
```



### more

https://wangchujiang.com/linux-command/c/more.html

介绍：按页显示文本内容



交互操作：

- H（获得帮助信息）
- **Enter（向下翻滚一行）**
- **空格（向下滚动一屏）**
- Q（退出命令）



### awk【特殊】

https://wangchujiang.com/linux-command/c/awk.html

介绍：awk是一种编程语言，一个强大的文本分析工具，，用于在linux/unix下对文本和数据进行处理。

 awk：命名取自三位创始人 Alfred Aho，Peter Weinberger, 和 Brian Kernighan 的 Family Name 的首字符



**常用命令选项**

- **-F fs**：fs指定输入分隔符，fs可以是字符串或正则表达式，如-F:
- **-v var=value**：赋值一个用户定义变量，将外部变量传递给awk
- **-f scripfile**：从脚本文件中读取awk命令
- **-m[fr] val**：对val值设置内在限制，-mf选项限制分配给val的最大块数目；-mr选项限制记录的最大数目。这两个功能是Bell实验室版awk的扩展功能，在标准awk中不适用。

```bash
[root@test:/root] awk
用法: awk [POSIX 或 GNU 风格选项] -f 脚本文件 [--] 文件 ...
用法: awk [POSIX 或 GNU 风格选项] [--] '程序' 文件 ...
POSIX 选项:                     GNU 长选项:
        -f 脚本文件             --file=脚本文件
        -F fs                   --field-separator=fs
        -v var=val              --assign=var=val
        -m[fr] val
        -O                      --optimize
        -W compat               --compat
        -W copyleft             --copyleft
        -W copyright            --copyright
        -W dump-variables[=file]        --dump-variables[=file]
        -W exec=file            --exec=file
        -W gen-po               --gen-po
        -W help                 --help
        -W lint[=fatal]         --lint[=fatal]
        -W lint-old             --lint-old
        -W non-decimal-data     --non-decimal-data
        -W profile[=file]       --profile[=file]
        -W posix                --posix
        -W re-interval          --re-interval
        -W source=program-text  --source=program-text
        -W traditional          --traditional
        -W usage                --usage
        -W use-lc-numeric       --use-lc-numeric
        -W version              --version

提交错误报告请参考“gawk.info”中的“Bugs”页，它位于打印版本中的“Reporting
Problems and Bugs”一节

翻译错误请发信至 translation-team-zh-cn@lists.sourceforge.net

gawk 是一个模式扫描及处理语言。缺省情况下它从标准输入读入并写至标准输出。

范例:
        gawk '{ sum += $1 }; END { print sum }' file
        gawk -F: '{ print $1 }' /etc/passwd
```



#### **语法结构**

```
awk 'BEGIN{ print "start" } pattern{ commands } END{ print "end" }' file
```

- **BEGIN语句块**：在awk开始从输入流中读取行 **之前** 被执行【可选】
- **END语句块**：在awk从输入流中读取完所有的行 **之后** 即被执行【可选】
- **pattern语句块**：awk读取的每一行都会执行该语句块【可选】

示例：

```
seq 5 | awk 'BEGIN{ sum=0; print "总和：" } { print $1"+"; sum+=$1 } END{ print "等于"; print sum }' 
总和：
1+
2+
3+
4+
5+
等于
15
```



#### **语法示例**

```
# 默认的字段定界符是空格
echo -e "1 2 3" | awk '{print $1,$2,$3}'
1 2 3


# 可以定义一些变量
echo | awk '{ var1="v1"; var2="v2"; var3="v3"; print var1,var2,var3; }' 
v1 v2 v3


# 支持运算符 = += -= *= /= %= ^= **=
awk 'BEGIN{a="b";print a++,++a;}'
0 2


# $0,$1, $2表示字段的位置
# 当然也支持从后访问，例如：$NF最后一个字段，$(NF-1)倒数第二个
# NR表示行号
echo -e "line1 f2 f3\nline2 f4 f5\nline3 f6 f7" | awk '{print "Line No:"NR", No of fields:"NF, "$0="$0, "$1="$1, "$2="$2, "$3="$3, "NF最后一个="$NF, "NF-1倒数第二个="$(NF-1), "NR行数="NR}'
Line No:1, No of fields:3 $0=line1 f2 f3 $1=line1 $2=f2 $3=f3 NF最后一个=f3 NF-1倒数第二个=f2 NR行数=1
Line No:2, No of fields:3 $0=line2 f4 f5 $1=line2 $2=f4 $3=f5 NF最后一个=f5 NF-1倒数第二个=f4 NR行数=2
Line No:3, No of fields:3 $0=line3 f6 f7 $1=line3 $2=f6 $3=f7 NF最后一个=f7 NF-1倒数第二个=f6 NR行数=3


# 支持正则，~：匹配正则表达式  !~：不匹配正则表达式。注意：
awk 'BEGIN{a = "abc"; if (a ~ /^a/){print "匹配成功";}}'
匹配成功


# 支持if-else、while、do-while、for
awk 'BEGIN{
test=100;
if(test>90){
  print "very good";
  }
  else if(test>60){
    print "good";
  }
  else{
    print "no pass";
  }
}'

very good
------------------------------------------------------------------------------------------------------------------------
awk 'BEGIN{
test=100;
total=0;
while(i<=test){
  total+=i;
  i++;
}
print total;
}'
5050
------------------------------------------------------------------------------------------------------------------------
awk 'BEGIN{
for(k in ENVIRON){
  print k"="ENVIRON[k];
}

}'
TERM=linux
G_BROKEN_FILENAMES=1
SHLVL=1
pwd=/root/text
...
logname=root
HOME=/root
SSH_CLIENT=192.168.1.21 53087 22



# 支持break，continue的操作，不过awk里是next
cat text.txt
a
b
c
d
e
awk 'NR%2==1{next}{print NR,$0;}' text.txt
2 b
4 d


# 支持将结果输入输出
echo | awk '{printf("hello word!n") > "datafile"}'
# 或
echo | awk '{printf("hello word!n") >> "datafile"}'


```



#### **小结**

awk是一种编程语言，拥有编程语言基本的特性，例如：变量、函数、循环、条件语句、正则、数组。



#### **实战**

##### **关闭服务**

```
ps -ef | grep 服务名 | grep -v "grep\|bash" | awk '{print $2}' | xargs kill -9
```

解读：

1. | 表示管道
2. ps -ef后面解释，grep找到对应的服务信息。
3. grep -v反转查找，这样就能到对应服务的所在的行。
4. 接着用awk进行分割，默认是按照空格分割，$2表示第二个字段。
5. xargs 一般是和管道一起使用，



示例：

```
awk '{print $2}'
grep "test" test.log | awk -F ' ' '{print $3}' # 按' '分割，输出第三个

注：awk -F ' ' '{print $3}' 等价于 awk '{print $3}'
```



#####  **查看最繁忙的线程**

```
processid=$(top -bn 1 | grep java | head -n1 | awk '{ print $1}');
threadid=$(top -Hbn 1 -p ${processid} | grep java | head -n1 | awk '{ print $1}' | xargs printf  '%x');
sudo /usr/local/src/jdk1.8.0_152/bin/jstack $processid | grep "0x$threadid" -A100 --color;

注意：sudo $JAVA_HOME/bin/jstack $processid | grep "0x$threadid" -A10 --color;
```

解读：

1. top -bn 1查看耗时高的进程（解释见top），grep找到java程序，head -n1取第一行，接着使用awk进行分割取第一个字段。结果用变量processid保存。
2. 后面top -Hbn 1 -p同理，找到对应的进程耗时高的子线程，并且以十六禁止打印，结果用变量threadid保存。
3. 最后用jstack命令查看



**注意：**

- 不加sudo会报**"Could not attach to "**，原因是：进程启动用户和自己jstat的用户不是同一个，使用sudo即可解决。
- 单独执行sudo会报：**"命令 `sudo` 是被禁止的 ..."**，但是上面的语句连接在一起执行就不会报，猜测是会当成脚本而不是单独的命令行。
- 如果配置了环境变量，可用$JAVA_HOME找到对应的java目录。例如：查看jdk内置的bin命令，`ls $JAVA_HOME/bin`
- 



##### **查看最繁忙进程的GC情况**

```
processid=$(top -bn 1 | grep java | head -n1 | awk '{print $1}');
sudo /usr/local/src/jdk1.8.0_152/bin/jstat -gc $processid 3000 2;

等价于（替换了$JAVA_HOME）
processid=$(top -bn 1 | grep java | head -n1 | awk '{print $1}');
sudo $JAVA_HOME/bin/jstat -gc $processid 3000 2;
```

同上，只不过这里用的jstat -gcutil命令，同理，还可以使用jmap、jinfo等。



## 服务相关命令

### 2.7 top

当然除了查看文件，我们可能还需要看一些进程





实时查看系统运行状态

- -b：以批处理模式操作
- -c：显示完整的治命令
- -d：屏幕刷新间隔时间
- -I：忽略失效过程
- -s：保密模式
- -S：累积模式
- -i<时间>：设置间隔时间
- -u<用户名>：指定用户名
- -p<进程号>：指定进程(必备)
- -n<次数>：循环显示的次数

在top命令执行过程中可以使用的一些交互命令。这些命令都是单字母的，如果在命令行中使用了-s选项， 其中一些命令可能会被屏蔽。

输入top，然后按对应的键即可。**Ctrl+C停止太low了，试试按q直接退出。**

- h：显示帮助画面，给出一些简短的命令总结说明
- k：终止一个进程
- i：忽略闲置和僵死进程，这是一个开关式命令
- q：退出程序
- r：重新安排一个进程的优先级别
- S：切换到累计模式
- s：改变两次刷新之间的延迟时间（单位为s），如果有小数，就换算成ms。输入0值则系统将不断刷新，默认值是5s
- f或者F：从当前显示中添加或者删除项目
- o或者O：改变显示项目的顺序
- l：切换显示平均负载和启动时间信息
- m：切换显示内存信息
- t：切换显示进程和CPU状态信息
- c：切换显示命令名称和完整命令行
- M：根据驻留内存大小进行排序
- P：根据CPU使用百分比大小进行排序
- T：根据时间/累计时间进行排序
- w：将当前设置写入~/.toprc文件中
- 1：数字1能够查看CPU信息，当然也可以看到是几核 【常用】



### 2.8 ps

- ps -aux是以BSD方式列出当前全部运行的进程
- ps -ef是以system v方式列出当前全部运行的进程 【常用】
- 上面两个命令虽然显示的都是当前全部运行进程，但是ps -aux列出的条目更多
- ps -axj 列出守护进程
- ps -l 列出ps中的进程操作命令及编号，例如kill等
- **ps -ef | grep init 查找当前运行进程中的的init进程** 【常用】



### 2.9 kill

用来删除执行中的程序或工作，生产上要是删除了，怕是要跑路了？

- -a：当处理当前进程时，不限制命令名和进程号的对应关系；
- -l <信息编号>：若不加<信息编号>选项，则-l参数会列出全部的信息名称；
- -p：指定kill 命令只打印相关进程的进程号，而不发送任何信号；
- -s <信息名称或编号>：指定要送出的信息；
- -u：指定用户。
- -9：表示强制杀死该进程

### 2.10 上传下载文件

有时候你也可能需要下载文件，可以用下面两个命令。

- rz 上传文件命令
- sz 下载文件到本地



### 2.11 查看系统存储情况

1. df命令可以显示目前所有文件系统的可用空间及使用情形
2. du：查询文件或文件夹的磁盘使用空间

```
df -h 
du -h --max-depth=1 ./
```





### 2.13 ping

将ICMP ECHO_REQUEST数据包发送到网络主机



### 2.14 curl



### 2.15 traceroute

traceroute跟踪从IP网络获取到给定主机的**路由信息包**。它利用IP协议的生存时间（TTL）字段并尝试从每个网关到主机的路径引发ICMP TIME_EXCEEDED响应。



**2.16 wget**

GNU Wget是一个免费实用程序，用于从Web非交互式下载文件。它支持HTTP，HTTPS和FTP协议，以及通过HTTP代理进行检索。



### 2.17 free

free显示系统中可用和可用的物理内存和交换内存的总量，以及内核使用的缓冲区和高速缓存。



## 3. 总结

上面的命令只是帮助我们查询，一般来说，我们去查询的时候，先定位一下是哪一个服务器上出了问题，连接上对应服务器，找到对应的日志存储位置。

**接着通过唯一的id去查找，找到对应的id相关信息之后，通过该id所在的线程id去查找。**

日志的打印格式(**Pattern**)可以在log4j2.xml配置文件下找

```
2019-08-31 00:00:00.414 ERROR [SimpleAsyncTaskExecutor-1] 1e1111c8-1bf4-4e2c-af43-db0bda2ba85c 后面的没有列出来

日期——线程名——线程ID
```



**在这里要看是异步线程，还是同步线程，还是多线程，不然你可能通过该线程id找不到对应的日志错误信息。如果没有线程id，那就只能通过线程名称了**

其次，如果是mq的消息，你可以先找到mq消费的**messageId**，接着通过该messageId找到更多的信息。



log4j2.xml日志文件大小达到一定的数量，你就只能到压缩文件查看了，用zgrep命令。不然你可能一直很奇怪为什么查不到信息。

**坑：当遇到一个错误信息的时候，你可以看看代码，然后通过版本控制器看看是不是最近有人改了代码，然后找出来暴打问一顿。（笑）**



**补充：还可以根据方法的名称去搜索error.log信息**





为了控制滚屏，可以按Ctrl+S键，停止滚屏；按Ctrl+Q键可以恢复滚屏。按Ctrl+C（中断）键可以终止该命令的执行，并且返回Shell提示符状态。

