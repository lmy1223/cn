---
layout: post
title: 'MySQL sql_safe_updates '
tags: [IT]
description: >

---
遇到一个问题

DELETE FROM 表名字 WHERE 条件;
报错：
```sql

Error Code: 1175. You are using safe update mode and you tried to update a table without a WHERE that uses a KEY column
 To disable safe mode, toggle the option in Preferences -> SQL Queries and reconnect.4
```

官网上面这么说

[https://dev.mysql.com/doc/refman/5.6/en/server-system-variables.html#sysvar_sql_safe_updates](https://dev.mysql.com/doc/refman/5.6/en/server-system-variables.html#sysvar_sql_safe_updates)

> If set to 1, MySQL aborts UPDATE or DELETE statements that do not use a key in the WHERE clause or a LIMIT clause. (Specifically, UPDATE statements must have a WHERE clause that uses a key or a LIMIT clause, or both. DELETE statements must have both.) This makes it possible to catch UPDATE or DELETE statements where keys are not used properly and that would probably change or delete a large number of rows. The default value is 0.


`sql_safe_updates = 1`     时开启sql_safe_updates，update和delete在修改数据时，如果不带limit，需要where条件可以走索引，否则会报错

所以要 `sql_safe_updates =0`

如果想要设置默认值可以修改 `my.cnf`  配置文件

`SET SQL_SAFE_UPDATES=0;`

或者

`SET SQL_SAFE_UPDATES=1;`

重启 MySQL 服务
