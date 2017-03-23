---
layout: post
comments: true
title: ' mongo 数据方案-备份 '
tags: [IT]
description: >
  
---


## 简介 

我在之前的文章中简单的介绍过 `MonoDB` --一个为海量数据为生的数据库

[http://mingyueli.com/cn/2016/10/17/mysql-mongodb-select/](http://mingyueli.com/cn/2016/10/17/mysql-mongodb-select/)

MongoDB 是一个基于分布式文件存储的数据库。由C++语言编写。旨在为WEB应用提供可扩展的高性能数据存储解决方案。

它的特点是高性能、易部署、易使用，存储数据非常方便。主要功能特性有：

*面向集合存储，易存储对象类型的数据。

*支持动态 查询 。

*支持完全索引，包含内部对象。

*使用高效的二进制数据存储，包括大型对象（如视频等）。

*自动处理碎片，以支持云计算层次的扩展性。

*支持 RUBY ， PYTHON ， JAVA ， C++ ， PHP ， C# 等多种语言。

*文件存储格式为BSON（一种JSON的扩展）。

*可通过 网络 访问。


## 备份（热备份）

在不停止服务的情况下 实现备份和恢复。

### mongodump
备份
` mongodump -h IP --port 端口 -u 用户名 -p 密码 -d 数据库 -o 文件存在路径`

```powershell

E:\mongodb\bin>mongodump -o D:\56\mongo\ -d lgb_new
2017-03-21T08:11:18.496+0800    writing lgb_new.twoNavigation to
2017-03-21T08:11:18.497+0800    writing lgb_new.picture to
2017-03-21T08:11:18.497+0800    writing lgb_new.article to
2017-03-21T08:11:18.497+0800    writing lgb_new.oneNavigation to
2017-03-21T08:11:18.498+0800    done dumping lgb_new.picture (10 documents)
2017-03-21T08:11:18.498+0800    done dumping lgb_new.twoNavigation (21 documents
)
2017-03-21T08:11:18.498+0800    writing lgb_new.image to
2017-03-21T08:11:18.499+0800    done dumping lgb_new.oneNavigation (7 documents)

2017-03-21T08:11:18.502+0800    done dumping lgb_new.image (2 documents)
2017-03-21T08:11:18.510+0800    done dumping lgb_new.article (91 documents)

E:\mongodb\bin>

```

### mongorestore
还原
` mongorestore -h IP --port 端口 -u 用户名 -p 密码 -d 数据库 --drop 文件存在路径`

--drop参数，有此参数，则表示，先删除所有的记录，然后恢复。如无此参数，则恢复备份时候的数据，备份之后新增加的数据依然存在

```powershell

E:\mongodb\bin>mongorestore -drop D:\56\mongo\lgb_new -d lgb_new

```

## 导出 & 导入

### 导出 mongoexport

`mongoexport -h IP --port 端口 -u 用户名 -p 密码 -d 数据库 -c 集合名 -f 字段 -q 条件导出 --csv -o 文件名`

-f    导出指字段，以字号分割，-f name,email,age导出name,email,age这三个字段
-q    可以根查询条件导出，-q '{ "uid" : "100" }' 导出uid为100的数据
--csv 表示导出的文件格式为csv的，这个比较有用，因为大部分的关系型数据库都是支持csv，在这里有共同点

```json

> db.testLogin.find()
{ "_id" : ObjectId("57e6661e56e28c00152d8723"), "name" : "lmy", "password" : "12
3456" }

```

```powershell

E:\mongodb\bin>mongoexport -d login -c testLogin --csv -f name,password -o D:\56
\mongo\login\testLogin.csv
2017-03-21T08:46:01.675+0800    csv flag is deprecated; please use --type=csv in
stead
2017-03-21T08:46:01.696+0800    connected to: localhost
2017-03-21T08:46:01.720+0800    exported 1 record

```

|1 | 2|
| :--: |:--:|
|name|	password|
|lmy	|123456|

### 导入 mongoimport 

` mongoimport -h IP --port 端口 -u 用户名 -p 密码 -d 数据库 -c 集合 --type 类型 --headerline --upsert --drop 文件名  `

--upsert 更新插入现有数据

```powershell

E:\mongodb\bin>mongoimport -d login -c testLogin --type csv --headerline --file
D:\56\mongo\login\testLogin.csv
2017-03-21T09:02:36.762+0800    connected to: localhost
2017-03-21T09:02:36.785+0800    imported 4 documents

```

|1 | 2|
| :--: |:--:|
|name|	password|
|lmy	|123456|
|aaa|	1|
|bbb|	2|
|ccc	|3|

```json

> db.testLogin.find()
{ "_id" : ObjectId("57e6661e56e28c00152d8723"), "name" : "lmy", "password" : "12
3456" }
{ "_id" : ObjectId("58d07baccf7c57263767b2f8"), "name" : "lmy", "password" : 123
456 }
{ "_id" : ObjectId("58d07baccf7c57263767b2f9"), "name" : "bbb", "password" : 2 }

{ "_id" : ObjectId("58d07baccf7c57263767b2fa"), "name" : "aaa", "password" : 1 }

{ "_id" : ObjectId("58d07baccf7c57263767b2fb"), "name" : "ccc", "password" : 3 }

```

当然是否为CSV文件都可以

#### mongodump / mongorestore 与mongoexport / mongoimport有什么区别

> mongodump/mongorestore 导入导出 BSON格式，二进制文件体积虽然小，但是可读性也小，BSON格式跨版本的数据迁移的时候有可能（情况特殊）会出现版本兼容性的错误，所以跨版本备份一般不使用 mongodump / mongorestore

> mongoexport / mongoimport 导入导出JSON格式，体积大，可读性强，但是只保留了数据的部分，索引包括在内的其他信息不会被保留



