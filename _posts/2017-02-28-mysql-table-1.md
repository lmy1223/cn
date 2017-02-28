---
layout: post
title: ' MySQL 表操作 '
tags: [IT]
description: >
  
---

表可以说  是关系型数据库的核心
默认存储基于行的方式存储，也就是一条一条的记录，每一行记录里面有一个一个的列

创建表   `create table table_name (.....)`

删除表   `drop table table_name `

查询表信息  `show create table table_name`

修改表  `alter table` 可以修改表结构 或者  表定义，比如添加索引，`alter table table_name add index 列名`

>> 对于 MySQL5.5  中在 修改添加索引操作的时候， 对表加S锁 （只读） 也就是说 在添加索引的时候，其他的线程只能进行读操作，如果有写操作出现的话，就会发生等待，如果表中的数据很大，添加 索引的就会耗费很长一段时间，在这个段时间里，业务就有可能会受到影响

>> MySQL5.6之后  新加了一个 online ，也就是说允许在线操作，也就是说操作的时候不会阻塞其他的操作线程，也就是说进行添加索引的时候，其他的线程不仅可以读，也允许增删改查操作

下面的表格是在  **官网上  扣过来的 **  不难看出添加删除索引  的时候是允许并发的增删改查，添加全文索引  `FULLTEXT` ， 改变列的数据类型，删除主键 ， 指定和转换字符集，  的时候   不允许并发增删改查

| Operation | In-Place? | Rebuilds Table? | Permits Concurrent DML?（允许并发的 增删改查） | Only Modifies Metadata? | Notes |
| :--: | :-: | :-: | :-: | :-: | :--: |
| `CREATE INDEX`, `ADD INDEX` | Yes* | No* | Yes | No | Restrictions apply for `FULLTEXT` indexes; see next row. |
| `ADD FULLTEXT INDEX` | Yes* | No* | No | No | Adding the first `FULLTEXT` index rebuilds the table if there is no user-defined `FTS_DOC_ID` column. Subsequent `FULLTEXT` indexes may be added on the same table without rebuilding the table. |
| `DROP INDEX` | Yes | No | Yes | Yes | Only modifies table metadata. |
| `OPTIMIZE TABLE` | Yes* | Yes | Yes | No | Performed in-place as of MySQL 5.6.17\. In-place operation is not supported for tables with `FULLTEXT` indexes. |
| Set column default value | Yes | No | Yes | Yes | Only modifies table metadata. |
| Change auto-increment value | Yes | No | Yes | No* | Modifies a value stored in memory, not the data file. |
| Add foreign key constraint | Yes* | No | Yes | Yes | The `INPLACE` algorithm is supported when `foreign_key_checks` is disabled. Otherwise, only the `COPY` algorithm is supported. |
| Drop foreign key constraint | Yes | No | Yes | Yes | `foreign_key_checks` can be enabled or disabled. |
| Rename column | Yes | No | Yes* | Yes | To permit concurrent DML, keep the same data type and only change the column name. |
| Add column | Yes | Yes | Yes* | No | Concurrent DML is not permitted when adding an auto-increment column. Data is reorganized substantially, making it an expensive operation. |
| Drop column | Yes | Yes | Yes | No | Data is reorganized substantially, making it an expensive operation. |
| Reorder columns | Yes | Yes | Yes | No | Data is reorganized substantially, making it an expensive operation. |
| Change `ROW_FORMAT` property | Yes | Yes | Yes | No | Data is reorganized substantially, making it an expensive operation. |


