+++
title = 'Mysql常用函数'
date = 2024-09-19T20:02:09+08:00
categories = ["MySQL"]
tags = ["mysql"]
+++
# Mysql常用函数

<img src="https://i.postimg.cc/cJQ332yQ/screenshot-12.png" /><br>

## **聚合函数**
| 函数                            |                                   功能 |
| ------                          |                                  ------|
|count                            |统计数量                                |
|max                              |最大值                                  |
|min                              |最小值                                  |
|avg                              |平均值                                  |
|sum                              | 求和                                   |


## **字符串函数**

| 函数                   | 功能                                                      |
| ------------------------ | -------------------------- |
| CONCAT(S1,S2,...Sn)      | 字符串拼接，将S1，S2，... Sn拼接成一个字符串              |
| LOWER(str)               | 将字符串str全部转为小写                                   |
| UPPER(str)               | 将字符串str全部转为大写                                   |
| LPAD(str,n,pad)          | 左填充，用字符串pad对str的左边进行填充，达到n个字符串长度 |
| RPAD(str,n,pad)          | 右填充，用字符串pad对str的右边进行填充，达到n个字符串长度 |
| TRIM(str)                | 去掉字符串头部和尾部的空格                                |
| SUBSTRING(str,start,len) | 返回从字符串str从start位置起的len个长度的字符串           |



## **数值函数**

| 函数                            |                                   功能 |
| ------                          |                                  ------|
| CEIL(x)                         |向上取整                                |
| FLOOR(x)                        |向下取整                                |
| MOD(x,y)                        |返回x/y的模                             |
| RAND()                          |返回0~1内的随机数                       |
|ROUND(x,y)                       |求参数x的四舍五入的值，保留y位小数       |

例题：<br>
通过数据库的函数，生成一个六位数的随机验证码。

``` mysql
 select lpad(round(rand()*1000000,0),6,'0')
```


## **日期函数**
| 函数                            |                                   功能 |
| ------                          |                                  ------|
|CURDATE()                        |返回当前日期                            |
|CURTIME()                        |返回当前时间                            |
|NOW()                            |返回当前日期和时间                      |
|YEAR(date)                       |获取指定date的年份                      |
|MONTH(date)                      |获取指定date的月份                      |
|DAY(date)                        |获取指定date的日期                      |
|DATE_ADD(date, INTERVAL exprtype)|返回一个日期/时间值加上一个时间间隔expr后的时间值|
|DATEDIFF(date1,date2)            |返回起始时间date1 和 结束时间date2之间的天数|

例题：<br>
查询所有员工的入职天数，并根据入职天数倒序排序

``` mysql
 select name, datediff(curdate(), entrydate) as 'entrydays' from emp order by
 entrydays desc;
```

## **流程函数**

| 函数                            |                                   功能 |
| ------                          |                                  ------|
|IF(value , t , f)                |如果value为true，则返回t，否则返回f      |
|IFNULL(value1 , value2)          |如果value1不为空，返回value1，否则返回value2|
|CASE WHEN [ val1 ] THEN [res1] ...ELSE [ default ] END| 如果val1为true，返回res1，... 否则返回default默认值|
|CASE [ expr ] WHEN [ val1 ] THEN[res1] ... ELSE [ default ] END|如果expr的值等于val1，返回res1，... 否则返回default默认值|
