---
layout: post
title: '在排序问题上说说order by 与group by'
tags: [jekyll]
description: >
  相信了解过关系型数据库的人都知道 `order by` 与 `group by` ，一个是排序，一个是分组，其实这两者并没有关系，但是我为什么要把他们两个拿出来放在一起说明呢，当然是为了解决问题了。

  是这样的 ：
---

>> 一堆设备，每个设备都可能进行过维修，当然每一次维修作为一条记录存储，所以我想知道某一个设备的最近维修时间。


<!--more-->


所以我们现在就来解决问题，首先把表结构设计出来


 ![](https://okwbu9s8e.qnssl.com/order1.png)

`alterTime`   是修改的时间

`deviceId`    是设备的id

 我先简单的插入几条记录  select 一下  结果是这样的：

 ![](https://okwbu9s8e.qnssl.com/order2.png)

&nbsp;


准备工作完毕，开始切入正题

## 在排序问题上说说order by 与group by


我现在想得到  每一个设备最后维修的时间  

开始是这么写的

`select * from gbk.new_table group by deviceId order by alterTime desc limit 1;`

结果却是这样的：

 ![](https://okwbu9s8e.qnssl.com/order3.png)

然后我修改一下：

`select * from gbk.new_table group by deviceId order by alterTime desc;`

没有按照我想要的时间排序（因为执行的时候是先执行group by  也就是说我的order by 根本没有起作用）

 ![](https://okwbu9s8e.qnssl.com/order4.png)

于是我嵌入了一个子查询：

`select * from (select * from gbk.new_table order by alterTime desc ) as a group by deviceId;`

得到了正确的结果


## 结论

``group by 得到的记录 就是根据正常从上往下顺序的第一个，而你先order by  然后在group by 得到的记录就是你想排序得到的第一个结果``

----------


#关于作者

```python
  {
    author  : "NULL&ERROR",
    site : "www.mingyueli.com/2016/10/mysql-order-by-group-by.html"
  }
```