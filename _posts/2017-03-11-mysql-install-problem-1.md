---
layout: post
title: ' MySQL 安装时出现的 ERROR'
tags: [IT]
description: >
  
---


真是装一次出现几个问题  ，  总结一下吧 

## Q 1
```powershell

[root@ftp mysql]# cp support-files/mysql.server /etc/init.d/mysql.server
[root@ftp mysql]# service mysql start
Starting MySQL.. ERROR! The server quit without updating PID file (/usr/local/mysql/data/mysqld/mysqld.pid).
```
### 解决

1. 可能进程里已经存在mysql进程

查看是否有mysqld进程，如果有  kill 掉 ，然后重新启动mysqld 

2. 可能是` /usr/local/mysql/data/mysqld/ ` 文件夹不存在或者 `/usr/local/mysql/data/mysqld/mysqld.pid`   文件没有写的权限


```powershell
[root@ftp data]# ps -ef | grep mysql

[root@ftp mysql]# mkdir /usr/local/mysql/data/mysqld

[root@ftp data]# chown -R mysql mysqld/
[root@ftp data]# ll
total 2109468
-rw-rw---- 1 mysql mysql         56 Mar 11 08:19 auto.cnf
-rw-rw---- 1 mysql mysql   12582912 Mar 11 08:18 ibdata1
-rw-rw---- 1 mysql mysql 1073741824 Mar 11 08:21 ib_logfile0
-rw-rw---- 1 mysql mysql 1073741824 Mar 11 08:19 ib_logfile1
drwx------ 2 mysql mysql       4096 Mar 11 08:17 mysql
drwxr-xr-x 2 mysql root        4096 Mar 11 08:24 mysqld
drwx------ 2 mysql mysql       4096 Mar 11 08:17 performance_schema
drwxr-xr-x 2 mysql mysql       4096 Mar 11 08:16 test
[root@ftp data]# service mysql start
Starting MySQL.. SUCCESS! 

[root@ftp data]# ps -ef | grep mysql
root     21436     1  0 08:25 pts/0    00:00:00 /bin/sh /usr/local/mysql/bin/mysqld_safe --datadir=/usr/local/mysql/data --pid-file=/usr/local/mysql/data/mysqld/mysqld.pid
mysql    21996 21436  1 08:25 pts/0    00:00:00 /usr/local/mysql/bin/mysqld --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data --plugin-dir=/usr/local/mysql/lib/plugin --user=mysql --log-error=/var/log/mysqld.log --pid-file=/usr/local/mysql/data/mysqld/mysqld.pid --socket=/usr/local/mysql/mysql-files/mysql.sock
root     22027 20139  0 08:25 pts/0    00:00:00 grep mysql
```



## Q 2
```powershell
[root@ftp bin]# ./mysql -uroot -p
./mysql: error while loading shared libraries: libncurses.so.5: cannot open shared object file: No such file or directory

```

### 解决

显然是没有找到 libncurses.so.5
于是用root用户安装这个库 

```powershell
[root@ftp lib]# yum install libncurses.so.5 

```

## Q 3 

```powershell
[root@ftp bin]# ./mysql -uroot -p
./mysql: error while loading shared libraries: libstdc++.so.6: cannot open shared object file: No such file or directory

```

原因主要是当前系统用的是X64位，但是系统中缺少X32的链接库，所以会报错

于是用root用户安装这个库 

```powershell
[root@ftp lib]# yum install libstdc++.so.6
```


`libncurses`   库用来在显示器上显示文本界面。 典型例子就是Linux内核的  make menuconfig配置界面。


