
+++
title = 'Mysql存储引擎'
date = 2024-09-21T14:04:10+08:00
categories = ["MySQL"]
tags = ["mysql","数据库"]
+++


# **一、Mysql存储引擎**

## **1.Mysql的体系结构**

![](https://i.postimg.cc/MpV3xV4W/screenshot-16.png)


1. 连接层
2. 服务层
3. 引擎层
4. 存储层



## **2.存储引擎介绍**

存储引擎就是存储数据、建立索引、更新/查询数据等技术的实现方式 。存储引擎是基于表的，而不是基于库的，`所以存储引擎也可被称为表类型` 。我们可以在创建表的时候，来指定选择的存储引擎，如果没有指定将自动选择默认的存储引擎(InnoDB)。

### **2.1InnoDB**

1).介绍

InnoDB是一种兼顾高可靠性和高性能的通用存储引擎，在 MySQL 5.5之后，InnoDB是默认的MySQL存储引擎。

2).特点

●DML操作遵循ACID模型，支持事务；

●行锁，提高并发访问性能；

●支持外键FOREIGN KEY约束，保证数据的完整性和正确性；

3).文件

参数：innodb\_file\_per\_table
````
show variables like 'innodb_file_per_table';
````

如果该参数开启，代表对于InnoDB引擎的表，每一张表都对应一个ibd文件。，存储该表的表结构（frm-早期的 、sdi-新版的）、数据和索引。
````
show variables like '%datadir%';
````

使用上面的命令查看自己表数据存储位置



4)\*逻辑存储结构\*

![](https://i.postimg.cc/XqkTkhMD/screenshot-17.png)


-  表空间: InnoDB存储引擎逻辑结构的最高层，ibd文件其实就是表空间文件，在表空间中可以包含多个Segment段。
- 段:表空间是由各个段组成的，常见的段有数据段、索引段、回滚段等。InnoDB中对于段的管理，都是引擎自身完成，不需要人为对其控制，一个段中包含多个区。
- 区:区是表空间的单元结构，每个区的大小为1M。默认情况下， InnoDB存储引擎页大小为16K，即一个区中一共有64个连续的页。
- 页:页是组成区的最小单元，`页也是InnoDB存储引擎磁盘管理的最小单元` ，每个页的大小默认为 16KB。为了保证页的连续性，InnoDB存储引擎每次从磁盘申请 4-5个区。
- 行: InnoDB存储引擎是面向行的，也就是说数据是按行进行存放的，在每一行中除了定义表时所指定的字段以外，还包含两个隐藏字段(后面会详细介绍)。

### **2.2 MyISAM（被）**

1).介绍

MyISAM是MySQL早期的默认存储引擎。

2).特点

不支持事务，不支持外键

支持表锁，不支持行锁

访问速度快



### **2.3Memory**

1).介绍

Memory引擎的表数据时存储在内存中的，由于受到硬件问题、或断电问题的影响，只能将这些表作为临时表或缓存使用。

2).特点

存放在内存中

hash索引（默认）



### **2.4三种索引的区别**

