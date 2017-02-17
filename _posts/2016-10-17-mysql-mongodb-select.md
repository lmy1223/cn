---
layout: post
title: 'MySQL与Mongo简单的查询 （一）'
tags: [IT]
description: >
  相信很多数据库方向的人都知道关系型数据库和菲关系型数据库的区别吧

  在这里我就略微的点一下

  关系型数据库，是指采用了关系模型来组织数据的数据库，由二维表及其之间的联系所组成的一个数据组织。

---


<!--more-->


但是再想想啊，虽然更方便，更易于维护，但是在海量数据的高并发与高效率的读写 等等 要求下，关系型数据库也就遇到了瓶颈，相对来说的一些标准也就限制了关系型数据库，于是逐渐诞生了一种

为 海量数据而生的 `NoSQL` 用于指代那些非关系型的，分布式的，且一般不保证遵循ACID原则的数据存储系统 。主要有 面向海量数据访问的文档型数据库，像 MongoDB 和 CouchDB 等，面向高性能并发读写的 key-value 数据库，代表有 Redis 和 Tokyo Cabinet 等，`NOSQL` 大多都是 面向可扩展性的分布式数据库，基本上都是基于 键值对的，类似于 json 格式的数据存储， 所以自然 不需要经过 SQL 层的解析，性能一遍都非常高。

为了更好地了解关系型数据库与菲关系型数据库的一些知识，我在 建表查询上面以 `MySQL` 和 `MongoDB` 作为两种数据库的代表简单的介绍一下 ，下面说一下具体的要求：

我想查询的内容是这样的：


>> 分数大于0且人名是bob或是jake的总分数 &nbsp; 平均分数 &nbsp; 最小分数 &nbsp; 最大分数 &nbsp; 计数

 &nbsp;

举这个实例来试试用 `MySQL` 和 `mongodb` 分别写一个查询

首先我们先做一些准备工作

建立MySQL的数据库表结构如下

```
CREATE TABLE `new_schema`.`demo` (
   `id` INT NOT NULL,
   `person` VARCHAR(45) NOT NULL,
   `score` VARCHAR(45) NOT NULL,
  PRIMARY KEY (`id`));
```

建完表之后我们来插入一些数据

```
INSERT INTO `new_schema`.`demo` (`id`, `person`, `score`) VALUES ('1', 'bob', '50');
INSERT INTO `new_schema`.`demo` (`id`, `person`, `score`) VALUES ('2', 'jake', '60');
INSERT INTO `new_schema`.`demo` (`id`, `person`, `score`) VALUES ('3', 'bob', '100');
INSERT INTO `new_schema`.`demo` (`id`, `person`, `score`) VALUES ('6', 'jake', '100');
INSERT INTO `new_schema`.`demo` (`id`, `person`, `score`) VALUES ('8', 'li', '100');
```

我截个图方便看一下结构

