---
layout: post
title: 'MySQL 左连接出现重复记录的问题'
tags: [jekyll]
description: >
  今天遇到了一个 在 MySQL 左连接时 出现重复记录的问题
  下面就是这个问题的说明

  举个例子两个表主表为A  从表为B

---

>A       

|aid |	place|
|:--:|:---:|
|1	|大连
|2	|上海
|3	|北京

>B

|bid	|aid	|type	|name
|:----:|:---:|:---:|:--:|
|1	|1	|学生|	赵
|2	|1	|老师|	钱
|3	|2	|领导|	孙
|4	|1	|学生|	李
|5	|2	|老师	|周
 

下面我想查询type为学生的A表和B表的所有信息

`select * from A join B on a.aid=b.aid where B.type="学生";`
得到的结果是：

![](https://okwbu9s8e.qnssl.com/left1.png)

 
如果我查询type为学生的A表信息

`select a.* from A join B on a.aid=b.aid where B.type="学生";`

得到的结果为：

![](https://okwbu9s8e.qnssl.com/left2.png)

所以！！！就是所谓的重复，

如果说你想查找 type 为学生的都来自于哪个 place     可以直接 `distinct`  ，例如：

`select distinct a.* from A join B on a.aid=b.aid where B.type="学生";`

得到的结果为：

![](https://okwbu9s8e.qnssl.com/left3.png)

但是就像上文提到的如果我查询type为学生的A表信息  

![](https://okwbu9s8e.qnssl.com/left4.png)


所得到的两个一样的数据其中包含的意义其实是不一样的。


----------


# 关于作者

```python
  {
    author  : "NULL&ERROR",
    site : "www.mingyueli.com/2016/10/mysql-left-join.html"
  }
```
