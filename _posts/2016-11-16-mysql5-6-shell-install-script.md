---
layout: post
title: 'MySQL5.6的Linux安装shell脚本之二进制安装'
tags: [jekyll]
description: >
  
  最近在写一个MySQL的shell安装脚本

  说明一点着里面的所有路径都是绝对路径

  下面来总结一下安装 遇到的一些问题，以及安装的过程
---

<!--more-->


这个是自带的安装过程

```shell
shell>groupadd mysql
shell>useradd -r -g mysql mysql
shell>cd /usr/local
shell>tar zxvf /path/to/mysql-VERSION-OS.tar.gz
shell>ln -s full-path-to-mysql-VERSION-OS mysql
shell>cd mysql
shell>chown -R mysql .
shell>chgrp -R mysql .
shell>scripts/mysql_install_db --user=mysql
shell>chown -R root .
shell>chown -R mysql data
shell>bin/mysqld_safe --user=mysql &amp;
# Next command is optional
shell>cp support-files/mysql.server /etc/init.d/mysql.server
```

安装之前首先检查有没有mysql的进程，如果有我们还安装个啥

```shell
mysqlProcessNum=/bin/ps aux | /bin/grep mysql | /usr/bin/wc -l | /bin/awk <span style="color: #800000;">'</span><span style="color: #800000;">{ print $1 }</span><span style="color: #800000;">'</span><span style="color: #000000;">;
if [ $mysqlProcessNum -gt 3 ]; then
    echo “已经安装MySQL“
    exit
fi
```

然后下载（可以从官网下载，但是个人感觉太慢了，于是就搭建了一个简单的ftp服务器，当然也可以本地上传嘛，在这里我就不细说了）

```shell
# download mysql package
yum install libaio   #MySQL的一个依赖包
/usr/bin/yum install awk wget -y
mysqlDownloadURL=ftp://。。。。。。。。。/pub/mysql/mysql-5.6.25-linux-glibc2.5-x86_64.tar.gz;
cd /tmp;
/bin/rm -rf mysql*.tar.gz
/usr/bin/wget $mysqlDownloadURL;
```

好我们已经下载好了

下面开始进入正题

解压，建立软连接

```shell
packageName=`/bin/ls | /bin/grep mysql*.tar.gz`;
# unpakcage mysql
/bin/tar zxvf $packageName -C /usr/local
mysqlAllNameDir=`/bin/ls -l /usr/local | grep mysql | /bin/awk '{ print $9 }'`
/bin/ln -s $mysqlAllNameDir /usr/local/mysql
```

添加用户和用户组（判断一下，如果没添加就添加一下）

```shell
userNum=`/bin/cat /etc/passwd | /bin/grep mysql | /bin/awk -F ':' '{ print $1 }' | /usr/bin/wc -l`
if [ $userNum -lt 1 ];then
    /usr/sbin/groupadd mysql
    /usr/sbin/useradd -d /usr/local/mysql -s /sbin/nologin -g mysql mysql
    echo "成功添加"
fi
```

检查/etc下面是否有my.cnf文件，有的话就干掉，或者备份

```shell
/bin/mv /etc/my.cnf /etc/my.cnf.bak
```

下面初始化

```/usr/local/mysql/scripts/mysql_install_db —datadir=/usr/local/mysql/data —user=mysql —basedir=/usr/local/mysql
```

说明一点！！！修改权限一定要在初始化之后，否则初始化之后的data目录不一定被附有权限


```shell
/bin/chown -R root.mysql /usr/local/mysql

/bin/chown -R mysql.mysql /usr/local/mysql/data/
```

现在可以自己写一个配置文件放在 `/etc` 下面

 `/etc/my.cnf` （这里也说明一点，MySQL配置文件有参数替换原则）

顺序是这样的

* **/etc/my.cnf**     
* **/etc/mysql/my.cnf**        
* **/usr/local/mysql/etc/my.cnf**        
* **~/.my.cnf**