| Operation | In-Place? | Rebuilds Table? | Permits Concurrent DML?（允许并发的 增删改查） | Only Modifies Metadata? | Notes |
| :--: | :-: | :-: | :-: | :-: | :--: |
| Change `KEY_BLOCK_SIZE` property | Yes | Yes | Yes | No | Data is reorganized substantially, making it an expensive operation. |
| Make column `NULL` | Yes | Yes | Yes | No | Data is reorganized substantially, making it an expensive operation. |
| Make column `NOT NULL` | Yes* | Yes | Yes | No | `STRICT_ALL_TABLES` or `STRICT_TRANS_TABLES` `SQL_MODE` is required for the operation to succeed. The operation fails if the column contains NULL values. As of 5.6.7, the server prohibits changes to foreign key columns that have the potential to cause loss of referential integrity. See Section 13.1.7, “ALTER TABLE Syntax”. Data is reorganized substantially, making it an expensive operation. |
| Change column data type | No | Yes | No | No | Only supports `ALGORITHM=COPY` |
| Add primary key | Yes* | Yes | Yes | No | Data is reorganized substantially, making it an expensive operation. `ALGORITHM=INPLACE` is not permitted under certain conditions if columns have to be converted to `NOT NULL`. |
| Drop primary key and add another | Yes | Yes | Yes | No | Data is reorganized substantially, making it an expensive operation. |
| Drop primary key | No | Yes | No | No | Only `ALGORITHM=COPY` supports dropping a primary key without adding a new one in the same `ALTER TABLE` statement. |
| Convert character set | No | Yes* | No | No | Rebuilds the table if the new character encoding is different. |
| Specify character set | No | Yes* | No | No | Rebuilds the table if the new character encoding is different. |
| Rebuild with `FORCE` option | Yes* | Yes | Yes | No | Uses `ALGORITHM=INPLACE` as of MySQL 5.6.17. `ALGORITHM=INPLACE` is not supported for tables with `FULLTEXT` indexes. |
| “null” rebuild using `ALTER TABLE ... ENGINE=INNODB` | Yes* | Yes | Yes | No | Uses `ALGORITHM=INPLACE` as of MySQL 5.6.17. `ALGORITHM=INPLACE` is not supported for tables with `FULLTEXT` indexes. |
| Set `STATS_PERSISTENT`, `STATS_AUTO_RECALC`, `STATS_SAMPLE_PAGES` persistent statistics options | Yes | No | Yes | Yes | Only modifies table metadata. |



## 分区操作

将一个表或者引擎分解为多个更小，或者跟可管理的部分就叫做分区表，目前的MySQL只支持水平分区，而且每个分区保存自己的数据索引

简单说  ，分区表就是把一个表拆分成多个表来存储数据

mysql 分区表支持的分区 类型： ` range  ， list  ， hash  ， key     ， colum	ns   `


* *  如果分区表里面有唯一索引，分区列必须是唯一索引的一个组成部分，如果没有索引，在创建分区列的时候会自动创建索引

```sql

mysql> create table one_1(a int primary key, b varchar(10))engine=innodb
    -> partition by hash (a)
    -> ;
Query OK, 0 rows affected (0.35 sec)

mysql> create table one_2(a int primary key, b varchar(10))engine=innodb
    -> partition by hash (b)
    -> ;
ERROR 1659 (HY000): Field 'b' is of a not allowed type for this type of partitio
ning
mysql> create table one_2(a int , b int)engine=innodb
    -> partition by hash (b)
    -> ;
Query OK, 0 rows affected (0.41 sec)

```

* * `range 、list 、 hash 、 key`  分区的时候 ， 分区的字段 必须是 `int` 类型，比如说如果是时间类型的话，必须把时间类型转换成一个int 类型再去  分区，不能直接进行分区，key 分区能直接把一个列进行 hash ，转成int  


```sql

mysql> create table two(a int , b varchar(10))engine=innodb
    -> partition by key(a)
    -> partitions 4;
Query OK, 0 rows affected (1.44 sec)

-- key 分区能直接把一个列进行 hash ，转成int  
mysql> create table two_1(a int , b varchar(10))engine=innodb
    -> partition by key(b)
    -> partitions 4
    -> ;
Query OK, 0 rows affected (1.37 sec)

mysql> show create table tw\G
*************************** 1. row ***************************
       Table: two_1
Create Table: CREATE TABLE `two_1` (
  `a` int(11) DEFAULT NULL,
  `b` varchar(10) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4
/*!50100 PARTITION BY KEY (b)
PARTITIONS 4 */
1 row in set (0.07 sec)


mysql> create table two_2(a int , b datetime)engine=innodb
    -> partition by hash (b)
    -> ;
ERROR 1659 (HY000): Field 'b' is of a not allowed type for this type of partitio
ning

-- hash 可以把时间类型转换成 int 类型再去  分区
mysql> create table two_2(a int , b datetime)engine=innodb
    -> partition by hash (year(b))
    -> ;
Query OK, 0 rows affected (0.43 sec)

```

* * columns 分区支持所有的整数类型 `int  、 smallint  、 tinyint   、 bigint ` ，一部分日期类型  `date   、datetime` ， 和一部分字符串类型  `char  、varchar 、  binary  、    varbinary`

