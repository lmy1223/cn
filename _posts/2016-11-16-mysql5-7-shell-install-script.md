---
layout: post
title: 'MySQL5.7的Linux安装shell脚本之二进制安装'
tags: [IT]
description: >
  之前写了一个 `MySQL5.6` 的安装脚本

  `MySQL 5.7` 已经开发两年多了。相比 MySQL 5.6，有很多改进的地方。不论是在速度还是在性能上相比于 5.6 都提升了很多 ！

  所以在 5.7 上有很多值得学习的新特征，比如说：

---




<!--more-->


* **（1）安全性提高**

* **（2）增强了InnoDB引擎的一些功能**

* **（3）支持对在线某个连接直接查看执行计划**

* **（4）新增log_syslog选项，可以把MySQL的日志打印到系统日志文件中**

* **（5）还支持多线程复制**

&nbsp;等等等，在这里我就不细说了

所以想在写一个5.7的安装脚本

下面看一下安装过程

```shell
shell> groupadd mysql
shell> useradd -r -g mysql mysql
shell> cd /usr/local
shell> tar zxvf /path/to/mysql-VERSION-OS.tar.gz
shell> ln -s full-path-to-mysql-VERSION-OS mysql
shell> cd mysql
shell> mkdir mysql-files
shell> chmod 770 mysql-files
shell> chown -R mysql .
shell> chgrp -R mysql .
shell> bin/mysql_install_db --user=mysql    # Before MySQL 5.7.6
shell> bin/mysqld --initialize --user=mysql # MySQL 5.7.6 and up
shell> bin/mysql_ssl_rsa_setup              # MySQL 5.7.6 and up
shell> chown -R root .
shell> chown -R mysql data mysql-files
shell> bin/mysqld_safe --user=mysql &
# Next command is optional
shell> cp support-files/mysql.server /etc/init.d/mysql.server
```

安装之前首先检查有没有mysql的进程

```shell
mysqlProcessNum=`/bin/ps aux | /bin/grep mysql | /usr/bin/wc -l | /bin/awk '{ print $1 }'`;
if [ $mysqlProcessNum -gt 3 ]; then
    echo "已经安装MySQL"
    exit
fi
```

然后下载（可以从官网下载，但是个人感觉太慢了，于是就搭建了一个简单的ftp服务器，当然也可以本地上传嘛，在这里我就不细说了）

```shell
# download mysql package
yum install libaio   #MySQL的一个依赖包
/usr/bin/yum install awk wget -y
mysqlDownloadURL=ftp://。。。。。。。。。/pub/mysql/mysql-5.7.8-rc-linux-glibc2.5-x86_64.tar.gz;
cd /tmp;
/bin/rm -rf mysql*.tar.gz
/usr/bin/wget $mysqlDownloadURL;
```

解压，建立软连接

添加用户和用户组（判断一下，如果没添加就添加一下）

```shell
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
```

检查/etc下面是否有my.cnf文件，有的话就干掉，或者备份

`/bin/mv /etc/my.cnf /etc/my.cnf.bak`

建立mysql-files文件夹并且赋予770权限

```shell
/bin/mkdir /usr/local/mysql/mysql-files
/bin/chmod 770 /usr/local/mysql/mysql-files
/bin/chown -R mysql /usr/local/mysql/
/bin/chgrp -R mysql /usr/local/mysql/
```

初始化

```shell
/usr/local/mysql/bin/mysql_install_db --user=mysql --datadir=/usr/local/mysql/data
/usr/local/mysql/bin/mysqld --initialize --user=mysql
/usr/local/mysql/bin/mysql_ssl_rsa_setup
```

写一份配置文件my.cnf 放到/etc下面

/etc/my.cnf （这里也说明一点，MySQL配置文件有参数替换原则）

**顺序是这样的**


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
&nbsp;

修改使用权限


```shell
/bin/chown -R root .
/bin/chown -R mysql /usr/local/mysql/data /usr/local/mysql/mysql-files
```
&nbsp;

以safe 方式启动

```shell
/usr/local/mysql/bin/mysqld_safe --user=mysql &
```

&nbsp;

**``mysql5.7会生成一个初始化密码，而在之前的版本首次登陆不需要登录。``**

```shell
#重启mysqld
#cd /usr/local/mysql
#./bin/mysqld restart
#mysql5.7有默认密码
cat /root/.mysql_secret 
[root@db mysql]# cat /root/.mysql_secret 
# Password set for user 'root@localhost' at 2016-11-16 19:10:59 
ny8(ko+lhtPu
[root@db mysql] ./bin/mysql -uroot -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.7.8-rc-log

Copyright (c) 2000, 2015, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
```

下面上完整的脚本

但是因为没有修改默认的密码

所以在回报这样的错

```shell
mysql> show databases;
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.
```

修改密码步骤如下所示

```shell
mysql> SET PASSWORD = PASSWORD('123123');
Query OK, 0 rows affected, 1 warning (0.00 sec)
mysql> ALTER USER 'root'@'localhost' PASSWORD EXPIRE NEVER;
Query OK, 0 rows affected (0.00 sec)
mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)
```

**然后退出再登录就好了！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！**

```shell
#!/bin/bash
# mysql install script, the home directory is /usr/local/mysql-VERSION and the soft link is /usr/local/mysql
yum install libaio
/usr/bin/yum install awk wget -y
config=`/bin/pwd`
mysqlProcessNum=`/bin/ps aux | /bin/grep mysql | /usr/bin/wc -l | /bin/awk '{ print $1 }'`;
if [ $mysqlProcessNum -gt 3 ];then
    echo "已经安装MySQL"
fi

# download mysql package
mysqlDownloadURL=ftp://。。。。。。。。/pub/mysql/mysql-5.7.8-rc-linux-glibc2.5-x86_64.tar.gz;
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
/bin/mkdir /usr/local/mysql/mysql-files
/bin/chmod 770 /usr/local/mysql/mysql-files
/bin/chown -R mysql /usr/local/mysql/
/bin/chgrp -R mysql /usr/local/mysql/

/usr/local/mysql/bin/mysql_install_db --user=mysql --datadir=/usr/local/mysql/data
/usr/local/mysql/bin/mysqld --initialize --user=mysql
/usr/local/mysql/bin/mysql_ssl_rsa_setup
#我的配置文件放到root目录下面了
/bin/cp $config/my.cnf /etc/　　　　

/bin/chown -R root .
/bin/chown -R mysql /usr/local/mysql/data /usr/local/mysql/mysql-files
/usr/local/mysql/bin/mysqld_safe --user=mysql &
#重启mysqld
cd /usr/local/mysql
./bin/mysqld restart
#mysql5.7有默认密码

cat /root/.mysql_secret 

#cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld
#chmod 755 /etc/init.d/mysqld

```

----------