```shell
[client]
socket=/usr/local/mysql/mysql-files/mysql.sock
[mysqld]
explicit_defaults_for_timestamp=true
datadir=/usr/local/mysql/data
socket=/usr/local/mysql/mysql-files/mysql.sock
user=mysql
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
# LOG
slow_query_log = 1
slow_query_log_file = /usr/local/mysql/mysql-files/mysql-slow.log
long_query_time = 2
# GENERAL LOG
#general_log = 1
#general_log_file = /usr/local/mysql/mysql-files/mysql-general.log
# BINARY LOG
server_id=101
log_bin=/usr/local/mysql/mysql-files/mysql-bin.log
binlog_format=ROW
sync_binlog=1
expire_logs_days=7
# ERROR LOG
log_error=/usr/local/mysql/mysql-files/mysql.err

# OTHER
character_set_server = utf8mb4
transaction-isolation = READ-COMMITTED
max_connections = 1000
log-queries-not-using-indexes
log_throttle_queries_not_using_indexes = 10

# INNODB
innodb_strict_mode=1
innodb_file_format=Barracuda
innodb_file_format_max=Barracuda
innodb_read_io_threads=4
innodb_write_io_threads=8 # 8 ~ 12
innodb_io_capacity=1000 # HDD:800 ~ 1200 SSD: 10000+
innodb_adaptive_flushing=1 # SSD: 0
innodb_flush_log_at_trx_commit=1
innodb_max_dirty_pages_pct=75
innodb_buffer_pool_dump_at_shutdown=1
innodb_buffer_pool_load_at_startup=1
innodb_flush_neighbors=1 # SSD:0
innodb_log_file_size=1024M # SSD:4G~8G HDD:1G~2G
innodb_purge_threads=1 # SSD:4
innodb_lock_wait_timeout=3
innodb_print_all_deadlocks=1
pid-file=/usr/local/mysql/data/mysqld/mysqld.pid

[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/usr/local/mysql/data/mysqld/mysqld.pid
```

下面以mysql_safe方式启动

`/usr/local/mysql/bin/mysqld_safe &`

好下面启动mysqld

`/usr/local/mysql/bin/mysqld restart`

```
[root@db mysql]# ./bin/mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.6.25-log MySQL Community Server (GPL)

Copyright (c) 2000, 2015, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type ‘help;‘ or ‘\h‘ for help. Type ‘\c‘ to clear the current input statement.

mysql> show databases;
+——————————+
| Database           |
+——————————+
| information_schema |
| mysql              |
| performance_schema |
| test               |
+——————————+
4 rows in set (0.00 sec)

mysql>
```
&nbsp;

说明几点：
&nbsp;

* * 删除重新安装的时候一定要看看是不是mysqld 的进程全部杀死了（ps&nbsp;-ef | grep mysqld），如果有使用&ldquo;kill&nbsp;-9&nbsp; 进程号&rdquo;杀死不然会严重影响下次安装

&nbsp;

* * 可能是第二次在机器上安装mysql，有残余数据影响了服务的启动。去mysql的数据目录/data看看，如果存在mysql-bin.index，就删除掉

&nbsp;

其他要说明的我在之前的安装过程中已经说明了

下面就是安装脚本的基本过程

```shell
#!/bin/bash
# mysql install script, the home directory is /usr/local/mysql-VERSION and the soft link is /usr/local/mysql
yum install libaio
/usr/bin/yum install awk wget -y
config=`/bin/pwd`
mysqlProcessNum=`/bin/ps aux | /bin/grep mysql | /usr/bin/wc -l | /bin/awk '{ print $1 }'`;
if [ $mysqlProcessNum -gt 3 ]; then
    echo "已经安装MySQL"
fi

# download mysql package
mysqlDownloadURL=ftp://。。。。。。。/pub/mysql/mysql-5.6.25-linux-glibc2.5-x86_64.tar.gz;
cd /tmp;
/bin/rm -rf mysql*.tar.gz
/usr/bin/wget $mysqlDownloadURL;
packageName=`/bin/ls | /bin/grep mysql*.tar.gz`;
# unpakcage mysql
/bin/tar zxvf $packageName -C /usr/local
mysqlAllNameDir=`/bin/ls -l /usr/local | grep mysql | /bin/awk '{ print $9 }'`
/bin/ln -s $mysqlAllNameDir /usr/local/mysql
userNum=`/bin/cat /etc/passwd | /bin/grep mysql | /bin/awk -F ':' '{ print $1 }' | /usr/bin/wc -l`
if [ $userNum -lt 1 ];then
    /usr/sbin/groupadd mysql
    /usr/sbin/useradd -d /usr/local/mysql -s /sbin/nologin -g mysql mysql
    echo "成功添加"
fi
#/bin/mv /etc/my.cnf /etc/my.cnf.bak
/usr/local/mysql/scripts/mysql_install_db --datadir=/usr/local/mysql/data --user=mysql --basedir=/usr/local/mysql
/bin/chown -R root.mysql /usr/local/mysql
/bin/chown -R mysql.mysql /usr/local/mysql/data/
#我的配置文件放到root目录下面了

/bin/cp $config/my.cnf /etc/　　　　

/usr/local/mysql/bin/mysqld_safe &
#/bin/chown -R mysql.mysql /usr/local/mysql/data/#/bin/cp $config/my.cnf /etc/
#cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld
#chmod 755 /etc/init.d/mysqld
```
----------


#关于作者

```python
  {
    author  : "NULL&ERROR",
    site : "www.mingyueli.com/2016/11/mysql5-6-shell-install-script.html"
  }
```
