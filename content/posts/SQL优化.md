+++
title = 'SQL优化'
date = 2024-09-27T09:13:15+08:00
categories = ["MYSQL"]
tags= ["mysql","数据库"]
+++


# **SQL优化**

## **插入数据**

### **insert**

假设我们需要插入大量数据，那么有哪些方法呢？
- 批量插入数据
````
INSERT INTO tb_test values(1,'Tom'),(2,'Cat'),(3,'Jerry');
````
- 手动控制事务的提交
````
start transaction;
INSERT INTO tb_test values(1,'Tom'),(2,'Cat'),(3,'Jerry');
INSERT INTO tb_test values(4,'Tom'),(5,'Cat'),(6,'Jerry');

commit;
````

这两种方法只适用于插入数据在几千条到几万条的，当数据涉及到几十万或者几百万时MySQL也给我们提供了别的方法、
- load 指令
````
-- 客户端连接服务端时，加上参数 ---local-infile
mysql --local-infile -uroot -p

-- 设置全局参数local_infile为1，开启从本地加载文件导入数据的开关
set global local_infile =1;

-- 执行load指令将准备好的数据，加载到表结构中
load data local infile 'd:/data/test.log' into table tb_test fields terminated by ',' lines terminated by '\n';';

-- fields terminatedp-每个字段之间使用什么分隔
-- lines terminated-每条数据之间用什么分隔
````
> 主键按顺序插入性能要高于乱序插入：因为插入时需要根据主键进行排序生成索引，当使用乱序时会可能会导致用一页的数据产生分页的现象。



## **主键优化**



在上一小节，我们提到，主键顺序插入的性能是要高于乱序插入的。这一小节，就来介绍一下具体的原因，然后再分析一下主键又该如何设计。

### **1)数据组织方式**

在InnoDB存储引擎中，表数据都是根据主键顺序组织存放的，这种存储方式的表称为索引组织表(IOT)



