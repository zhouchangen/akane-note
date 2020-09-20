# 5 redis-note

## 介绍

GitHub地址： https://github.com/AkaneMurakawa/akane-note-collection



REmote DIctionary Server(Redis)



基于**版本6.0.3**的Redis笔记，阅读Redis的配置文件并翻译，见[redis.conf](./2 redis.conf中文翻译.md)。对Redis主流的应用进行实战演示。由于Redis配置文件中有一些知识晦涩难懂，并不是所有内容都进行了翻译，但是常用的配置都已翻译，只有少数的两三个模块未完全翻译。另外，翻译不正确的地方请PR。最后，会推荐一些我在学习Redis时，看到觉得不错的文章和教程。代码示例见[demo](https://www.yuque.com/mizu/akane-note/demo)，为了方便我已将示例配置文件放在[conf](https://www.yuque.com/mizu/akane-note/conf)。



说明: 不同版本之间存在差异，配置文件也会不同，实际以实践结果为主。



## Redis误区和问题

**Redis是单线程的？**

Redis是基于内存的。主要是单线程的，然而有些操作，例如UNLINK、 slow I/O处理或其他的时候，需要开始额外的线程(用于实现非阻塞)，具体原因请阅读`redis.conf`中的`THREADED I/O`模块。



**为什么Redis那么快？**

Redis是基于内存的，CPU并不是Redis性能瓶颈，Redis的瓶颈是机器的内存和网络带宽。因为是单线程(没有CPU上下文切换)，读写都在一个CPU上，导致Redis非常的快。



**缓存雪崩**

缓存雪崩是指在我们设置缓存时采用了**相同的过期时间**，导致缓存在某一时刻同时失效，请求全部转发到DB，DB瞬时压力过重雪崩。



解决方法：

将缓存失效时间分散，例如：在原有基础上加上随机值



**缓存穿透**

缓存穿透是指查询一个一定不存在的数据，由于缓存是不命中时被动写的，并且出于容错考虑，如果从存储层查不到数据则不写入缓存，这将导致这个不存在的数据每次请求都要到存储层去查询，失去了缓存的意义。在流量大时，可能DB就挂掉了，要是有人利用不存在的key频繁攻击我们的应用，这就是漏洞。



解决方法：

有很多种方法可以有效地解决缓存穿透问题，最常见的则是采用布隆过滤器，将所有可能存在的数据哈希到一个足够大的bitmap中，一个一定不存在的数据会被 这个bitmap拦截掉，从而避免了对底层存储系统的查询压力。另外也有一个更为简单粗暴的方法（我们采用的就是这种），如果一个查询返回的数据为空（不管是数 据不存在，还是系统故障），我们仍然把这个空结果进行缓存，但它的过期时间会很短，最长不超过五分钟。



其他方法：增强校验



布隆过滤器Bloom Filter

> 布隆过滤器的原理是，当一个元素被加入集合时，通过K个散列函数将这个元素映射成一个位数组中的K个点，把它们置为1。检索时，我们只要看看这些点是不是都是1就（大约）知道集合中有没有它了：如果这些点有任何一个0，则被检元素一定不在；如果都是1，则被检元素很可能在。这就是布隆过滤器的基本思想。



缺点：

1. 存在误判
2. 删除困难。一个放入容器的元素映射到bit数组的k个位置上是1，删除的时候不能简单的直接置为0，可能会影响其他元素的判断。可以采用Counting Bloom Filter



实现：Guava中的Bloom Filter



**缓存击穿**

对于一些设置了过期时间的key，如果这些key可能会在某些时间点被超高并发地访问，是一种非常“热点”的数据。这个时候，如果缓存失效了，就会直接访问数据库，就被击穿了。这个和缓存雪崩的区别在于这里针对某一key缓存，前者则是很多key。



解决方法：设置热点key不过期，或加上互斥锁。



互斥锁(mutex key)实现：



简单地来说，就是在缓存失效的时候（判断拿出来的值为空），不是立即去load db，而是先使用缓存工具的某些带成功操作返回值的操作（比如Redis的SETNX或者Memcache的ADD）去set一个mutex key，当操作返回成功时，再进行load db的操作并回设缓存；否则，就重试整个get缓存的方法。SETNX (SET if Not eXists)：不存在则设置，可以实现锁的效果。



**分布式锁**

场景：避免不同节点重复处理、避免数据被破坏

1. 必须设置超时时间，防止服务挂掉未释放锁
2. Redis 分布式锁不要用于较长时间的任务（如果在加锁和释放锁之间的逻辑执行得太长，以至于超出了锁的超时限制）



### Redis配置

Redis在配置文件中，将配置根据不同功能和模块进行划分，你可以按照关键字搜索阅读指定的模块，如下形式:



```
################################## INCLUDES ###################################

# Include one or more other config files here.  This is useful if you
# have a standard template that goes to all Redis servers but also need
# to customize a few per-server settings.  Include files can include
# other files, so use this wisely.
```



## Redis模块



- INCLUDES 引入配置文件
- MODULES 引入模块
- NETWORK 网络配置
- TLS/SSL 安全连接
- GENERAL 通常配置
- SNAPSHOTTING 快照(俗称的RDB)
- REPLICATION (主从)复制
- KEYS TRACKING KEYS跟踪
- SECURITY 安全
- CLIENTS 客户端
- MEMORY MANAGEMENT 内存管理
- LAZY FREEING 懒释放(阻塞和非阻塞)
- THREADED I/O 线程I/O
- APPEND ONLY MODE 追加模式(APPEND ONLY FILE俗称的AOF)
- LUA SCRIPTING LUA脚本
- REDIS CLUSTER Redis集群
- CLUSTER DOCKER/NAT support  DOCKER/NAT方式集群支持
- SLOW LOG 慢查询日志
- LATENCY MONITOR 延迟监视器
- EVENT NOTIFICATION 事件通知
- GOPHER SERVER 地图服务器
- ADVANCED CONFIG 高级配置
- ACTIVE DEFRAGMENTATION 激活碎片整理



注：在这里整理Redis模块，有助于我们可以根据关键字快速寻找某一块的配置。



## Redis实战示例



- 主从复制-读写分离
- 哨兵模式
- 集群



## 推荐阅读



- [Redis中文网](http://www.redis.cn/documentation.html)
- [Redis开发与运维](https://github.com/AkaneMurakawa/awesome-programming-books/blob/master/redis/Redis开发与运维.pdf)
- [菜鸟教程-Redis](https://www.runoob.com/redis/redis-tutorial.html)
- [狂神说Java-Redis最新超详细版教程通俗易懂](https://www.bilibili.com/video/BV1S54y1R7SB?p=1)
- [妈妈再也不担心我面试被Redis问得脸都绿了](https://www.cnblogs.com/wmyskxz/p/12568926.html#_label2_1)



## 后记

要想深入了解Redis，光是了解这些肯定是不够的。这里只是引个例子，当我们想学习一门知识的时候，如何去学习。在这里，主要的学习过程是：

1. 了解Redis是什么
2. Redis的使用，包括基本的命令和企业级的开发
3. 深入阅读Redis的配置，要想使用好Redis，那就要好好阅读其配置
4. 有能力，可以阅读Redis官网，那里有更详细的解释
5. 源码阅读，下载Redis的源码，我们可以看到Redis其实是C语言编写的，如果有兴趣，我们还可以看看Redis是如何实现