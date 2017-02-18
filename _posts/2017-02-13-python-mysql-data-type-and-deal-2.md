---
layout: post
title: 'Python与MySQL数据类型及数据处理（二）'
tags: [IT]
description: >
  综合 MySQL 数据类型和 Python 数据类型 ，以及 MySQL 和 Python 这两种有代表性的数据库和语言 分别对数据的操作 总结为一下内容，其中 对于之前的 MySQL 数据类型和 Python 数据类型 文章 做一个总结 

  顺便重点带一下 Python 的内置数据类型里面没有的 `日期和时间` 这个数据类型

---



<!--more-->


# 数字

MySQL中 数字类型有两种

整形：int，tinyint，smallint，bigint  ，medium

浮点型： float，double，decimal

其中的一些问题我在之前的文章里面也有谈过

详情请见

[http://mingyueli.com/cn/2017/02/11/python-mysql-data-type-and-deal-1/](http://mingyueli.com/cn/2017/02/11/python-mysql-data-type-and-deal-1/)

但是 python 中 只有整数 `int` 和浮点数 `float` 的区别

python 的数字是无限扩展的（没有溢出的概念，也就是说可以无限大）在 python2 里面整数有 int 和 long ，但是 python3 里面已经统一了

```python
# python3

Python 3.5.2 (default, Jul  5 2016, 12:43:10) 
[GCC 5.4.0 20160609] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> a=1
>>> a
1
>>> type(a)
<class 'int'>
>>> a=2.1
>>> type(a)
<class 'float'>
>>> a=11111111111111111111111111111111111111111111
>>> type(a)
<class 'int'>
>>> 
```


```python
# python2

Python 2.7.12 (default, Nov 19 2016, 06:48:10) 
[GCC 5.4.0 20160609] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> a=11111111111111111111111
>>> type(a)
<type 'long'>
>>> a=1
>>> type(a)
<type 'int'>
>>> 

```

-------------

# 字符串类型

在 MySQL 里字符串有很多类型：

``char，vachar，text，tinytext，mediumtext，longtext，binary，varbinary，blob，tinyblob ，mediumblob，longblog``

每种类型占的字节大小存储的需求也有所不同不同

在python中所有字符串类型都是str（还可以通过下标来访问）

```python
In [6]: a= "my"

In [7]: type (a)
Out[7]: str
```
----------------

# 时间类型：

MySQL里面时间类型分为五种 ：date ， datetime ，  time  ， year  ， timestamp（表示的格式有所不同，所占的字节大小也不同）

python的内置数据类型里面没有日期和时间这个数据类型，但是可以通过  `datetime`  和 ` time ` 标准库模块来实现对日期时间的管理，下面我们来重点说一下python 实现时间和日期的标准库。

可以转换为 时间数组，时间戳，自定义格式的时间类型
&nbsp;

>> 下面就用python的标准库 `time` 将 字符串转换为时间戳或者指定的时间格式输出
&nbsp;

```python
In [1]: a = "2017-02-13 10:10:00"

In [2]: import time
   ...:

In [3]: timeArray = time.strptime(a,"%Y-%m-%d %H:%M:%S")     #转换为数组

In [4]: timeArray
Out[4]: time.struct_time(tm_year=2017, tm_mon=2, tm_mday=13, tm_hour=10, tm_min=
10, tm_sec=0, tm_wday=0, tm_yday=44, tm_isdst=-1)

In [5]: timestamp = int(time.mktime(timeArray))     #把数组形式转换为时间戳

In [6]: timestamp
Out[6]: 1486951800

In [7]: timeStyle1 = time.strftime("%Y-%m-%d %H:%M:%S", timeArray)     #把数组转换成指定的时间格式输出

In [8]: timeStyle1
Out[8]: '2017-02-13 10:10:00'

In [9]: timeStyle2 = time.strftime("%Y/%m/%d %H:%M:%S", timeArray)

In [10]: timeStyle2
Out[10]: '2017/02/13 10:10:00'

In [11]: timeStyle3 = time.strftime("%Y/%m/%d", timeArray)
    ...:

In [12]: timeStyle3
Out[12]: '2017/02/13'

In [13]: timeStyle4 = time.strftime(" %H:%M:%S", timeArray)

In [14]: timeStyle4
Out[14]: ' 10:10:00'

In [15]:

```
&nbsp;

>> 得到当前时间并且转换为指定的格式输出
&nbsp;

```python
In [17]: now = int(time.time())     #当前时间输出为时间戳格式

In [18]: now
Out[18]: 1486952084

In [19]: timeArray = time.localtime(now)     #时间戳转换成数组形式

In [20]: timeArray
Out[20]: time.struct_time(tm_year=2017, tm_mon=2, tm_mday=13, tm_hour=10, tm_min
=14, tm_sec=44, tm_wday=0, tm_yday=44, tm_isdst=0)

In [21]: timeStyle5 = time.strftime("%Y-%m-%d %H:%M:%S", timeArray)     # 由数组形式转换成指定时间格式输出

```

当前时间显示时间类型：
``time.strftime('%Y-%m-%d %H:%M:%S', time.localtime(time.time()))``
&nbsp

#### mysql 的日期函数

&nbsp;

```
mysql> select now();
+---------------------+
| now()               |
+---------------------+
| 2017-02-13 10:19:48 |
+---------------------+
1 row in set (0.00 sec)

mysql> select sysdate();
+---------------------+
| sysdate()           |
+---------------------+
| 2017-02-13 10:19:58 |
+---------------------+
1 row in set (0.00 sec)

mysql> select sysdate(),now(),sleep(5),now(),sysdate();
+---------------------+---------------------+----------+---------------------+--
-------------------+
| sysdate()           | now()               | sleep(5) | now()               | s
ysdate()           |
+---------------------+---------------------+----------+---------------------+--
-------------------+
| 2017-02-13 10:20:05 | 2017-02-13 10:20:05 |        0 | 2017-02-13 10:20:05 | 2
017-02-13 10:20:10 |
+---------------------+---------------------+----------+---------------------+--
-------------------+
1 row in set (5.00 sec)

mysql>

```
-------------

## 复合类型：

>> MySQL 还支持两种复合数据类型 ENUM 和 SET，它们扩展了 SQL 规范。虽然这两种类型在技术上是字符串类型，但是可以被视为不同的数据类型。

>> 两者的区别就是 ： 一个 ENUM 类型只允许从一个集合中取得一个值；而 SET 类型允许从一个集合中取得任意多个值。

-------------

### ``如何看 python 处理数据和 mysql 处理数据``

MySQL 用处主要是存储和查询数据，

Python 的用处主要是可以用来获取数据操作数据，比如说操作文件，可以从文件里面读出数据，对文件里面的数据进行处理，再比如说爬虫，从一堆网页中获取所需要的数据，分析数据和数据挖掘 ，比如说 pandas 可以将数据的格式转换成你想要的格式，比如说CSV  等等，进行一些数据的变换去更适合统计学或者是科学方面的研究。可以操作数据库，最后将想要的结果输出或者存储到数据库（支持关系型，非关系型）中。

**``简单来说 就是一个是数据的计算，一个是数据的存储``**

----------