![](https://i.postimg.cc/s29W4960/screenshot-18.png)





# **二、索引**

## **索引概述**

索引（index）是帮助MySQL高效获取数据的数据结构(有序)。在数据之外，数据库系统还维护着满足特定查找算法的数据结构，这些数据结构以某种方式引用（指向）数据，这样就可以在这些数据结构上实现高级查找算法，这种数据结构就是索引。



## **无索引和有索引的情况下**

假如我们要执行的SQL语句为 ： select\* from user where age= 45;



在无索引的情况下会对全表进行扫描效率很低



![](https://i.postimg.cc/j5H8pFx6/screenshot-21.png)



在有索引时我们可以根据这个表建立索引，一般索引都是B\+tree，然后会根据age进行查找



![](https://i.postimg.cc/dVBk7RWs/screenshot-23.png)



| 优势                                 | 劣势               |
| ------------------------------------ | ------------------ |
| 提高数据检索的效率，降低数据库IO成本 | 索引列也要占用空间 |
| 通过索引\|列对数据进行排序，降低     |
、数据排序的成本，降低CPU的消
耗。 | 索引大大提高了查询效率，同时却也降低更新表的速度
如对表进行INSERT、UPDATE、DELETE时，效率降低 |

## **索引结构**

| **索引结构**        | 描述                                                                       |
| ------------------- | -------------------------------------------------------------------------- |
| B\+Tree索引         | 最常见的索引类型，大部分引擎都支持 B\+树索引                               |
| Hash索引            | 底层数据结构是用哈希表实现的,只有精确匹配索引列的查询才有效,不支持范围查询 |
| R-tree(空间索引）   | 空间索引\|是MyISAM引\|擎的一个特殊索引类型，主要用于地理空间数据类         |
| 型，通常使用较少    |
| Full-text(全文索引) | 是一种通过建立倒排索引，快速匹配文档的方式。类似于                         |
| Lucene, Solr,ES     |

上述是MySQL中所支持的所有的索引结构，接下来，我们再来看看不同的存储引擎对于索引结构的支持情况。



![](https://i.postimg.cc/PrvBKh99/screenshot-24.png)



## **索引分类**

在MySQL数据库，将索引的具体类型主要分为以下几类：主键索引、唯一索引、常规索引、全文索引。

### **索引**基础**分类**





| **分类** | **含义**                                             | **特点**                | **关键字** |
| -------- | ---------------------------------------------------- | ----------------------- | ---------- |
| 主键索引 | 针对于表中主键创建的索引                             | 默认自动创建,只能有一个 | PRIMARY    |
| 唯一索引 | 避免同一个表中某数据列中的值重复                     | 可以有多个              | UNIQUE     |
| 常规索引 | 快速定位特定数据                                     | 可以有多个              |            |
| 全文索引 | 全文索引查找的是文本中的关键词，而不是比较索引中的值 | 可以有多个              | FULLTEXT   |



### 聚集索引&二级索引

而在在InnoDB存储引擎中，根据索引的存储形式，又可以分为以下两种：

| **分类**                 | **含义**                                                   | **特点**            |
| ------------------------ | ---------------------------------------------------------- | ------------------- |
| 聚集索引(ClusteredIndex) | 将数据存储与索引放到了一块，索引结构的叶子节点保存了行数据 | 必须有,而且只有一个 |
| 二级索引(SecondaryIndex) | 将数据与索引分开存储，索引结构的叶子节点关                 | 可以存在多个        |

聚集索引选取规则:
- 如果存在主键，主键索引就是聚集索引。
- 如果不存在主键，将使用第一个唯一（UNIQUE）索引作为聚集索引。
- 如果表没有主键，或没有合适的唯一索引，则InnoDB会自动生成一个rowid作为隐藏的聚集索引。



接下来，我们来分析一下，当我们执行如下的SQL语句时，具体的查找过程是什么样子的。



![](https://i.postimg.cc/C1Bx49gN/screenshot-25.png)



具体过程如下:

①.由于是根据name字段进行查询，所以先根据name='Arm'到name字段的二级索引中进行匹配查找。但是在二级索引中只能查找到 Arm对应的主键值 10。

②.由于查询返回的数据是\*，所以此时，还需要根据主键值10，到聚集索引中查找10对应的记录，最终找到10对应的行row。

③.最终拿到这一行的数据，直接返回即可。


> 回表查询：   这种先到二级索引中查找数据，找到主键值,
> 然后再到聚集索引中根据主键值，获取数据的方式，就称之为回表查询
>



## **索引语法**

1）创建索引
````
CREATE [UNIQUE | FULLTEXT] INDEX index_name NO table_name(index_col_name,....);
````

2) 查看索引
````
SHOW INDEX FROM table_name;
````

3)删除索引
````
DROP INDEX index_name NO table_name;
````





# **三、SQL性能分析**

### **3.1SQL执行频率**

MySQL客户端连接成功后，通过 show\[session\|global\] status命令可以提供服务器状态信息。通过如下指令，可以查看当前数据库的INSERT、UPDATE、DELETE、SELECT的访问频次：
````
-- session 是查看当前会话；
-- global 是查询全局数据；
 SHOW GLOBAL STATUS LIKE 'COM_______';
````
> 通过上述指令，我们可以查看到当前数据库到底是以查询为主，还是以增删改为主，从而为数据库优化提供参考依据。如果是以增删改为主，我们可以考虑不对其进行索引的优化。如果是以查询为主，那么就要考虑对数据库的索引进行优化了。



### **3.2慢查询日志**

慢查询日志记录了所有执行时间超过指定参数（long\_query\_time，单位：秒，默认10秒）的所有SQL语句的日志。

MySQL的慢查询日志默认没有开启，我们可以查看一下系统变量 slow\_query\_log。
````
show variables like '%quer%';
````

#### **在windos下开启慢查询**

 临时开启慢查询（重启MySQL后就会失效）
````
set global slow_query_log='ON';

--设置慢查询日志存放的位置
set global slow_query_log_file='D:\\home\\mysql.log';
````

永久开启慢查询（mysql版本5.7.30）



找到mysql的安装目录，找到my.ini文件夹在\[mysqld\]处加入以下代码开启慢查询，永久有效。


````
#存储位置
datadir=D:/soft/mysql-5.30/Data
#开启慢查询
slow-query-log=1
#D:/soft/mysql-5.30/Data/HJH-slow.log 慢查询日志文件存储位置
slow_query_log_file="HJH-slow.log"
#慢查询判断时间
long_query_time=10
````

#### **在Ubuntu中开启慢查询**

//TODO

### **profile详情**

### show profiles能够在做SQL优化时帮助我们了解时间都耗费到哪里去了。通过have\_profiling参数，能够看到当前MySQL是否支持profile操作：


````
-- 查看数据库是否支持profile
SELECT @@have_profiling;

-- 开启profile
SET [session | global]profiling = 1;

````

开启profiling后就能使用profiling相关命令来查询执行过的SQL语句



执行一系列的业务SQL的操作，然后通过如下指令查看指令的执行耗时：
````
 -- 查看每一条SQL的耗时基本情况
show profiles;

-- 查看指定query_id的SQL语句各个阶段的耗时情况
show profile for query query_id;

-- 查看指定query_id的SQL语句CPU的使用情况
show profile cpu for query query_id;

````

### **explain**

EXPLAIN或者 DESC命令获取 MySQL如何执行 SELECT语句的信息，包括在 SELECT语句执行过程中表如何连接和连接的顺序。
````
-- 直接在select语句前加上关键字 explain/desc
EXPLAIN SELECT 字段列表 FROM 表明 WHERE 条件;
````



![](https://i.postimg.cc/s2dgvXWJ/screenshot-26.png)



Explain执行计划中各个字段的含义:

| **字段**      | 含义                                                                                                                                                                                                |
| ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| id            | select查询的序列号，表示查询中执行select子句或者是操作表的顺序(id相同，执行顺序从上到下；id不同，值越大，越先执行)。                                                                                |
| select\_type  | 表示 SELECT的类型，常见的取值有 SIMPLE（简单表，即不使用表连接或者子查询）、PRIMARY（主查询，即外层的查询）、UNION（UNION中的第二个或者后面的查询语句）、SUBQUERY（SELECT/WHERE之后包含了子查询）等 |
| type          | 表示连接类型，性能由好到差的连接类型为NULL、system、const、eq\_ref、ref、range、 index、all 。                                                                                                      |
| possible\_key | 显示可能应用在这张表上的索引，一个或多个。                                                                                                                                                          |
| key           | 实际使用的索引，如果为NULL，则没有使用索引。                                                                                                                                                        |
| key\_len      | 表示索引中使用的字节数， 该值为索引字段最大可能长度，并非实际使用长度，在不损失精确性的前提下，长度越短越好 。                                                                                      |
| rows          | MySQL认为必须要执行查询的行数，在innodb引擎的表中，是一个估计值，可能并不总是准确的。                                                                                                               |
| filtered      | 表示返回结果的行数占需读取行数的百分比， filtered的值越大越好。                                                                                                                                     |
| Extra         | Using where: Using Index 查找使用了索引，但是需要的数据都在索引列表中找到，所以不需要回表查询 。Using index condition:查找使用了索引，但是需要回表查询数据                                          |





# **五、索引的使用**

### **最左前缀法则**

使用联合索引时要遵守最左前缀法则。最左前缀法则是指在查询索引时从索引的最左列开始，并且不跳过索引中的列。如果跳过某一列，后面的字段索引失效。

tb\_user表
````
create table tb_user(
	id int primary key auto_increment comment '主键',
	name varchar(50) not null comment '用户名',
	phone varchar(11) not null comment '手机号',
	email varchar(100) comment '邮箱',
	profession varchar(11) comment '专业',
	age tinyint unsigned comment '年龄',
	gender char(1) comment '性别 , 1: 男, 2: 女',
	status char(1) comment '状态',
	createtime datetime comment '创建时间'
) comment '系统用户表';


INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('吕布', '17799990000', 'lvbu666@163.com', '软件工程', 23, '1', '6', '2001-02-02 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('曹操', '17799990001', 'caocao666@qq.com', '通讯工程', 33, '1', '0', '2001-03-05 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('赵云', '17799990002', '17799990@139.com', '英语', 34, '1', '2', '2002-03-02 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('孙悟空', '17799990003', '17799990@sina.com', '工程造价', 54, '1', '0', '2001-07-02 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('花木兰', '17799990004', '19980729@sina.com', '软件工程', 23, '2', '1', '2001-04-22 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('大乔', '17799990005', 'daqiao666@sina.com', '舞蹈', 22, '2', '0', '2001-02-07 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('露娜', '17799990006', 'luna_love@sina.com', '应用数学', 24, '2', '0', '2001-02-08 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('程咬金', '17799990007', 'chengyaojin@163.com', '化工', 38, '1', '5', '2001-05-23 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('项羽', '17799990008', 'xiaoyu666@qq.com', '金属材料', 43, '1', '0', '2001-09-18 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('白起', '17799990009', 'baiqi666@sina.com', '机械工程及其自动化', 27, '1', '2', '2001-08-16 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('韩信', '17799990010', 'hanxin520@163.com', '无机非金属材料工程', 27, '1', '0', '2001-06-12 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('荆轲', '17799990011', 'jingke123@163.com', '会计', 29, '1', '0', '2001-05-11 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('兰陵王', '17799990012', 'lanlinwang666@126.com', '工程造价', 44, '1', '1', '2001-04-09 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('狂铁', '17799990013', 'kuangtie@sina.com', '应用数学', 43, '1', '2', '2001-04-10 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('貂蝉', '17799990014', '84958948374@qq.com', '软件工程', 40, '2', '3', '2001-02-12 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('妲己', '17799990015', '2783238293@qq.com', '软件工程', 31, '2', '0', '2001-01-30 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('芈月', '17799990016', 'xiaomin2001@sina.com', '工业经济', 35, '2', '0', '2000-05-03 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('嬴政', '17799990017', '8839434342@qq.com', '化工', 38, '1', '1', '2001-08-08 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('狄仁杰', '17799990018', 'jujiamlm8166@163.com', '国际贸易', 30, '1', '0', '2007-03-12 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('安琪拉', '17799990019', 'jdodm1h@126.com', '城市规划', 51, '2', '0', '2001-08-15 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('典韦', '17799990020', 'ycaunanjian@163.com', '城市规划', 52, '1', '2', '2000-04-12 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('廉颇', '17799990021', 'lianpo321@126.com', '土木工程', 19, '1', '3', '2002-07-18 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('后羿', '17799990022', 'altycj2000@139.com', '城市园林', 20, '1', '0', '2002-03-10 00:00:00');
INSERT INTO itcast.tb_user (name, phone, email, profession, age, gender, status, createtime) VALUES ('姜子牙', '17799990023', '37483844@qq.com', '工程造价', 29, '1', '4', '2003-05-26 00:00:00');
````

在 tb\_user表中，有一个联合索引，这个联合索引涉及到三个字段，顺序分别为：profession， age，status。

对于最左前缀法则指的是，查询时，最左边的列，也就是profession必须存在，否则索引全部失效。



![image.png](https://darwin-controller-pro.oss-cn-hangzhou.aliyuncs.com/100d1612bf7b43158702ddc213d7c4c3/image.png?Expires=3304039820&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=68VccCIR1ThrhjMWwwtlHQOmCBI%3D "")

从图中可以看出只要有profession存在就会使用索引，当profession不存在即使使用后面的属性也不会触发索引。


> 思考题：
> 当执行SQL语句: explain select \* from tb\_user where age = 3 and
 status='0’ and profession='软件工程';时，是否满足最左前缀法则，走不走
上述的联合索引，索引长度？
>
> 很显然时走的，最左前缀法则中指的最最左边的列，是指在查询时，联合索引的最左边的字段（就是第一个字段）必须存在，与我们编写SQL时。条件的顺序无关。





### **范围查询**

联合索引中，出现范围查询(\>,\<)，范围查询右侧的列索引失效。



![image.png](https://darwin-controller-pro-01.oss-cn-hangzhou.aliyuncs.com/bfc44d1693c142169e8dd3159c7aa327/image.png?Expires=3304040474&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=uxT64YYgp%2BBmXG8VtQnQ09WyRhI%3D "")

在使用范围查询时有效字段是38，但是在不使用时有效字段是42，说明在使用范围查询时有字段失效了。

所以在业务允许的情况下，尽可能的使用类似的\>=或\<=这类的范围查询。





### **范围失效的情况**
- 索引列运算

不要在索引列上进行运算操作，索引将失效。
- 字符串不加引号

不要在索引列上进行运算操作，索引将失效。
- 模糊查询使用头部模糊查询，索引失效

如果仅仅是尾部模糊匹配，索引不会失效。如果是头部模糊匹配，索引失效。
- or连接条件

用or分割开的条件，如果or前的条件中的列有索引，而后面的列中没有索引，那么涉及的索引都不会被用到。

-数据分布影响。

如果MySQL评估使用索引比全表更慢，则不使用索引。



# **SQL提示**

SQL提示，是优化数据库的一个重要手段，简单来说，就是在SQL语句中加入一些人为的提示来达到优化操作的目的



1). use index ：建议MySQL使用哪一个索引完成此次查询（仅仅是建议，mysql内部还会再次进行评估）。
````
explain select * from tb_user use index(idx_user_pro) where profession='软件工程'
````



2). ignore index ：忽略指定的索引。


````
explain select * from tb_user ignore index(idx_user_pro) where profession='软件工程'
````

3). force index ：强制使用索引。
````
explain select * from tb_user ignore index(idx_user_pro) where profession='软件工程'
````





# **覆盖索引**

尽量使用覆盖索引，减少select\*。那么什么是覆盖索引呢？覆盖索引是指查询使用了索引，并且需要返回的列，在该索引中已经全部能够找到 。

![image.png](https://darwin-controller-pro-01.oss-cn-hangzhou.aliyuncs.com/0d08f6e6f0354782a294c0c3d63ee8ec/image.png?Expires=3304046761&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=gms41e%2BskUWdzEUC5dNO%2FMsWcf0%3D "")

B.执行SQL: select \* from tb\_user where id =2;

![image.png](https://darwin-controller-pro-01.oss-cn-hangzhou.aliyuncs.com/0c814530f97b418c88de7c3662dc3263/image.png?Expires=3304046885&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=2MwVPaXLQ%2F47PxaMcwnoSUB7Amw%3D "")

根据id查询，直接走聚集索引查询一次索引扫描，直接返回数据，性能高。



c.执行SQL: select id ,name from tb\_user where name ='Arm';

![image.png](https://darwin-controller-pro.oss-cn-hangzhou.aliyuncs.com/e426287c39a8440396f23cce94c1030d/image.png?Expires=3304047013&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=mU2rvvE5l4F7BrozXDMdAwtYzaw%3D "")

根据name字段查询辅助索引，id和name在name的二级索引中都是可以直接获取到的，所以不需要回表查询，性能高。



d.执行SQL:select id ,name ,gender from tb\_user where name ='Arm';

![image.png](https://darwin-controller-pro-01.oss-cn-hangzhou.aliyuncs.com/873185bb6aa44128b6d48843d0033115/image.png?Expires=3304047192&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=jN7utyLdFKad%2BQ9zhJFv8d%2FakPY%3D "")

由于在name的二级索引中，不包含gender，所以，需要两次索引扫描，也就是需要回表查询，性能相对较差一点。



# **前缀索引**

当字段类型为字符串（varchar，text，longtext等）时，有时候需要索引很长的字符串，这会让索引变得很大，查询时，浪费大量的磁盘IO，影响查询效率。此时可以只将字符串的一部分前缀，建立索引，这样可以大大节约索引空间，从而提高索引效率。



1).语法
````
create index idx_xxxx on table_name(colum(n));
````



示例

在tb\_user的表的email字段，建立长度未5的前缀索引。
````
create index idx_email_5 on tb_user(email(5));
````

![image.png](https://darwin-controller-pro.oss-cn-hangzhou.aliyuncs.com/bbbede15a4e44b50bfd1bb91d13f77b2/image.png?Expires=3304047585&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=J5WBvAAEGifmVVEIc5%2B3meJb3z8%3D "")



2)查询流程

![image.png](https://darwin-controller-pro.oss-cn-hangzhou.aliyuncs.com/60eb102ffb8447e1ade9098639487c33/image.png?Expires=3304047670&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=So1qHGMA54KPpwLKLDeUQtH3b2A%3D "")



# **单列索引和联合索引**

单列索引：即一个索引只包含单个列。

联合索引：即一个索引包含了多个列。



在业务场景中，如果存在多个查询条件，考虑针对于查询字段建立索引时，建议建立联合索引，而非单列索引。



![image.png](https://darwin-controller-pro.oss-cn-hangzhou.aliyuncs.com/3c76af6ef2b44d74826ecffdf44ed035/image.png?Expires=3304048885&OSSAccessKeyId=LTAI5tBVMtznbk7xyCa56gof&Signature=F%2F4QWnVhtiFlDa2vSKuenQKqg9I%3D "")



# **索引设计原则**

1).针对于数据量较大，且查询比较频繁的表建立索引。

2).针对于常作为查询条件（where）、排序（order by）、分组（group by）操作的字段建立索引。

3).尽量选择区分度高的列作为索引，尽量建立唯一索引，区分度越高，使用索引的效率越高。

4).如果是字符串类型的字段，字段的长度较长，可以针对于字段的特点，建立前缀索引。

5).尽量使用联合索引，减少单列索引，查询时，联合索引很多时候可以覆盖索引，节省存储空间，避免回表，提高查询效率。

6).要控制索引的数量，索引并不是多多益善，索引越多，维护索引结构的代价也就越大，会影响增删改的效率。

7).如果索引列不能存储NULL值，请在创建表时使用NOT NULL约束它。当优化器知道每列是否包含NULL值时，它可以更好地确定哪个索引最有效地用于查询。