```sql
-- less than 必须严格递增分区
mysql> create table three(a int , b datetime)engine=innodb
    -> partition by range columns (b)(
    -> partition f1 values less than ("2017-01-01"),
    -> partition f2 values less than ("2016-01-01"),
    -> partition f3 values less than ("2015-01-01")
    -> );
ERROR 1493 (HY000): VALUES LESS THAN value must be strictly increasing for each
partition

/*
对哪个列进行分区，根据对应列的数据类型写出相应的表示方法，比如  datetime  类型 默认的是  YYYY-MM-DD
*/
mysql> create table three(a int , b datetime)engine=innodb
    -> partition by range columns (b)(
    -> partition f1 values less than ("2015-01-01"),
    -> partition f2 values less than ("2016-01-01"),
    -> partition f3 values less than ("2017-01-01")
    -> );
Query OK, 0 rows affected (1.23 sec)

mysql>
```

### 子分区

字面意思就是在分区的基础上再进行分区，分散数据的存储，也称之为  ` 复合分区`


* 允许在range & list  的分区上再进行  hash  或 key  分区


```sql

/*
首先 range  分区 ，在进行 key 的复合分区
*/
create table four(id int , b datetime)
partition by range(year(b))
subpartition by hash(to_days(b))
subpartitions 2(
partition f1 values less than (1999),
partition f2 values less than (2010),
partition f3 values less than maxvalue
);


mysql> create table four(id int , b datetime)
    -> partition by range(year(b))
    -> subpartition by hash(to_days(b))
    -> subpartitions 2(
    -> partition f1 values less than (1999),
    -> partition f2 values less than (2010),
    -> partition f3 values less than maxvalue
    -> );
Query OK, 0 rows affected (1.95 sec)

```

分区表的维护

查看分区表的信息
在  `information_schema`  数据库下面的  `partitions`   表查看分区的信息 

