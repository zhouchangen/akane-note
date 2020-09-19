# 4 SQL-Tips

## 1、重复数据保留一条

```
delete from goods_country
where (goods_no,country) in (
    select a.goods_no, a.country from (
    select goods_no, country from goods_country a group by goods_no, country  having count(*)>1
    ) a
)
and tid not in (
    select b.tid from (
        select min(tid) as tid from goods_country group by goods_no,country having count(*)>1
    ) b
);
```





## 2、存储过程，生成自增值

```
delimiter ;;
CREATE PROCEDURE proc_batch_insert()
BEGIN
DECLARE i BIGINT;
SET i= 20200481953;
WHILE i < 20200492953 DO

    INSERT INTO `test`.`xxxx` (`tid`) VALUES (i);
    SET i=i+1;
END WHILE;
END ;;
delimiter ;  
call proc_batch_insert();  
```



## 3、插入重复问题

```
insert ignore into table
```



## 4、统计每天单量



```
-- 日均单量 365天

select count(DISTINCT order_no)/365 AS 日均单量
from order_info
where create_time >= '2020-01-01'
and status='C'


-- 每天单量
select DATE_FORMAT(create_time,'%Y-%m-%d') 日期
  ,count(DISTINCT order_no) 每天单量
from order_info
where create_time >= '2020-01-01' and status='C'
group by DATE_FORMAT(create_time,'%Y-%m-%d')
```



## 5、GROUP_CONCAT

GROUP_CONCAT将组中的字符串连接成为具有各种选项的单个字符串



```
INSERT INTO t(v) VALUES('A'),('B'),('C'),('B');

+---------------------------------------------------------------------+
| GROUP_CONCAT(DISTINCT v
        ORDER BY v ASC
        SEPARATOR ';') |
+---------------------------------------------------------------------+
| A;B;C                                                               |
+---------------------------------------------------------------------+
1 row in set
```