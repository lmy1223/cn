---
layout: post
title: '搭建简单FTP服务器以及遇到的几个问题（一）'
tags: [jekyll]
description: >
  `FTP` 是 File Transfer Protocol（文件传输协议）的英文简称，而中文简称为“文传协议”。用于 Internet 上的控制文件的双向传输。同时，它也是一个应用程序（Application）。


  FTP的服务器软件有很多下面就拿 `vsftpd` 举例。

  vsftpd 是一款在Linux发行版本里面最主流的FTP服务器程序，特点是小巧轻快，安全易用

---





<!--more-->


下面开始安装

先检测是否存在vsftp服务
 
`[root@tomcat ~]# rpm -qa |grep vsftpd`

没有，下面开始安装

`[root@tomcat ~]# yum install vsftpd* -y`

使用vsftpd软件，主要包括如下几个命令：


 * **启动ftp命令　　#service vsftpd start**
 
 * **停止ftp命令　　#service vsftpd stop**
 
 * **重启ftp命令　　#service vsftpd restart**


```
[root@tomcat ~]# /etc/init.d/vsftpd restart
Shutting down vsftpd:                                      [  OK  ]
Starting vsftpd for vsftpd:                                [  OK  ]
```

访问：失败   


![](https://okwbu9s8e.qnssl.com/ftp1.png)


错误排查：防火墙没关闭

```
[root@tomcat ~]# /etc/init.d/iptables stop
iptables: Setting chains to policy ACCEPT: filter          [  OK  ]
iptables: Flushing firewall rules:                         [  OK  ]
iptables: Unloading modules:                               [  OK  ]
[root@tomcat ~]#
```

连上（但是是秘名用户就能访问）

![](https://okwbu9s8e.qnssl.com/ftp2.png)

下面我们开始更改一下配置文件

`[root@tomcat ~]# vi /etc/vsftpd/vsftpd.conf`

##配置文件的内容简单介绍一下：

```shell
# Example config file /etc/vsftpd/vsftpd.conf
#
# The default compiled in settings are fairly paranoid. This sample file
# loosens things up a bit, to make the ftp daemon more usable.
# Please see vsftpd.conf.5 for all compiled in defaults.
#
# READ THIS: This example file is NOT an exhaustive list of vsftpd options.
# Please read the vsftpd.conf.5 manual page to get a full idea of vsftpd's
# capabilities.
#
# Allow anonymous FTP? (Beware - allowed by default if you comment this out).
anonymous_enable=NO　　　　　　//禁止匿名用户访问
#
# Uncomment this to allow local users to log in.
local_enable=YES　　　　//允许本地用户登录FTP
#
# Uncomment this to enable any form of FTP write command.
write_enable=YES　　　　//运行用户在FTP目录中有写入的权限
#
# Default umask for local users is 077. You may wish to change this to 022,
# if your users expect that (022 is used by most other ftpd's)
local_umask=022　　　　//设置本地用户的文件生成掩码为022，默认为077
#
# Uncomment this to allow the anonymous FTP user to upload files. This only
# has an effect if the above global write enable is activated. Also, you will
# obviously need to create a directory writable by the FTP user.
#anon_upload_enable=YES　　　　
#
# Uncomment this if you want the anonymous FTP user to be able to create
# new directories.
#anon_mkdir_write_enable=YES
#
# Activate directory messages - messages given to remote users when they
# go into a certain directory.
dirmessage_enable=YES　　　　//激活目录信息，当远程用户更改目录时，将出现提示信息
#
# The target log file can be vsftpd_log_file or xferlog_file.
# This depends on setting xferlog_std_format parameter
xferlog_enable=YES　　　　//启用上传和下载日志功能
#
# Make sure PORT transfer connections originate from port 20 (ftp-data).
connect_from_port_20=YES　　　　//启用FTP数据端口的连接请求
#
# If you want, you can arrange for uploaded anonymous files to be owned by
# a different user. Note! Using "root" for uploaded files is not
# recommended!
#chown_uploads=YES
#chown_username=whoever
#
# The name of log file when xferlog_enable=YES and xferlog_std_format=YES
# WARNING - changing this filename affects /etc/logrotate.d/vsftpd.log
#xferlog_file=/var/log/xferlog
#
# Switches between logging into vsftpd_log_file and xferlog_file files.
# NO writes to vsftpd_log_file, YES to xferlog_file
xferlog_std_format=YES　　　　//是否使用标准的日志文件格式
#
# You may change the default value for timing out an idle session.
#idle_session_timeout=600
#
# You may change the default value for timing out a data connection.
#data_connection_timeout=120
#
# It is recommended that you define on your system a unique user which the
# ftp server can use as a totally isolated and unprivileged user.
#nopriv_user=ftpsecure
#
# Enable this and the server will recognise asynchronous ABOR requests. Not
# recommended for security (the code is non-trivial). Not enabling it,
# however, may confuse older FTP clients.
#async_abor_enable=YES
#
# By default the server will pretend to allow ASCII mode but in fact ignore
# the request. Turn on the below options to have the server actually do ASCII
# mangling on files when in ASCII mode.
# Beware that on some FTP servers, ASCII support allows a denial of service
# attack (DoS) via the command "SIZE /big/file" in ASCII mode. vsftpd
# predicted this attack and has always been safe, reporting the size of the
# raw file.
# ASCII mangling is a horrible feature of the protocol.
#ascii_upload_enable=YES
#ascii_download_enable=YES
#
# You may fully customise the login banner string:
#ftpd_banner=Welcome to blah FTP service.
#
# You may specify a file of disallowed anonymous e-mail addresses. Apparently
# useful for combatting certain DoS attacks.
#deny_email_enable=YES
# (default follows)
#banned_email_file=/etc/vsftpd/banned_emails
#
# You may specify an explicit list of local users to chroot() to their home
# directory. If chroot_local_user is YES, then this list becomes a list of
# users to NOT chroot().
#chroot_local_user=YES
#chroot_list_enable=YES
# (default follows)
#chroot_list_file=/etc/vsftpd/chroot_list
#
# You may activate the "-R" option to the builtin ls. This is disabled by
# default to avoid remote users being able to cause excessive I/O on large
# sites. However, some broken FTP clients such as "ncftp" and "mirror" assume
# the presence of the "-R" option, so there is a strong case for enabling it.
#ls_recurse_enable=YES
#
# When "listen" directive is enabled, vsftpd runs in standalone mode and
# listens on IPv4 sockets. This directive cannot be used in conjunction
# with the listen_ipv6 directive.
listen=YES　　　　//让vsftpd处于独立启动监听端口模式
#
# This directive enables listening on IPv6 sockets. To listen on IPv4 and IPv6
# sockets, you must run two copies of vsftpd with two configuration files.
# Make sure, that one of the listen options is commented !!
#listen_ipv6=YES

pam_service_name=vsftpd　　　　//设置PAM认证服务配置文件名称，文件存放在/etc/pam.d/目录
userlist_enable=YES　　　　//用户列表中的用户是否允许登录FTP服务器
tcp_wrappers=YES　　　　//使用tcp_wrappwers作为主机访问控制方式
```

下面我设置成禁止用户访问模式

![](https://okwbu9s8e.qnssl.com/ftp3.png)

然后定义一个用户名

```
[root@tomcat ~]# useradd test
[root@tomcat ~]# passwd test
Changing password for user test.
New password: 
BAD PASSWORD: it is WAY too short
BAD PASSWORD: is too simple
Retype new password: 
passwd: all authentication tokens updated successfully.
[root@tomcat ~]# 
```

密码设置为123

然后访问一下：

![](https://okwbu9s8e.qnssl.com/ftp4.png) 

但是访问不进去：

百度了一下才知道是 selinux的问题

`[root@tomcat ~]# vi /etc/selinux/config`

![](https://okwbu9s8e.qnssl.com/ftp5.png)


重启一下

```
[root@tomcat ~]# reboot 

Broadcast message from root@tomcat.56team.com
    (/dev/pts/0) at 18:42 ...

The system is going down for reboot NOW!
[root@tomcat ~]# 
```

 查看是否关闭了

```
[root@tomcat ~]# sestatus
SELinux status:                 disabled
[root@tomcat ~]#
```

 

最简单的FTP服务器就搭建好了


----------


#关于作者

```python
  {
    author  : "NULL&ERROR",
    site : "www.mingyueli.com/2016/11/solve-problem-for-ftp-1.html"
  }
```
