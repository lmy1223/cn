---
layout: post
title: 'Python与MySQL数据类型及数据处理（一）'
tags: [jekyll]
description: >
  今天有人问我  MySQL的数据类型和 Python 的数据类型有什么区别呢？我一脸懵逼啊

  这两者的数据类型有什么直接的关系么，于是经过我的和众多网民的分析，最终的得出一个脑残级别的结论：

  ``SQL 是查询导出数据的，而 Python 则是分析数据的，一个是数据的计算，一个是数据的处理，两者并没有冲突。``


---


<!--more-->


但是我还是非常无聊的总结了一下 MySQL 和 Python 数据类型和对数据处理方面的知识。以此来复习一下 MySQL 和 Python 的基本知识

**但是我本文还是重点说一下MySQL的数据类型，以及别人和我的一些经验**


----------


# 数据类型

所有的标准 SQL 中的数据类型几乎在 MySQL 里面都有


----------


## 数字类型：

MySQL数据类型

整形：int，tinyint，smallint，bigint  ，medium
浮点型： float，double，decimal

### 整形
| 类型 | 占用的空间大小（字节） | 取值范围（Signed） |取值范围（Unsigned）|
| :-: | :----------------: | :---------------: | :----:|
|int|4|-2147483648-2147483647|0-4294967295|
|tinyint|1|-128-127|0-255|
|smallint|2|-32768-32767|0-65535|
|medium|3|-8388608-8388607|0-16777215|
|bigint|8|-9223372036854775808-9223372036854775807|0-18446744073709551615|

* int 类型  作为自增的主键的话，如果业务发展的比较快，那么表很快就会被填满，所以线上 一般列是自增的要求，一般都用 `bigint`，而很少用int 。

* 如果你的表示数据字典表 ，所以本身的信息就不是很多，id 的数据类型一般都会选择用 `tinyint`。


### Signed & Unsigned

最直观的显示就是有没有符号
有符号就是从负数到正数，无符号的话就是从0开始的值，但是！推荐不使用 `unsigned` 使用` unsigned` 是有一定风险的，有溢出的现象

但是一旦出现溢出现象解决的办法也是有的：

解决  ``set sql_mode='NO_UNSIGNED_SUBTRACTION``

### Auto_increment 

自增，每张表最多只能有一个，而且必须是索引的一部分

### Zerofill 

只是负责 显示属性，值不做任何修改

###  浮点型

单精度类型：float，4个字节
双精度类型：duble，8个字节
高精度类型：decimal，可变长度

* float(M,D)，duble(M,D)，decimal(M,D) M为整数位，D为小数位
* 财务、账务系统必须用DECIMAL类型 。


----------


## 字符串类型

char(n) 定长字符字符 （n 表示字符  ）
vachar(n) 变长字符（n 表示字符）
>> 尽量使用varchar而不要使用char  

text，tinytext，mediumtext，longtext 都是大对象
>> text 可以理解 为 varchar

binary(n) 定长二进制字节（n 表示字节  ）
varbinary(n) 变长二进制字节（n 表示字节  ）

blob，tinyblob ，mediumblob，longblog  都是二进制大对象
>> blob 可以理解为 varbinary

* blob 和 text 列都不能有默认值

* 对 类型为 text 的列进行排序时只使用该列的前 `max_sort_length `个字节，例如：

```
mysql> select @@global.max_sort_length;
+--------------------------+
| @@global.max_sort_length |
+--------------------------+
|                     1024 |
+--------------------------+
1 row in set (0.00 sec)
```

### 字符集

在 MySQL 字符串类型里面 `char`，`varchar`，`text`，`tinytext`，`mediumtext`，`longtext` 是有字符集（一组符号和编码的集合）概念的 

在数据库里面，常见的字符集有：`utf8mb4`，`utf8`，`gbk`，`gb18030`

其中：