```sql
/*
就拿上一个例子表 four 举例子，一共六个分区
*/
mysql> select * from information_schema.partitions
    -> where table_name = "four"\G
*************************** 1. row ***************************
                TABLE_CATALOG: def
                 TABLE_SCHEMA: test
                   TABLE_NAME: four
               PARTITION_NAME: f1
            SUBPARTITION_NAME: f1sp0
   PARTITION_ORDINAL_POSITION: 1
SUBPARTITION_ORDINAL_POSITION: 1
             PARTITION_METHOD: RANGE
          SUBPARTITION_METHOD: HASH
         PARTITION_EXPRESSION: year(b)
      SUBPARTITION_EXPRESSION: to_days(b)
        PARTITION_DESCRIPTION: 1999
                   TABLE_ROWS: 0
               AVG_ROW_LENGTH: 0
                  DATA_LENGTH: 16384
              MAX_DATA_LENGTH: NULL
                 INDEX_LENGTH: 0
                    DATA_FREE: 0
                  CREATE_TIME: NULL
                  UPDATE_TIME: NULL
                   CHECK_TIME: NULL
                     CHECKSUM: NULL
            PARTITION_COMMENT:
                    NODEGROUP: default
              TABLESPACE_NAME: NULL
*************************** 2. row ***************************
                TABLE_CATALOG: def
                 TABLE_SCHEMA: test
                   TABLE_NAME: four
               PARTITION_NAME: f1
            SUBPARTITION_NAME: f1sp1
   PARTITION_ORDINAL_POSITION: 1
SUBPARTITION_ORDINAL_POSITION: 2
             PARTITION_METHOD: RANGE
          SUBPARTITION_METHOD: HASH
         PARTITION_EXPRESSION: year(b)
      SUBPARTITION_EXPRESSION: to_days(b)
        PARTITION_DESCRIPTION: 1999
                   TABLE_ROWS: 0
               AVG_ROW_LENGTH: 0
                  DATA_LENGTH: 16384
              MAX_DATA_LENGTH: NULL
                 INDEX_LENGTH: 0
                    DATA_FREE: 0
                  CREATE_TIME: NULL
                  UPDATE_TIME: NULL
                   CHECK_TIME: NULL
                     CHECKSUM: NULL
            PARTITION_COMMENT:
                    NODEGROUP: default
              TABLESPACE_NAME: NULL
*************************** 3. row ***************************
                TABLE_CATALOG: def
                 TABLE_SCHEMA: test
                   TABLE_NAME: four
               PARTITION_NAME: f2
            SUBPARTITION_NAME: f2sp0
   PARTITION_ORDINAL_POSITION: 2
SUBPARTITION_ORDINAL_POSITION: 1
             PARTITION_METHOD: RANGE
          SUBPARTITION_METHOD: HASH
         PARTITION_EXPRESSION: year(b)
      SUBPARTITION_EXPRESSION: to_days(b)
        PARTITION_DESCRIPTION: 2010
                   TABLE_ROWS: 0
               AVG_ROW_LENGTH: 0
                  DATA_LENGTH: 16384
              MAX_DATA_LENGTH: NULL
                 INDEX_LENGTH: 0
                    DATA_FREE: 0
                  CREATE_TIME: NULL
                  UPDATE_TIME: NULL
                   CHECK_TIME: NULL
                     CHECKSUM: NULL
            PARTITION_COMMENT:
                    NODEGROUP: default
              TABLESPACE_NAME: NULL
*************************** 4. row ***************************
                TABLE_CATALOG: def
                 TABLE_SCHEMA: test
                   TABLE_NAME: four
               PARTITION_NAME: f2
            SUBPARTITION_NAME: f2sp1
   PARTITION_ORDINAL_POSITION: 2
SUBPARTITION_ORDINAL_POSITION: 2
             PARTITION_METHOD: RANGE
          SUBPARTITION_METHOD: HASH
         PARTITION_EXPRESSION: year(b)
      SUBPARTITION_EXPRESSION: to_days(b)
        PARTITION_DESCRIPTION: 2010
                   TABLE_ROWS: 0
               AVG_ROW_LENGTH: 0
                  DATA_LENGTH: 16384
              MAX_DATA_LENGTH: NULL
                 INDEX_LENGTH: 0
                    DATA_FREE: 0
                  CREATE_TIME: NULL
                  UPDATE_TIME: NULL
                   CHECK_TIME: NULL
                     CHECKSUM: NULL
            PARTITION_COMMENT:
                    NODEGROUP: default
              TABLESPACE_NAME: NULL
*************************** 5. row ***************************
                TABLE_CATALOG: def
                 TABLE_SCHEMA: test
                   TABLE_NAME: four
               PARTITION_NAME: f3
            SUBPARTITION_NAME: f3sp0
   PARTITION_ORDINAL_POSITION: 3
SUBPARTITION_ORDINAL_POSITION: 1
             PARTITION_METHOD: RANGE
          SUBPARTITION_METHOD: HASH
         PARTITION_EXPRESSION: year(b)
      SUBPARTITION_EXPRESSION: to_days(b)
        PARTITION_DESCRIPTION: MAXVALUE
                   TABLE_ROWS: 0
               AVG_ROW_LENGTH: 0
                  DATA_LENGTH: 16384
              MAX_DATA_LENGTH: NULL
                 INDEX_LENGTH: 0
                    DATA_FREE: 0
                  CREATE_TIME: NULL
                  UPDATE_TIME: NULL
                   CHECK_TIME: NULL
                     CHECKSUM: NULL
            PARTITION_COMMENT:
                    NODEGROUP: default
              TABLESPACE_NAME: NULL
*************************** 6. row ***************************
                TABLE_CATALOG: def
                 TABLE_SCHEMA: test
                   TABLE_NAME: four
               PARTITION_NAME: f3
            SUBPARTITION_NAME: f3sp1
   PARTITION_ORDINAL_POSITION: 3
SUBPARTITION_ORDINAL_POSITION: 2
             PARTITION_METHOD: RANGE
          SUBPARTITION_METHOD: HASH
         PARTITION_EXPRESSION: year(b)
      SUBPARTITION_EXPRESSION: to_days(b)
        PARTITION_DESCRIPTION: MAXVALUE
                   TABLE_ROWS: 0
               AVG_ROW_LENGTH: 0
                  DATA_LENGTH: 16384
              MAX_DATA_LENGTH: NULL
                 INDEX_LENGTH: 0
                    DATA_FREE: 0
                  CREATE_TIME: NULL
                  UPDATE_TIME: NULL
                   CHECK_TIME: NULL
                     CHECKSUM: NULL
            PARTITION_COMMENT:
                    NODEGROUP: default
              TABLESPACE_NAME: NULL
6 rows in set (0.00 sec)
```

适当使用MySQL 分区 会提高一部分性能




