+++
title = 'MySQL的基本操作'
date = 2024-09-10T16:55:14+08:00
categories = ["MySQL"]
tags = ["mysql","基础"]
+++

# MySQL的基本操作

1、查看所有的数据库
```mysql
show datebases;
```

2、创建自己的数据库

```mysql
create datebase 数据库名字;
```

3、使用自己的数据库

```mysql
use 数据库名字;
```

>说明：如果没有使用use语句，后面针对数据库的操作也没有加“数据名”的限定，那么会报“ERROR 1046 (3D000): No database selected”（没有选择数据库） 使用完use语句之后，如果接下来的SQL都是针对一个数据库操作的，那就不用重复use了，如果要针对另 一个数据库操作，那么要重新use。

4、查看某个库的所有表格

```mysql
show tables; #要求前面有use语句
show tables from 数据库名;
```

5、创建新的表格

```mysql
create table 表名称(
字段名 数据类型,
字段名 数据类型
);

```

6、查看一个表的数据

```mysql
select * from 数据库表名称;
```

7、添加一条记录

```mysql
nsert into 表名称 values(值列表);
```

8、删除表格

```mysql
drop table 表名称;
```

11、删除数据库

```mysql
drop database 数据库名;
```