![](http://images2015.cnblogs.com/blog/912233/201610/912233-20161017123256451-1105757557.png)

 &nbsp;

好 &nbsp; &nbsp;&nbsp;&nbsp;接下来我们进入 `mongodb` 的准备工作

看一下建立的mongodb的集合里面文档的结构（基本跟MySQL一毛一样）在这里我就不写插入文档的具体过程了 （为了便看mongodb的显示我都用两种格式显示：一个是表格模块显示 &nbsp; 一个是文本模块显示）

&nbsp; 

>>这个是表格模块显示

&nbsp; 

![](http://images2015.cnblogs.com/blog/912233/201610/912233-20161017125503732-272290629.png)

&nbsp; 

>>这个是文本模块显示

&nbsp; 

```
/* 1 */
{
    "_id" : ObjectId("58043fa8e9a7804c05031e17"),
    "person" : "bob",
    "sorce" : 50
}

/* 2 */
{
    "_id" : ObjectId("58043fa8e9a7804c05031e18"),
    "person" : "bob",
    "sorce" : 100
}

/* 3 */
{
    "_id" : ObjectId("58043fa8e9a7804c05031e19"),
    "person" : "jake",
    "sorce" : 60
}

/* 4 */
{
    "_id" : ObjectId("58043fa8e9a7804c05031e1a"),
    "person" : "jake",
    "sorce" : 100
}

/* 5 */
{
    "_id" : ObjectId("58043fa8e9a7804c05031e1b"),
    "person" : "li",
    "sorce" : 100
}
```

&nbsp; 


----------


##开始进入正题

现在我想查的MySQL语句是这样的（分数大于0且人名是bob或是jake的总分数 &nbsp; 平均分数 &nbsp; 最小分数 &nbsp; 最大分数 &nbsp; 计数）

```
SELECT person, SUM(score), AVG(score), MIN(score), MAX(score), COUNT(*) 
    FROM demo 
        WHERE score > 0 AND person IN('bob','jake') 
            GROUP BY person;
```

下面开始用Mongo写出这个查询

　　首先想到的是**聚合框架**

 - 先用 `$match` 过滤 &nbsp; &nbsp;分数大于0且人名是bob或是jake

```
db.demo.aggregate(
{
    "$match":{
        "$and":[
            {"sorce":{"$gt":0}},
            {"person":{"$in":["bob","jake"]}}
            ]
        }
    }
```

得到这个结果

&nbsp; 

>>这个是表格模块显示的结果：

&nbsp; 

![](http://images2015.cnblogs.com/blog/912233/201610/912233-20161017124756951-917358905.png)

&nbsp; 

>>这个是文本模块显示的结果：

&nbsp; 

```
/* 1 */
{
    "_id" : ObjectId("58043fa8e9a7804c05031e17"),
    "person" : "bob",
    "sorce" : 50
}

/* 2 */
{
    "_id" : ObjectId("58043fa8e9a7804c05031e18"),
    "person" : "bob",
    "sorce" : 100
}

/* 3 */
{
    "_id" : ObjectId("58043fa8e9a7804c05031e19"),
    "person" : "jake",
    "sorce" : 60
}

/* 4 */
{
    "_id" : ObjectId("58043fa8e9a7804c05031e1a"),
    "person" : "jake",
    "sorce" : 100
}
```

然后想要分组并且显示最大  最小  总计  平均值  和计数值

 - 那么 `$group` 派上用场了：

```
db.demo.aggregate(
{
    "$match":{
        "$and":[
            {"sorce":{"$gt":0}},
            {"person":{"$in":["bob","jake"]}}
            ]
        }
    },
{
   "$group":{"_id":"$person",
       "sumSorce":{"$sum":"$sorce"},
       "avgSorce":{"$avg":"$sorce"},
       "lowsetSorce":{"$min":"$sorce"},
       "highestSorce":{"$max":"$sorce"},
       "count":{"$sum":1}} 
    }
)
```

得到的结果就是 &nbsp; &nbsp;&nbsp;分数大于0且人名是bob或是jake的总分数 &nbsp; 平均分数 &nbsp; 最小分数 &nbsp; 最大分数 &nbsp; 计数

&nbsp; 

>>结果的表格模块显示：

&nbsp; 

&nbsp;![](http://images2015.cnblogs.com/blog/912233/201610/912233-20161017125856951-1505138535.png)

&nbsp; 

>>结果的文本模块显示：

&nbsp; 

```
/* 1 */
{
    "_id" : "bob",
    "sumSorce" : 150,
    "avgSorce" : 75.0,
    "lowsetSorce" : 50,
    "highestSorce" : 100,
    "count" : 2.0
}

/* 2 */
{
    "_id" : "jake",
    "sumSorce" : 160,
    "avgSorce" : 80.0,
    "lowsetSorce" : 60,
    "highestSorce" : 100,
    "count" : 2.0
}
```
以上就是一个简单查询的例子


----------