![image.png](https://darwin-controller-pro.oss-cn-hangzhou.aliyuncs.com/eca96c6e6faa46e08cf80d55baddbc6c/image.png?Expires=3304070654&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=GoQzCKzzWQILHEcFGHiHzNSQOFw%3D "")

行数据都是存储在聚集索引的叶子节点上的，而InnoDB的逻辑结构是：表-\>段 -\>区 -\>块-\> 行



![image.png](https://darwin-controller-pro-01.oss-cn-hangzhou.aliyuncs.com/c17cdb5c0fee4bfdbbd6c72a490ec87b/image.png?Expires=3304070824&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=EiSHHSRXvtTHJKX%2Fj7wLQCNcfhA%3D "")



在InnoDB引擎中。数据行是记录在逻辑结构的page页中的，每一页的大小是16k，一个页中所存储的行也是有限的，如果插入的数据行row在该页存储不小，将会存储到下一个页中，页与页之间会通过指针连接。

### **页分裂**

页可以为空，也可以填充一半，也可以填充100%。每个页包含了2-N行数据(如果一行数据过大，会行溢出)，根据主键排列。

A. 主键顺序插入效果


1. 从磁盘中申请页，主键顺序插入

![image.png](https://darwin-controller-pro.oss-cn-hangzhou.aliyuncs.com/7167f87ff6144b5f9a66a694b3adfc4e/image.png?Expires=3304071099&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=%2B4bwN1sG2Z34Orkbb0EgDxInFI4%3D "")



B.主键乱序插入效果
1. 加入1#，2#页都已经写满了，存放了如图的数据



![image.png](https://darwin-controller-pro-01.oss-cn-hangzhou.aliyuncs.com/cd45f4142d5142569fda7ca1fc5fdd8c/image.png?Expires=3304071169&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=t9sMVjppvR9MoRmBVoCKv45XhaU%3D "")



2.此时在插入id为50的记录，会发生什么现象

会在开启一个页，写入新的页中？



![image.png](https://darwin-controller-pro.oss-cn-hangzhou.aliyuncs.com/f3db3ffddaa34778808f48270cc9112f/image.png?Expires=3304071289&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=sf0piRmFtWIEyV6dZ%2ByaUDX0ceE%3D "")

不会。因为，索引结构的叶子节点是有顺序的。按照顺序，应该存储在47之后。





![image.png](https://darwin-controller-pro.oss-cn-hangzhou.aliyuncs.com/ee35df3500984cb0b1f359eda772e526/image.png?Expires=3304071359&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=KZlKCyW11R168i1NJJxyfSAbUe8%3D "")



但是在47所在的1#页中已经写满了，存储不了50对应的数据了。那么此时会开辟一个新的页 3#。

![image.png](https://darwin-controller-pro-01.oss-cn-hangzhou.aliyuncs.com/8761781f0b394f02acbe285f5bda643e/image.png?Expires=3304071628&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=HiX0D5BMnT%2BM3u1kg8dX53%2Bp%2BCw%3D "")

但是不会直接将50存入3#页，而是会将1#页一半的数据移动到3#页没然后3#页，插入50。

![image.png](https://darwin-controller-pro.oss-cn-hangzhou.aliyuncs.com/a014d94de0d5433fb098cb0c6e643359/image.png?Expires=3304071724&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=bPHyZbNKyiHs%2BRB0b6iLTUhR5s4%3D "")

移动数据，并插入id为50的数据之后，那么此时，这三个页之间的数据顺序是有问题的。 1#的下一个页，应该是3#， 3#的下一个页是2#。所以，此时，需要重新设置链表指针。

![image.png](https://darwin-controller-pro-01.oss-cn-hangzhou.aliyuncs.com/bdeaee9f82cf4b459cb6a8eb71668e9c/image.png?Expires=3304071795&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=mgl%2FdAdAb3xwee3T4Wqi%2FFYsVW8%3D "")



上述的这种现象，称之为"页分裂"，是比较耗费性能的操作。

### **页合并**

当有1#,2#,3#三个页时，假设我们删除了2#页上的50%(MERGE\_THRESHOLD)的记录，那么InnoDB会开始开始寻找1#或3#(磁盘最近距离)，看看是否可以将两页合并以优化空间使用。
> MERGE\_THRESHOLD:合并页的阈值，可以自己设置，并在创建表或者创建索引时指定。





![image.png](https://darwin-controller-pro-01.oss-cn-hangzhou.aliyuncs.com/0433f426a2a54be8b059ea46229f1351/image.png?Expires=3304072329&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=%2Bnmwpa1adLu8NwTgcacc1m0FG4E%3D "")

### **索引设计原则**
- 满足业务需求的情况下，尽量降低主键的长度
- 插入数据时，尽量选择顺序插入，选择使用AUTO\_INCREMENT自增主键。
- 业务操作时，避免对主键的修改。
- 尽量不要使用UUID做主键或者是其他自然主键，如身份证号。
- 业务操作时，避免对主键的修改。

## **order by 优化**

MySQL的排序，有两种方式：
- Using filesort:通过表的索引或全表扫描，读取满足条件的数据行，然后在排序缓冲区sort buffer中完成排序操作，所有不是通过索引直接返回排序结果的排序都叫 FileSort排序。
- Using index:通过有序索引顺序扫描直接返回有序数据，这种情况即为 using index，不需要额外排序，操作效率高。

对于以上的两种排序方式，Using index的性能高，而Using filesort的性能低，我们在优化排序操作时，尽量要优化为 Using index。
> 可以使用explain 查看extra



 为了使排序也使用索引，就需要在创建索引时添加排序。默认也会添加排序方式为asc，也可以自己指定。


````
-- 创建索引
create index idx_user_age_phone on tb_user(age[asc | desc] ,phone[asc | desc])

````

根据age, phone进行降序一个升序，一个降序

![image.png](https://darwin-controller-pro-01.oss-cn-hangzhou.aliyuncs.com/c6026ad9ec8642b786f7c092d8cf8430/image.png?Expires=3304118571&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=dZBo3V6pbA5xAOUW2KLkckSBVd0%3D "")

因为创建索引时，如果未指定顺序，默认都是按照升序排序的，而查询时，一个升序，一个降序，此时就会出现Using filesort。

解决这种问题只需要在创建索引时一个使用desc，一个使用asc。



我们得出order by优化原则:

A.根据排序字段建立合适的索引，多字段排序时，也遵循最左前缀法则。

B.尽量使用覆盖索引。

C.多字段排序,一个升序一个降序，此时需要注意联合索引在创建时的规则（ASC/DESC）。

D.如果不可避免的出现filesort，大数据量排序时，可以适当增大排序缓冲区大小sort\_buffer\_size(默认256k)。



## **group by优化**

优化思路

A.在分组操作时，可以通过索引来提高效率。

B.分组操作时，索引的使用也是满足最左前缀法则的。

当进行分组查询时如果使用了联合索引那就要符合最左前缀法，如果不使用就会出现Using temporary。



## **limit优化**

分页查询，在查询时，越往后，分页查询效率越低。

优化思路

一般分页查询时，通过创建覆盖索引能够比较好地提高性能，可以通过覆盖索引加子查询形式进行优化。

未使用索引时

![image.png](https://darwin-controller-pro.oss-cn-hangzhou.aliyuncs.com/5a3a07467ee243589c0240eabfd92960/image.png?Expires=3304197243&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=CDLo2g5XiglFQUTfZ1pANGlhhhI%3D "")



使用索引时

![image.png](https://darwin-controller-pro.oss-cn-hangzhou.aliyuncs.com/3b09390904684a359e447d6e7fe3eae6/image.png?Expires=3304197230&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=t5GsKv6VYo6EENdg60aotDIToWM%3D "")



## **count优化**

### **优化方案**

在之前的测试中，我们发现，如果数据量很大，在执行count操作时，是非常耗时的。

而对于count的执行方式不同的引擎也有不同的方法。
- MyISAM 引擎把一个表的总行数存在了磁盘上，因此执行 count(\*)的时候会直接返回这个数，效率很高；但是如果是带条件的count，MyISAM也慢。
- InnoDB 引擎是它执行count(\*) 的时候，需要把数据一行一行的从引擎里面读取出来，然后积累计数。

优化思路：自己计数（可以借助redis这样的数据库进行，但是如果是带条件的count，还是麻烦）。

不同count使用方法的性能：



count(字段)\<count(主键)\<count(1)\|count(\*);

## **update优化**

开启事务后，在使用没有的索引的字段机进行update的处理时会产生表锁，

![image.png](https://darwin-controller-pro-01.oss-cn-hangzhou.aliyuncs.com/13c1661a34c244aeadcbb735915daf69/image.png?Expires=3304199454&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=p%2FUtWr7ECDQ1K47TnQ3n%2Bp77rOg%3D "")

![image.png](https://darwin-controller-pro-01.oss-cn-hangzhou.aliyuncs.com/95b2fcc4df5947008a13d864afc05f6b/image.png?Expires=3304199435&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=BHgeBBCsh9SUxOA2cDORHDn5ik4%3D "")



优化思路

因为InnoDB的行锁是针对索引加的行锁，不是针对记录加的锁，并且该索引不能失效，否则行锁会升级为表锁。

