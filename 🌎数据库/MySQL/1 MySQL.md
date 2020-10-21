# 1 MySQL

在这里列出MySQL中的基本知识，对于索引和事务是比较重要的内容，因此会单独一章说明。



## MySQL知识



### MySQL执行SQL过程

对于一条数据执行，并不是一个简单过程。了解执行SQL的生命周期，便于我们分析是哪一个过程导致了慢执行。

生命周期：

连接器 ——> 解析器  ——> 优化器  ——> 执行器



### 性能监控

当我们分析执行SQL时，有时想知道执行过程所花费的时间，cpu，io等，便可以用下面的命令。

```mysql
-- 开启profiling
mysql> set profiling=1;

-- 执行query
mysql> select * from T1;
mysql> select * from T2;

-- 展示最近的一次执行所花费的时间，status表示的不同的阶段
mysql> show profile;
+---------------+----------+
| Status        | Duration |
+---------------+----------+
| starting      | 0.000137 |
| freeing items | 4.7E-5   |
| cleaning up   | 2.4E-5   |
+---------------+----------+
3 rows in set


-- 当然也可以用以下命令查看从开启profiling所统计数据
mysql> show profiles;
+----------+------------+---------------------------------------+
| Query_ID | Duration   | Query                                 |
+----------+------------+---------------------------------------+
|        1 |   0.034193 | select * from iconsignment limit 10   |
|        2 | 0.00046575 | select * from iconsignment limit 10\G |
|        3 |   0.000418 | select * from iconsignment limit 10   |
|        4 |   0.000208 | \G

-- 也可以通过Query_ID指定要查询哪一次的
mysql> show profile for query 2

-- 还可以指定获取更多的属性属性
mysql> show profile cpu for query 2;
-- all表示查看所有的属性
mysql> show profile all for query 2;

```



**show PROCESSLIST**

使用show processlist查看连接的线程个数，来观察是否有大量线程处于不正常的状态或者其他不正常的特征



注：使用performance schema来更加容易的监控mysql，详细见[MySQL performance schema详解.md](MySQL performance schema详解.md)





**show status like 'Handler_read%'**

 查看服务器状态

- Handler_read_first：读取索引第一个条目的次数（说明进行了全表扫描）

- **Handler_read_key：通过index获取数据的次数**（较高，说明使用索引情况较多）
- Handler_read_last：读取索引最后一个条目的次数

- Handler_read_next：通过索引读取下一条数据的次数

- Handler_read_prev：通过索引读取上一条数据的次数（一般是使用了order by）

- Handler_read_rnd：从固定位置读取数据的次数 （很多时候表现为没有使用索引或者文件排序）

- Handler_read_rnd_next：从数据节点读取下一条数据的次数（常说明你的表索引不正确或写入的查询没有利用索引）




### 数据类型知识

#### BLOB 和 TEXT 

MySQL 把每个 BLOB 和 TEXT 值当作一个独立的对象处理。两者都是为了存储很大数据而设计的字符串类型，

- BLOB：采用二进制存储
- TEXT ：采用**字符方式**存储



#### datetime和timestamp

- datetime：**占用8个字节，与时区无关**，可保存到毫米
- timestamp：**占用4个字节，与时区有关**，精确到秒，采用整型存储。保存时间范围：1970-01-01到**2038-01-19**



#### int(11) 和varchar(255)

- int(11)，长度 无意义
- varchar(255)，长度 表现最大限制





## 1 联表

join的本质是嵌套循环，因此建议是**小表join大表**，因为如果有索引的话，小表查询速度更快，循环次数更少。



**Nested-Loop 与Block Nested-Loop**

文章：[Using join buffer (Block Nested Loop)](https://www.cnblogs.com/wqbin/p/12127711.html)

- Nested Loop Join(NLJ)算法
- Block Nested-Loop Join(BNL)算法，与NLJ区别在于多了join_buffer

> 举例来说：
>
> 外层循环的结果集是100行
>
> 1. 使用NLJ 算法需要扫描内部表100次。
> 2. 如果使用BNL算法，先把对Outer Loop表(外部表)每次读取的10行记录放到join buffer，然后在InnerLoop表(内部表)中**直接匹配这10行数据**，**内存循环就可以一次与这10行进行比较**, 这样只需要比较10次，对内部表的扫描减少了9/10。所以BNL算法就能够显著减少内层循环表扫描的次数。



**join_buffer**

```mysql
-- 默认262144 256k
show variables like '%join_buffer%';
```



[一张图看懂 SQL 的各种 join 用法](https://www.javazhiyin.com/32279.html)

- 内连接 INNER JOIN、JOIN
- 外连接 Left JOIN、Right JOIN
- 全连接 FULL JOIN
- 交叉连接 N*N CROSS JOIN
- 联合查询 UNION、UNION ALL



 **join on和where区别**

1、 on条件是在**生成临时表时**使用的条件，它不管on中的条件是否为真，都会返回左边表中的记录。

2、where条件是在临时表生成好后，再对临时表进行过滤的条件。这时已经没有left join的含义（必须返回左边表的记录）了，条件不为真的就全部过滤掉。













## 3 性能优化

[Mysql性能优化实践](https://www.javazhiyin.com/30033.html)





## 4 死锁

MySQL 行级锁、间隙锁gapLock 解决：用主键id删除



[Innodb_pool_size](https://www.cnblogs.com/wanbin/p/9530833.html)

覆盖索引， select 字段 不走filesort



## 5 打印死锁日志



MySQL

```mysql
show engine innodb status;
```



Java

```java
// catch异常
catch (org.springframework.dao.DeadlockLoserDataAccessException e) {
    // 日志获取 见下方Dao
    
    // 日志打印
    log.error("死锁：type:{},name:{},status:{}", map.get("Type"), map.get("Name"),map.get("Status").split("LATEST DETECTED DEADLOCK")[1].split("FILE I/O")[0]);
}

Dao
@Select("show engine innodb status")
Map<String,String> getCurrentDeadLockLog();
```





## 6 存储过程插入数据过慢

```mysql
set sync_bin=0;set innodb_flush_log_at_trx_commit=0
select GLOBAL STATUS like 'innodb_page_size'
```




## 7 MySQL悲观锁

```mysql
for update
```









## 8、SHOW PROFILES;

```MYSQL
set profiling =1 ;

show profiles;
show table like



```







