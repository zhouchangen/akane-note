# application.properties

## boostrap与application

- boostrap.yml 系统级别，优先加载，常用于服务配置管理，例如consul、spring cloud config
- application.yml spring级别



## 常用配置

```
server.port= #服务端口
#tomcat启动的最大线程数
server.tomcat.max-connections=1000 
#tomcat最大链接数
server.tomcat.max-threads=500 
#tomcat连接超时设置
server.connection-timeout=30000s 

# 配置MySQL数据源
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/kaguya?serverTimezone=UTC&useUnicode=true&characterEncoding=UTF-8&useSSL=false&autoReconnect=true&rewriteBatchedStatements=TRUE&useAffectedRows=true&allowMultiQueries=true
spring.datasource.username=root
spring.datasource.password=kaguya
# 初始化大小，最小，最大
spring.datasource.druid.initialSize=10
spring.datasource.druid.minIdle=10
spring.datasource.druid.maxActive=40
# 配置获取连接等待超时的时间
spring.datasource.druid.maxWait=60000
# 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
spring.datasource.druid.timeBetweenEvictionRunsMillis=60000
# 配置一个连接在池中最小生存的时间，单位是毫秒
spring.datasource.druid.minEvictableIdleTimeMillis=300000
spring.datasource.druid.validationQuery=show status like '%Service_Status%'
# spring.datasource.druid.validationQuery=SELECT 1
spring.datasource.druid.testWhileIdle=true
spring.datasource.druid.testOnBorrow=false
spring.datasource.druid.testOnReturn=false
# 打开PSCache，并且指定每个连接上PSCache的大小
spring.datasource.druid.poolPreparedStatements=true
spring.datasource.druid.maxPoolPreparedStatementPerConnectionSize=35

#cas
#cas服务器地址
cas.server.url= 
#登录的过滤器
cas.server.loginAction.url=/ 
#客户端服务器地址
cas.client.url= 
cas.client.sys.type= 
#首页
cas.client.index.url= 
shiro.autho.permission.error.url=/ 

# thymeleaf
#是否开启缓存
spring.thymeleaf.cache=false 
#检查模板是否存在
spring.thymeleaf.check-template-location=true 
#内容类型
spring.thymeleaf.content-type=text/html 
#启动MVC Thymeleaf视图分辨率
spring.thymeleaf.enabled=true 
#编码
spring.thymeleaf.encoding=UTF-8  
#模板编码
spring.thymeleaf.mode=HTML5 
#url后缀类型
spring.thymeleaf.suffix=.html 

# redis
#Redis数据库索引（默认为0
spring.redis.database=0 
spring.redis.host= #host
spring.redis.port= #端口
# redis客户端：jedis
#最大空闲连接数
spring.redis.jedis.pool.max-idle=8 
#最小空闲连接数
spring.redis.jedis.pool.min-idle=0 
#最大连接数据库连接数
spring.redis.jedis.pool.max-active=8 
 #最大等待毫秒数
spring.redis.jedis.pool.max-wait=-1s

# redis客户端：lettuce
# SpringBoot2.x已经将jedis改成了lettuce，见spring-boot-starter-data-redis
#最大空闲连接数
spring.redis.lettuce.pool.max-idle=8 
#最小空闲连接数
spring.redis.lettuce.pool.min-idle=0
#最大连接数据库连接数
spring.redis.lettuce.pool.max-active=8 
#最大等待毫秒数
spring.redis.lettuce.pool.max-wait=-1s 
spring.session.store-type=redis


# redis客户端：jedis
spring.redis.cluster.max-redirects=6
spring.redis.cluster.nodes=127.0.0.1:6677
spring.redis.jedis.pool.max-active=80
spring.redis.jedis.pool.max-idle=30
spring.redis.jedis.pool.max-wait=2000s
spring.redis.jedis.pool.min-idle=10
#session集群使用redis
spring.session.store-type=redis

#mybatis
mybatis.mapper-locations=classpath:META-INF/mybatis/**/*Mapper.xml
#开启驼峰
mybatis.configuration.map-underscore-to-camel-case=true
mapper.mappers=
mapper.not-empty=false
mapper.identity=MYSQL
#pagehelper
pagehelper.helper-dialect=mysql
pagehelper.reasonable=true
pagehelper.support-methods-arguments=true
pagehelper.params=count=countSql


#rabbitmq
#支持多个
spring.rabbitmq.addresses=127.0.0.1:5672,128.0.0.1:5672 
spring.rabbitmq.virtual-host=
spring.rabbitmq.username=
spring.rabbitmq.password=
#指定心跳超时，0为不指定.
spring.rabbitmq.requested-heartbeat=20s 
#消息手动确认
spring.rabbitmq.listener.simple.acknowledge-mode=manual 
#每次从队列中取几个
spring.rabbitmq.listener.simple.prefetch=5 
#消费者的数量 出队
spring.rabbitmq.listener.simple.concurrency=10 
 #缓存中保持的Channel数量
spring.rabbitmq.cache.channel.size=100
```