```powershell
[root@ftp lib]# ll /usr/lib/
total 1260
drwxr-xr-x. 3 root root   4096 Oct 21 09:39 anaconda-runtime
drwxr-xr-x. 3 root root   4096 Oct 21 09:39 ConsoleKit
dr-xr-xr-x. 2 root root   4096 Sep 23  2011 games
drwxr-xr-x  2 root root  12288 Mar 10 21:27 gconv
lrwxrwxrwx  1 root root     14 Mar 11 08:33 libform.so.5 -> libform.so.5.7
-rwxr-xr-x  1 root root  57708 Mar 16  2015 libform.so.5.7
lrwxrwxrwx  1 root root     15 Mar 11 08:33 libformw.so.5 -> libformw.so.5.7
-rwxr-xr-x  1 root root  62700 Mar 16  2015 libformw.so.5.7
lrwxrwxrwx  1 root root     24 Mar 10 21:27 libfreebl3.chk -> ../../lib/libfreebl3.chk
lrwxrwxrwx  1 root root     23 Mar 10 21:27 libfreebl3.so -> ../../lib/libfreebl3.so
lrwxrwxrwx  1 root root     28 Mar 10 21:27 libfreeblpriv3.chk -> ../../lib/libfreeblpriv3.chk
lrwxrwxrwx  1 root root     27 Mar 10 21:27 libfreeblpriv3.so -> ../../lib/libfreeblpriv3.so
-rwxr-xr-x  1 root root  17312 May 10  2016 libmemusage.so
lrwxrwxrwx  1 root root     14 Mar 11 08:33 libmenu.so.5 -> libmenu.so.5.7
-rwxr-xr-x  1 root root  28088 Mar 16  2015 libmenu.so.5.7
lrwxrwxrwx  1 root root     15 Mar 11 08:33 libmenuw.so.5 -> libmenuw.so.5.7
-rwxr-xr-x  1 root root  28760 Mar 16  2015 libmenuw.so.5.7
lrwxrwxrwx  1 root root     15 Mar 11 08:33 libpanel.so.5 -> libpanel.so.5.7
-rwxr-xr-x  1 root root  10268 Mar 16  2015 libpanel.so.5.7
lrwxrwxrwx  1 root root     16 Mar 11 08:33 libpanelw.so.5 -> libpanelw.so.5.7
-rwxr-xr-x  1 root root  10268 Mar 16  2015 libpanelw.so.5.7
-rwxr-xr-x  1 root root   7464 May 10  2016 libpcprofile.so
lrwxrwxrwx  1 root root     19 Mar 10 21:46 libstdc++.so.6 -> libstdc++.so.6.0.13
-rwxr-xr-x  1 root root 930192 May 10  2016 libstdc++.so.6.0.13
lrwxrwxrwx  1 root root     13 Mar 11 08:33 libtic.so.5 -> libtic.so.5.7
-rwxr-xr-x  1 root root  73392 Mar 16  2015 libtic.so.5.7
dr-xr-xr-x. 2 root root   4096 Oct 21 09:53 locale
drwxr-xr-x. 3 root root   4096 Aug 18  2016 python2.6
drwxr-xr-x. 3 root root   4096 Oct 21 09:53 rpm
lrwxrwxrwx. 1 root root     30 Oct 21 09:54 sendmail -> /etc/alternatives/mta-sendmail
lrwxrwxrwx. 1 root root     24 Oct 21 09:54 sendmail.postfix -> ../sbin/sendmail.postfix
drwxr-xr-x. 2 root root   4096 Jul 13  2016 yum-plugins

```



## Q 4

```powershell
ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2)

```

### 解决
打开/etc/my.cnf,看看里面配置的 socket 位置目录

`socket=/usr/local/mysql/mysql-files/mysql.sock`

路径和  `/tmp/mysql.sock` 不一致。建立一个软连接

```ln -s /usr/local/mysql/mysql-files/mysql.sock /tmp/mysql.sock```


```powershell
[root@ftp bin]# ./mysql -uroot -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.6.26-log MySQL Community Server (GPL)

Copyright (c) 2000, 2015, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| mysqld             |
| performance_schema |
| test               |
+--------------------+
5 rows in set (0.03 sec)

mysql> 

```