* 尽量用 `utf8mb4` 因为`utf8`在表情上面可能是乱码
* `gb18030` 在MySQL5.7版本才支持
```
mysql> show character set;
+----------+-----------------------------+---------------------+--------+
| Charset  | Description                 | Default collation   | Maxlen |
+----------+-----------------------------+---------------------+--------+
| big5     | Big5 Traditional Chinese    | big5_chinese_ci     |      2 |
| dec8     | DEC West European           | dec8_swedish_ci     |      1 |
| cp850    | DOS West European           | cp850_general_ci    |      1 |
| hp8      | HP West European            | hp8_english_ci      |      1 |
| koi8r    | KOI8-R Relcom Russian       | koi8r_general_ci    |      1 |
| latin1   | cp1252 West European        | latin1_swedish_ci   |      1 |
| latin2   | ISO 8859-2 Central European | latin2_general_ci   |      1 |
| swe7     | 7bit Swedish                | swe7_swedish_ci     |      1 |
| ascii    | US ASCII                    | ascii_general_ci    |      1 |
| ujis     | EUC-JP Japanese             | ujis_japanese_ci    |      3 |
| sjis     | Shift-JIS Japanese          | sjis_japanese_ci    |      2 |
| hebrew   | ISO 8859-8 Hebrew           | hebrew_general_ci   |      1 |
| tis620   | TIS620 Thai                 | tis620_thai_ci      |      1 |
| euckr    | EUC-KR Korean               | euckr_korean_ci     |      2 |
| koi8u    | KOI8-U Ukrainian            | koi8u_general_ci    |      1 |
| gb2312   | GB2312 Simplified Chinese   | gb2312_chinese_ci   |      2 |
| greek    | ISO 8859-7 Greek            | greek_general_ci    |      1 |
| cp1250   | Windows Central European    | cp1250_general_ci   |      1 |
| gbk      | GBK Simplified Chinese      | gbk_chinese_ci      |      2 |
| latin5   | ISO 8859-9 Turkish          | latin5_turkish_ci   |      1 |
| armscii8 | ARMSCII-8 Armenian          | armscii8_general_ci |      1 |
| utf8     | UTF-8 Unicode               | utf8_general_ci     |      3 |
| ucs2     | UCS-2 Unicode               | ucs2_general_ci     |      2 |
| cp866    | DOS Russian                 | cp866_general_ci    |      1 |
| keybcs2  | DOS Kamenicky Czech-Slovak  | keybcs2_general_ci  |      1 |
| macce    | Mac Central European        | macce_general_ci    |      1 |
| macroman | Mac West European           | macroman_general_ci |      1 |
| cp852    | DOS Central European        | cp852_general_ci    |      1 |
| latin7   | ISO 8859-13 Baltic          | latin7_general_ci   |      1 |
| utf8mb4  | UTF-8 Unicode               | utf8mb4_general_ci  |      4 |
| cp1251   | Windows Cyrillic            | cp1251_general_ci   |      1 |
| utf16    | UTF-16 Unicode              | utf16_general_ci    |      4 |
| utf16le  | UTF-16LE Unicode            | utf16le_general_ci  |      4 |
| cp1256   | Windows Arabic              | cp1256_general_ci   |      1 |
| cp1257   | Windows Baltic              | cp1257_general_ci   |      1 |
| utf32    | UTF-32 Unicode              | utf32_general_ci    |      4 |
| binary   | Binary pseudo charset       | binary              |      1 |
| geostd8  | GEOSTD8 Georgian            | geostd8_general_ci  |      1 |
| cp932    | SJIS for Windows Japanese   | cp932_japanese_ci   |      2 |
| eucjpms  | UJIS for Windows Japanese   | eucjpms_japanese_ci |      3 |
+----------+-----------------------------+---------------------+--------+
40 rows in set (0.08 sec)
```

设置字符集：有三种方法：
 
1.设置全局字符集 ：设置配置参数   `character_set_sever = xxx`

2 建立数据库和表或者列的时候    指定字符集例如：

```
CREATE TABLE `student` (
  `studentId` int(11) NOT NULL CHARSET utf8mb4,
  `studentName` varchar(10) NOT NULL CHARSET utf8mb4,
  `studentAge` int(11) NOT NULLCHARSET utf8mb4
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='学生表';

```  

### collection   排序规则

默认的排序规则是不区分大小写 ，忽略空格

```
mysql> select 'a'='a';
+---------+
| 'a'='a' |
+---------+
|       1 |
+---------+
1 row in set (0.00 sec)

mysql> select 'a'='A';
+---------+
| 'a'='A' |
+---------+
|       1 |
+---------+
1 row in set (0.00 sec)

mysql> select 'a'='A ';
+----------+
| 'a'='A ' |
+----------+
|        1 |
+----------+
1 row in set (0.00 sec)
```

如果大小写区分   不忽略空格  排序规则应该设置成 utf8mb4_bin

```
mysql> SET NAMES utf8mb4 COLLATE utf8mb4_bin;
Query OK, 0 rows affected (0.03 sec)

mysql> select ' a'='a';
+----------+
| ' a'='a' |
+----------+
|        0 |
+----------+
1 row in set (0.00 sec)

mysql> select 'a'='A';
+---------+
| 'a'='A' |
+---------+
|       0 |
+---------+
1 row in set (0.00 sec)
```

## 集合类型

MySQL 特有的  枚举型和集合类型 ：`enum`  &  `set`

可以用于约束的检查代替SQL_Server里面定义的约束：和 `sql_mode` 配合使用，设置成严格模式，`SET SQL_MODE='strict_trans_tables'`

比如说：性别  `sex enum('male','female')`   当插入除了male和female 以外的值的时候是会报错，因为数据的一致性受到破坏 。

