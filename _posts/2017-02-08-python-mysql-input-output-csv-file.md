---
layout: post
title: 'Python操作MySQL写入读出CSV文件'
tags: [IT]
description: >
  前几日看到有人讨论说一个Python的面试题，题目是这样的：

  **有一个数据文件，是csv格式，大约1T数据，要导入到MySQL，要求正确高效。**

  csv数据格式这个样子的

---



<!--more-->

```
[root@iZ28b4rx8dxZ ~]# cat data.csv 
id,name,sex,age
1,张三,女,18
2,李四,男,19
```

正好最近要写再测试数据库的脚本，虽然两者不搭边，总归都是操作数据库的，正好测试的时候就用python连了

首先  Python 数据库接口支持很多数据库，什么关系型的像 mysql 啊、PG（PostgreSQL） 啊、SQL Server、Oracle，非关系型的像 MongoDB 啊，Redis 啊， Hbase 等等，

这里就以MySQL数据库为例，毕竟我要用的MySQL嘛，而且面试题也是，当然操作其他数据库道理也都一样，从一个 csv 文件中读入数据，插入到数据库中，再将数据库中的数据读出，保存到另一个csv文件。


#介绍

主要定义两个对象，一个用于管理连接的 Connection（count） 了，另一个是用于执行查询的 Cursor （cur）对象。

&nbsp;

##Python 操作数据库的大致思路

* 导入模块

* 连接数据库

* 执行查询返回结果

&nbsp;

##步骤：

* 导入数据库模块 `import MySQLdb`

* 连接数据库 `connect` ，返回一个 `conn` 对象

* 通过该对象的 `cursor()` 成员函数返回一个 `cur` 对象

* 通过 `cur` 对象的 `execute()` 方法执行SQL语句

* 关闭 `cur` 和 `conn`对象 


----------


#把csv中的数据读出来放到插入到mysql表

对于mysql 数据库，需要安装第三方模块Mysql-python 。安装完以后，在程序中导入模块即可。
 
库名：test
表名：student
 
>> csv和数据库表的结构都是这样的

| id   | name | sex  | age |
| :--: | :--: | :--: | :--: |
| 1  | 张三 | 女    | 18 |
| 2  | 李四 | 男    | 20 |
| 3  | 王二 | 男    | 19 |
 
 
首先要导入MySQLdb模块先安装MySQLdb
 
`pip install MySQLdb`
 
```python

#!/usr/bin/python
# -*- coding: UTF-8 -*-
# This is a script that writes data from CSV to MySQL

import csv
import MySQLdb


def main():
    # 连接数据库
    conn = MySQLdb.connect(
        host='localhost',
        port=3306,
        user='root',
        passwd='123',
        db='test',
    )
    cur = conn.cursor()

    # 创建数据表
    cur.execute("DROP TABLE IF EXISTS `student`")
    conn.commit()
    create_db = """create table student(
                    id int,
                    name varchar(10),
                    sex char(4),
                    age int
                    )
                   ENGINE=InnoDB DEFAULT CHARSET=utf8"""
    cur.execute(create_db)    #执行创建表语句
    conn.commit()

    # 把 CSV 读到数组里
    f = open("/var/python_code/input_CSV_file.csv", 'r')
    student = []
    for row in csv.reader(f):
        student.append(row)
    f.close()

    # 还可以替换成为with
    # student = []
    # with open("/var/python_code/input_CSV_file.csv", 'r') as f:
    #     for row in csv.reader(f):
    #         student.append(row)

    # 执行 insert
    insert_db = "insert into student values(%s, %s, %s, %s)"
    cur.executemany(insert_db, student)    #批量高效插入
    conn.commit()

    # 关闭连接
    if cur:
        cur.close()
    if conn:
        conn.close()


if __name__ == '__main__':
    main()
``` 
 
``不得不提一下``

>> `executemany()` 是对数据进行批量插入如果对效率有要求的时候 最合适不过的选择了,原理大概就是将数组中的元素一个个取出来然后一条条的执行，在这之前我曾尝试过用 execute()，当时是从文件里面读出一行写入到数据可一行，后来我仔细想了一下，如果数据量很大呢，就像面试题说的，如果 1T 的数据呢，估计得等到驴年马月，估计公司也会see by . 于是果断把代码删掉重写成：把文件里面的内容全部读出来写入到数组里，然后用 `executemany()` 执行批量插入了



----------


   
#执行查询语句，并将结果输出到CSV文件里

```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-
# This is a script that writes data from MySQL to csv

import csv
import MySQLdb


def main():
    # 连接数据库
    conn = MySQLdb.connect(
        host='localhost',
        port=3306,
        user='root',
        passwd='123',
        db='test',
    )
    cur = conn.cursor()

    # 以写的方式打开 csv 文件并将内容写入到w
    f = open("/var/python_code/output_CSV_file.csv", 'w')
    write_file = csv.writer(f)

    # 从 student 表里面读出数据，写入到 csv 文件里
    cur.execute("select * from student")
    while True:
        row = cur.fetchone()    #获取下一个查询结果集为一个对象
        if not row:
            break
        write_file.writerow(row)    #csv模块方法一行一行写入
    f.close()

    # 关闭连接
    if cur:
        cur.close()
    if conn:
        conn.close()


if __name__ == '__main__':
    main()
```

----------


