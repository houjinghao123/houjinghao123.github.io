+++
title = 'MySQL锁'
date = 2024-10-01T13:21:57+08:00
+++

# **MySQL锁**

## **1、概述**

如何保证数据并发访问的一致性、有效性是所有数据库必须解决的一个问题，锁冲突也是影响数据库并发访问性能的一个重要因素。从这个角度来说，锁对数据库而言显得尤其重要，也更加复杂。

MySQL中的锁，按照锁的粒度分，分为以下三类：
- 全局锁：锁定数据库的所有表。
- 表级锁：每次操作锁住整张表。
- 行级锁：每次操作锁住对应的行数据。

## **2、全局锁**

### **2.1介绍**

全局锁就是对整个数据库实例加锁，加锁后整个实例就处于只读状态，后续的DML的写语句，DDL语句，已经更新操作的事务提交语句都将被阻塞。

使用场景是做全库的逻辑备份，对所有的表进行锁定，从而获取一致性视图，保证数据的完整性。

在进行全库的逻辑备份时加锁是为了防止出现数据不一致问题，发生这种问题的原因如下：



假设在数据库中存在这样三张表: tb\_stock库存表，tb\_order订单表，tb\_orderlog订单日志表。

![image.png](https://darwin-controller-pro-01.oss-cn-hangzhou.aliyuncs.com/3a544566641d4a5ba81178cde8aabc20/image.png?Expires=3304408724&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=LsqnUL4E76lcaGQTm64IeBY7ra0%3D "")
1. 在进行数据备份时，先备份了tb\_stick库存表
2. 然后接下来，在业务系统中，执行了下单操作，扣减库存，生成订单（更新tb\_stock表，插入tb\_order表）。
3. 然后在执行备份tb\_order表的逻辑。
4. 业务中执行插入订单日志操作。
5. 最后，又备份了tb\_orderlog表。

此时备份出来的数据，是存在问题的。因为备份出来的数据，tb\_stock表与tb\_order表的数据不一致(有最新操作的订单信息,但是库存数没减)。



在对数据库加上全局锁后

![image.png](https://darwin-controller-pro-01.oss-cn-hangzhou.aliyuncs.com/0f89474285e6401a826c8252b8d240fc/image.png?Expires=3304409048&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=VBHW4Ev9hzj4GoSV3lFr0OknzH0%3D "")

对数据库进行进行逻辑备份之前，先对整个数据库加上全局锁，一旦加了全局锁之后，其他的DDL、DML全部都处于阻塞状态，但是可以执行DQL语句，也就是处于只读状态，而数据备份就是查询操作。那么数据在进行逻辑备份的过程中，数据库中的数据就是不会发生变化的，这样就保证了数据的一致性和完整性。



### **2.2语法**

1）加全局锁
````mysql
flush table with read lock;
````

2）数据备份
````mysql
mysqldump -uroot -p**** 要备份的数据库名 > 要备份到的地址/名字.sql
````

该方式时数据库自带的备份工具是直接在命令行窗口执行的



3）释放锁

````mysql
unlock tables;
````

![image.png](https://darwin-controller-pro.oss-cn-hangzhou.aliyuncs.com/7e08247e250b4e75a17427ea42c51823/image.png?Expires=3304410236&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=N%2FdXFFOp%2BAR9TIeVZoXAFG9APP8%3D "")



![image.png](https://darwin-controller-pro.oss-cn-hangzhou.aliyuncs.com/edfd9ca1a936453ab8fa995ac469417b/image.png?Expires=3304410284&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=Ytb2jWY5Ip%2F81Nq0Y6T8cpuzxy8%3D "")



### 2.3问题

数据库中加全局锁，是一个比较重的操作，存在以下问题：
- 如果在主库上备份，那么在备份期间都不能执行更新，业务基本上就得停摆。
- 如果在从库上备份，那么在备份期间从库不能执行主库同步过来的二进制日志（binlog），会导致主从延迟。



在InnoDB引擎中，我们可以在备份时加上参数--single-transaction参数来完成不加锁的一致性数据备份。
````powershell
mysqldump --single-transaction -uroot -p**** 要备份的数据库名 > 要备份到的地址/名字.sql
````



## 3表级锁

### **3.1介绍**

表级锁，每次操作锁住整张表。锁定粒度大，发生锁冲突的概率最高，并发度最低。应用在MyISAM、InnoDB、BDB等存储引擎中。



对于表级锁，主要分为以下三类
- 表锁
- 元数据锁(meta data lock,MDL)
- 意向锁



### **3.2表锁**

对于表锁，分为两类：
- 表共享读锁（read lock）
- 表独占写锁（write lock）



语法：
- 加锁：lock tables 表名...read/write。
- 释放锁：unlock tables /断开客户端连接。



特点 :
1. 读锁



![image.png](https://darwin-controller-pro-01.oss-cn-hangzhou.aliyuncs.com/83e441953f204565b25178e669ce9e5c/image.png?Expires=3304410787&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=haapP%2Fz2UuRwZPmmpphS8nD%2B2tE%3D "")



左侧为客户端一，对指定表加了读锁，不会影响右侧客户端二的读，但是会阻塞右侧客户端的写。

![image.png](https://darwin-controller-pro-01.oss-cn-hangzhou.aliyuncs.com/3dfe236357a44032907aab752042d9d7/image.png?Expires=3304414330&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=EMxdtmeLA2PuCqdAOpv4dL9jkmg%3D "")


2. 写锁



![image.png](https://darwin-controller-pro.oss-cn-hangzhou.aliyuncs.com/26329b4e9325401b837f385245e937a2/image.png?Expires=3304414715&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=8mA2CwOnVfTIUVGXG389MMSqB2Y%3D "")



左侧为客户端一，对指定表加了写锁，会阻塞右侧客户端的读和写。

![image.png](https://darwin-controller-pro.oss-cn-hangzhou.aliyuncs.com/bbc9dd32a371433fad208d20ea75b356/image.png?Expires=3304414930&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=ieB8VabOFNU4IghaXYnFFuH58U0%3D "")


> 读锁不会堵塞其他客户端的读，但是会堵塞写。
> 写锁即会堵塞其他客户端的读，又会堵塞其他客户端换的写。





### 3.3元数据锁

meta data lock,元数据锁，简写MDL。

MDL加锁过程是系统自动控制，无需显式使用，在访问一张表的时候会自动加上。MDL锁主要作用是维护表元数据的数据一致性，在表上有活动事务的时候，不可以对元数据进行写入操作。**为了避免DML与DDL冲突，保证读写的正确性。**

这里的元数据，大家可以简单理解为就是一张表的表结构。也就是说，某一张表涉及到未提交的事务时，是不能够修改这张表的表结构的。

在MySQL5.5中引入了MDL，当对一张表进行增删改查的时候，加MDL读锁(共享)；当对表结构进行变更操作的时候，加MDL写锁(排他)。

常见的SQL操作时，所添加的元数据锁：



| **对应SQL**                                  | **锁类型**                                 | **说明**                                           |
| -------------------------------------------- | ------------------------------------------ | -------------------------------------------------- |
| lock tables xxx read/write                   | SHARED\_READ\_ONLY/SHARED\_NO\_READ\_WRITE |                                                    |
| select 、select...lock in share mode         | SHARED\_READ                               | 与SHARED\_READ、SHARED\_WRITE兼容，与EXCLUSIVE互斥 |
| insert 、update、delete、select... forupdate | SHARED\_WRITE                              | 与SHARED\_READ、SHARED\_WRITE兼容，与EXCLUSIVE互斥 |
| alter table...                               | EXCLUSIVE                                  | 与其他的MDL都互斥                                  |

当执行SELECT、INSERT、UPDATE、DELETE等语句时，添加的是元数据共享锁（SHARED\_READ/SHARED\_WRITE），之间是兼容的。



当执行SELECT语句时，添加的是元数据共享锁（SHARED\_READ），会阻塞元数据排他锁（EXCLUSIVE），之间是互斥的。



我们可以通过下面的SQL,来查看数据库中的元数据锁的情况:
````mysql
select object_type,object_schema,object_name,lock_type,lock_duration from
performance_schema.metadata_locks ;
````

如果是5.7版本的数据库需要手动开启metadata\_locks锁记录(临时生效)
````mysql
update performance_schema.setup_instruments set enabled = 'YES',timed='YES' where name='wait/lock/metadata/sql/mdl';
````

永久生效在配置文件中加入以下内容
````mysql
# 开启监控元数据锁
performance-schema-instrument='wait/lock/metadata/sql/mdl=ON'
````





![image.png](https://darwin-controller-pro-01.oss-cn-hangzhou.aliyuncs.com/9463470a2f4f44a8994529b639aefe19/image.png?Expires=3304418366&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=qb%2F7UCLID%2FmTxTFeUekTiAsSCUQ%3D "")

当在不同的连接窗口上时EXCLUSIVE锁是和其他锁互斥的。如果在同一连接窗口则会强制提交事务。

### **3.4意向锁**

**1）介绍**

为了避免DML在执行时，加的行锁与表锁冲突，在InnoDB中引入了意向锁，使得表锁不用检查每行数据数据是否加锁，使用意向锁来减少表锁的检查。

假如没有意向锁，客户端一对表加了行锁后，客户端二如何给表加表锁呢，来通过示意图简单分析一下：

首先客户端一，开启一个事务，然后执行DML操作，在执行DML语句时，会对涉及到的行加行锁。

![image.png](https://darwin-controller-pro.oss-cn-hangzhou.aliyuncs.com/b329edafabbf46b5a6814c5bf4a0a2bf/image.png?Expires=3304475777&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=MbUHjn4uzns30RwIbHjnZT4tpeE%3D "")

当客户端二，想对这张表加表锁时，会检查当前表是否有对应的行锁，如果没有，则添加表锁，此时就会从第一行数据，检查到最后一行数据，效率较低。

![image.png](https://darwin-controller-pro-01.oss-cn-hangzhou.aliyuncs.com/b4b287240ecc4f50afe9e71d381ffe18/image.png?Expires=3304475804&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=T6ZFaly%2FQPZ%2Foat4MiUgg40cfXA%3D "")

有了意向锁之后 :
客户端一，在执行DML操作时，会对涉及的行加行锁，同时也会对该表加上意向锁。

![image.png](https://darwin-controller-pro-01.oss-cn-hangzhou.aliyuncs.com/92938a889a944f90b402e45412ec5404/image.png?Expires=3304475825&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=bPrAszhHH6anSJCVIHIEYqqR0hU%3D "")

而其他客户端，在对这张表加表锁的时候，会根据该表上所加的意向锁来判定是否可以成功加表锁，而不用逐行判断行锁情况了。

**2）分类**


- 意向共享锁（IS）:由语句select ... lock in share mode添加 。 与 表锁共享锁(read)兼容，与表锁排他锁(write)互斥。
- 意向排他锁 （IX）：由insert、update、delete、select...for update添加。与表锁共享锁和排他锁都互斥，意向锁之间不会互斥。
> 一旦事务提交了，意向共享锁、意向排他锁，都会自动释放。





可以通过以下SQL，查看意向锁及行锁的加锁情况：（在Mysql8.0中能正常使用）
````mysql
select object_schema,object_name,index_name,lock_type,lock_mode,lock_data from performance_schema.data_locks;
````







## **四、行级锁**

### **4.1介绍**

行级锁，每次操作锁住对应的行数据。锁定粒度最小，发生锁冲突的概率最低，并发度最高。应用在InnoDB存储引擎中。



InnoDB的数据是基于索引组织的，行锁是通过对索引上的索引项加锁来实现的，而不是对记录加的锁。对于行级锁，主要分为以下三类：


- 行锁（Record Lock）: 锁定单个行记录的锁，防止其他事务对此行进行update和delete。在RC、RR隔离级别下都支持。

![image.png](https://darwin-controller-pro.oss-cn-hangzhou.aliyuncs.com/419e627c869242c0931a2846247f0793/image.png?Expires=3304479490&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=vw23Mc8nrIkD5QD8nJfFJn8nUGA%3D "")
- 间隙锁（Gap Lock）：锁定索引记录间隙（不含该记录），确保索引记录间隙不变，防止其他事务在这个间隙进行insert，产生幻读。在RR隔离级别下都支持。



![image.png](https://darwin-controller-pro.oss-cn-hangzhou.aliyuncs.com/332f3535458e443dac68a36eecb2f3ba/image.png?Expires=3304479632&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=Uxxbqqm0aBXgHb19SDpYHq99gzQ%3D "")
- 临键锁（Next-Key Lock）：行锁和间隙锁的组合，同时锁住数据，并锁住数据前面的间隙Gap。在RR隔离级别下支持

![image.png](https://darwin-controller-pro-01.oss-cn-hangzhou.aliyuncs.com/25fb8594ef7843b9be206cd0c0ffe9e6/image.png?Expires=3304479752&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=gHU54AFiA7CBSHxPud%2FRaoS2tHA%3D "")



### **4.2、行锁**

1）介绍



InnoDB实现了以下两种类型的行锁：
- 共享锁（S）：允许一个事务去读一行，阻止其他事务获得相同数据集的排他锁。
- 排他锁（X）：允许获取排他锁的事务更新数据，阻止其他事务获得相同数据集的共享锁和排他锁。

|     | X    | IX   | S    | IS   |
| --- | ---- | ---- | ---- | ---- |
| X   | 互斥 | 互斥 | 互斥 | 互斥 |
| IX  | 互斥 | 兼容 | 互斥 | 兼容 |
| S   | 互斥 | 互斥 | 兼容 | 兼容 |
| IS  | 互斥 | 兼容 | 兼容 | 兼容 |



常见的SQL语句，在执行时，所加的行锁如下：

| SQL                           | 行锁类型   | 说明                                      |
| ----------------------------- | ---------- | ----------------------------------------- |
| INSERT ...                    | 排他锁     | 自动加锁                                  |
| UPDATE ...                    | 排他锁     | 自动加锁                                  |
| DELETE ...                    | 排他锁     | 自动加锁                                  |
| SELECT（正常）                | 不加任何锁 |                                           |
| SELECT ... LOCK IN SHARE MODE | 共享锁     | 需要手动在SELECT之后加LOCK IN SHARE  MODE |
| SELECT ... FOR UPDATE         | 排他锁     | 需要手动在SELECT之后加FOR UPDATE          |

2）演示
- 共享锁（S）

![image.png](https://darwin-controller-pro.oss-cn-hangzhou.aliyuncs.com/892befe3cf384030a1d58062f45f775b/image.png?Expires=3304558246&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=wO3uz2dbG%2BY7v8gtsQm3TirzqxE%3D "")

给id为1的加上共享锁后可以对数据进行查询，但是不能对数据进行更改。


- 排他锁（X）

![image.png](https://darwin-controller-pro.oss-cn-hangzhou.aliyuncs.com/c4d9be6e987b4dc1b2d0b19dde074a9d/image.png?Expires=3304558616&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=XCNRe9nVisE9ef19WBfYXPHGvYA%3D "")

给id=3的加上排他锁后可以对其他事务进行查询但是不能更改和在对它加其他的锁。



### **4.3、间隙锁**

只存在于可重复读隔离级别，目的是为了解决可重复读隔离级别下幻读的现象。

假设，表中有一个范围id为（3，5）间隙锁，那么其他事务就无法插入id=4这条记录了，这样就有效
的防止幻读现象的发生。

![image.png](https://darwin-controller-pro-01.oss-cn-hangzhou.aliyuncs.com/fda9579c3897457596608b319a44285a/image.png?Expires=3304559467&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=8nf%2Fj7kKIIBcv%2BvlXUTwtJWSgsk%3D "")

间隙锁虽然存在X型间隙锁和S型间隙锁，但是并没有什么区别，间隙锁之间是兼容的，即两个事务可以
同时持有包含共同间隙范围的间隙锁，并不存在互斥关系，因为间隙锁的目的是防止插入幻影记录而提出
的。

### 4.4、临键锁

Next-Key Lock 称为临键锁，是 Record Lock\+ Gap Lock 的组合，锁定一个范围，并且锁定记录本身。



假设，表中有一个范围 id 为（3，5\]的 next-key lock，那么其他事务即不能插入 id= 4 记录，也不能修
改id=5 这条记录。

![image.png](https://darwin-controller-pro.oss-cn-hangzhou.aliyuncs.com/dabafcf758aa4dd89bd6f10be8f7bf9d/image.png?Expires=3304559705&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=LLC9PZHPK88pGkZqy1syt5hF9ts%3D "")

所以，next-key lock 即能保护该记录，又能阻止其他事务将新纪录插入到被保护记录前面的间隙中。
next-keylock是包含间隙锁\+记录锁的，如果一个事务获取了×型的next-keylock，那么另外一个事务
在获取相同范围的×型的next-keylock时，是会被阻塞的。

`参考文档`

[MySQL数据库官方文档-InnoDB Locking](https://dev.mysql.com/doc/refman/5.7/en/innodb-locking.html)