枚举类型和集合类型本身存储是很高效的 。
必须配合`sql_mode` 使用 ，不然插入以外的值是不会报错的，但是本身插入的是一个null 。
```
mysql> CREATE TABLE `test` (
    ->   sex ENUM('male','female')
    -> );
Query OK, 0 rows affected (0.36 sec)

mysql> INSERT INTO test SELECT 'male';
Query OK, 1 row affected (0.08 sec)
Records: 1  Duplicates: 0  Warnings: 0

mysql> INSERT INTO test SELECT 'a';
Query OK, 1 row affected, 1 warning (0.03 sec)
Records: 1  Duplicates: 0  Warnings: 1

mysql> SELECT * FROM test;
+------+
| sex  |
+------+
| male |
|      |
+------+
2 rows in set (0.00 sec)

mysql> SET SQL_MODE='strict_trans_tables';
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO test SELECT 'b';
ERROR 1265 (01000): Data truncated for column 'sex' at row 1
```


----------


# 日期类型

year，data，time，datatime，timestamp

| 类型 | 占用的空间大小（字节） | 取值范围|
| :-: | :----------------: | :---------------: |
|year|1|YEAR(2): 1970~ 2070 YEAR(4): 1901~ 2155|
|data|3|1000-01-01~ 9999-12-31|
|time|3|-838：59：59~ 838：59：59|
|datatime|8|1000-01-01 00:00:00~ 9999-12-31 23:59:59|
|timestamp|4|1970-01-01 00：00：00 UTC~2038-01-19 03:14:07 UTC|



# 日期函数

`now`：返回执行时的时间
`current_timestamp` ：返回执行时的时间
`sysdate`：返回执行函数时的时间
`date_add(date,INTERVALexprunit)`：增加时间
`date_sub(date,INTERVALexprunit)`：减少时间
`date_format`：格式化时间显示

```
mysql> select now(),sysdate(),sleep(5),now(),sysdate();
+---------------------+---------------------+----------+---------------------+--
-------------------+
| now()  | sysdate()  | sleep(5) | now()   | sysdate()  |
+--------+-----------+----------+----------+--------+
| 2017-02-10 13:36:51 | 2017-02-10 13:36:51 | 0 | 2017-02-10 13:36:51 | 2017-02-10 13:36:56 |
+---------------------+---------------------+---+---------------------+---------------------+
1 row in set (5.02 sec)

```


----------


# JSON  类型
>> MySQL 5.7版本开始支持非结构化数据类型 `JSON`，用户可以在数据库级别操作 JSON 的任意键值和数据。
```
mysql> CREATE TABLE test_two ( id int auto_increment,
	-> data json,
	-> primarykey(uid)
	-> );
Query OK, 0 rows affected (0.01 sec)

mysql> INSERT INTO test_two values (NULL,
	-> '{"name":"zhangsan","mail":"xxx@xxx.com","string": "Hello World"}');
Query OK, 1 row affected (0.00 sec)

mysql> INSERT INTO test_two values (NULL,"a");
ERROR 3130 (22032): Invalid JSON text: "Invalid value" at position 2 in value (or column) 'a'.
```
在MySQL官方文档摘录的一部分介绍如下：

提供的与json类型相关的函数
```
JSON_APPEND() JSON_ARRAY_INSERT() JSON_UNQUOTE() JSON_ARRAY()
JSON_REPLACE() JSON_CONTAINS() JSON_DEPTH() JSON_EXTRACT()
JSON_INSERT() JSON_KEYS() JSON_LENGTH() JSON_VALID()
JSON_MERGE() JSON_OBJECT() JSON_QUOTE() JSON_REMOVE()
JSON_CONTAINS_PATH() JSON_SEARCH() JSON_SET() JSON_TYPE()
```

函数的调用规则基本可以表示为：

``JSON_APPEND(json_doc, path, val[, path, val] ...)``

参数解释：
* json_doc 为JSON文档，或者是表里面的某一列，也可以是JSON文档里面的嵌套子文档变量
* path 为路径表达式，用来定位要访问的键，更方便快速的访问JSON的键值
* val 可有可无，若有表示键对应的操作数值

用法如下所示：

```
mysql> select
	-> json_extract(data, '$.name'),
	-> json_extract(data,'$.mail')
	-> from test_two;
+-----------------------------+-------------------------------+
| jsn_extract(data, '$.name') | jsn_extract(data,'$.address') |
+-----------------------------+-------------------------------+
| "zhangsan" | "xxx@xxx.com" |
+-----------------------------+-------------------------------+
1 rows in set (0.00 sec)
```

关于 MySQL支持JSON 的问题有机会拿出来单独谈谈


----------


#关于作者

```python
  {
    author  : "NULL&ERROR",
    site : "www.mingyueli.com/2017/02/python-mysql-data-type-and-deal-1.html"
  }
```
