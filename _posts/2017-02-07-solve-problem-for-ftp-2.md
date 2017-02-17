---
layout: post
title: '搭建简单FTP服务器中处理的几个问题（二）'
tags: [IT]
description: >
  之前写过一篇文章是关于搭建FTP的，当时是一时心血来潮，随便弄了一下其中出了一部分错误，也没有具体的记录下来，最近由于一些原因，需要自己重新拿过来好好研究一下，于是又开始研究起来，具体的搭建过程我就不说了，搭建中详细的内容可以参考

---

<!--more-->

&nbsp;

[搭建简单FTP服务器以及遇到的几个问题（一）][1]


  [1]: https://www.mingyueli.com/2016/11/ftp-1.html


上篇文章主要讲述FTP是干嘛的，为什么要用到，以及如何搭建部署，匿名非匿名等等

本篇文章  **主要讲述了**

根据我重新实践对于过程中遇到的几个问题以及可能遇到的几个问题详细总结一下，作为一下参考。


----------


#1. 首先匿名用户：

``[root@iZ28b4rx8dxZ vsftpd]# vim /etc/vsftpd/vsftpd.conf``

``anonymous_enable=YES``　　　　　　//匿名用户可以访问

&nbsp;

连接FTP，进入主目录，发现可以查看但是不能创建目录上传文件

&nbsp;

##错误信息：

```
状态:	正在创建目录“/pub/创建目录”...
命令:	MKD 创建目录
响应:	550 Permission denied.
命令:	MKD /pub/创建目录
响应:	550 Permission denied.
```
&nbsp;

## 分析错误原因

&nbsp;

### 1. 配置文件

#### 解决办法

``[root@iZ28b4rx8dxZ vsftpd]# vim /etc/vsftpd/vsftpd.conf``

``write_enable=NO``

需要把NO改为YES或者直接#注释掉，并且允许匿名账户可删除创建更名权限

```
#write_enable=NO
write_enable=YES

anon_upload_enable=YES
anon_mkdir_write_enable=YES
anon_other_write_enable=YES
```

重启FTP

```
[root@iZ28b4rx8dxZ vsftpd]# service vsftpd restart
Restarting vsftpd (via systemctl):                         [  OK  ]
```

&nbsp;

###2. FTP用户权限配置有问题

#### 解决办法

检查一下FTP用户的主目录是否存在以及用户是否有权限访问主目录。

进入FTP 的主目录修改为ftp用户ftp用户组，注意权限`755`
```
[root@iZ28b4rx8dxZ ftp]# chown ftp:ftp /var/ftp/pub/
[root@iZ28b4rx8dxZ ftp]# chmod 755 /var/ftp/pub/
[root@iZ28b4rx8dxZ ftp]# ll /var/ftp/
total 4
drwxr-xr-x 2 ftp ftp 4096 Nov  6 03:43 pub
[root@iZ28b4rx8dxZ vsftpd]# service vsftpd restart
Restarting vsftpd (via systemctl):                         [  OK  ]
```


----------


&nbsp;

#2. 匿名用户禁止访问

更改配置文件：

``[root@iZ28b4rx8dxZ vsftpd]# vim /etc/vsftpd/vsftpd.conf``

``anonymous_enable=NO``　　　　　　//匿名禁止访问

添加用户名密码

&nbsp;

##错误信息

```
响应:   530 Login incorrect.
错误:   严重错误: 无法连接到服务器
```

&nbsp;

##分析错误原因

&nbsp;

### 1. FTP密码不正确导致

#### 解决办法

可重置FTP密码后再做登录测试。

```
[root@iZ28b4rx8dxZ ~]# passwd ftpuser
Changing password for user ftpuser.
New password: 
BAD PASSWORD: it is WAY too short
BAD PASSWORD: is too simple
Retype new password: 
passwd: all authentication tokens updated successfully.
[root@iZ28b4rx8dxZ ~]# 
```

&nbsp;

### 2. FTP用户权限配置有问题

检查一下FTP用户的主目录是否存在以及用户是否有权限访问主目录。

进入FTP 的主目录修改为ftp用户ftp用户组，注意权限`755`
```
[root@iZ28b4rx8dxZ ftp]# chown ftp:ftp /var/ftp/pub/
[root@iZ28b4rx8dxZ ftp]# chmod 755 /var/ftp/pub/
[root@iZ28b4rx8dxZ ftp]# ll /var/ftp/
total 4
drwxr-xr-x 2 ftp ftp 4096 Nov  6 03:43 pub
[root@iZ28b4rx8dxZ vsftpd]# service vsftpd restart
Restarting vsftpd (via systemctl):                         [  OK  ]
```
&nbsp;

### 3. FTP限制

`vim /etc/vsftpd/vsftpd.conf`

 * userlist_deny=NO  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; // 只允许userlist_file文件中的用户可以访问ftp

 * userlist_deny=YES  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;// userlist_file文件中列出的用户不能通过ftp访问

更改配置文件为

```
userlist_enable=YES
userlist_deny=NO
userlist_file=/etc/vsftpd/user
```

然后以一个用户一行的形式加到 `/etc/vsftpd/user` 文件中，然后重启ftp

连接ftp

```
状态:	正在连接 121.42.197.209:21...
状态:	连接建立，等待欢迎消息...
状态:	不安全的服务器，不支持 FTP over TLS。
状态:	已连接
状态:	读取目录列表...
状态:	列出“/var/ftp/pub”的目录成功
状态:	正在创建目录“/var/ftp/pub/创建目录”...
状态:	读取“/var/ftp/pub/创建目录”的目录列表...
状态:	列出“/var/ftp/pub/创建目录”的目录成功
状态:	开始上传 入门.pdf
状态:	读取“/var/ftp/pub/创建目录”的目录列表...
状态:	列出“/var/ftp/pub/创建目录”的目录成功
状态:	正在删除“/var/ftp/pub/创建目录/入门.pdf”
状态:	读取“/var/ftp/pub”的目录列表...
状态:	列出“/var/ftp/pub”的目录成功
```
----------
