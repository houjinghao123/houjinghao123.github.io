+++
title = 'Mysql事务'
date = 2024-09-20T14:04:10+08:00
categories = ["MySQL"]
tags = ["mysql","数据库"]
+++

# 事务
事务 是一组操作的集合，它是一个不可分割的工作单位，事务会把所有的操作作为一个整体一起向系统提交或撤销操作请求，即这些操作要么同时成功，要么同时失败。

## 使用到的关键字

```mysql
set autocommit=0;
start transaction;
commit;
rollback;

savepoint  断点
commit to 断点
rollback to 断点
```

## 事务四大特性
- 原子性（Atomicity）：事务是不可分割的最小操作单元，要么全部成功，要么全部失败
- 一致性（Consistency）：事务完成时，必须使所有的数据都保持一致状态
- 隔离性（Isolation）：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环境下运行
- 持久性（Durability）：事务一旦提交或回滚，它对数据库中的数据改变就是永久的

## 并发事务问题
1） 脏读：一个事务读到另一个事务还没有提交的数据

<img src="https://i.postimg.cc/SKW9fz92/screenshot-13.png" />

比如B读取到了A未提交的数据。

2） 不可重复读：一个事务先后读取同一条记录，但两次读取的数据不同，称为不可重复读。

<img src="https://i.postimg.cc/2yXLSQsJ/screenshot-14.png" />

事务A两次读取同一条记录，但是读取到的数据却是不一样的。

3） 幻读：一个事务读取数据时，另外一个事务进行更新，导致第一个事务读取到了没有更新的数据

<img src="https://i.postimg.cc/25fby8tz/screenshot-15.png" />

> 不可重复读和幻读的区别
>
> 不可重复读是读取了其他事务更改的数据 ->update   <br>
> 幻读是读取了其他事务新增的数据 ->insert和delete

## 事务的隔离级别


为了解决并发事务所引发的问题，在数据库中引入了事务隔离级别。

数据库事务的隔离级别有4个，由低到高依次为Read uncommitted 、Read committed 、Repeatable read 、Serializable ，这四个级别可以逐个解决脏读 、不可重复读 、幻读 这几类问题。

`MySql默认REPEATABLE_READ`

| 隔离级别 | 脏读 | 不可重复读 | 幻读 |
|----------| ---  | ----------| ---- |
|Read uncommitted(读未提交) | Y | Y | Y |
|Read committed(读以提交) | N | Y | Y|
|Repeatable Read(可重复读) | N |N  |Y  |
|Serializable | N |N |N|

- 查看事务隔离级别
  ```mysql
  SELECT @@TRANSACTION_ISOLATION;
  ```
- 设置事务隔离级别
  ```mysql
  SET [ SESSION | GLOBAL ] TRANSACTION ISOLATION LEVEL { READ UNCOMMITTED |
  READ COMMITTED | REPEATABLE READ | SERIALIZABLE }
  ```