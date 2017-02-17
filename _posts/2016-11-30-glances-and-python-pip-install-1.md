---
layout: post
title: '系统监控glances以及Python&pip 安装'
tags: [jekyll]
description: >
  `glances` 是前几天一个前辈推荐的系统监控系统，最近尝试用了一下，感觉比很多的监控系统实用，比如 `top` 只能监控本机的系统，但是 glances 即可以监控本机，也可以通过客户端模式监控其他的机器，可以把数据输出到 csv 或者 html 格式的文件 也就方便了绘制图表或者是其他程序处理，而且很实用的是他提供了基于 Xml/RPC 的 API ，这就让他实现可编程的应用

---

<!--more-->

 
glances支持很多系统，比如说 Linux，Mac OS，Win 等等
 
其中glances是python开发的，我也是从python这方面了解到的，下面简单介绍一下它的功能，其中他是使用psutil库采集系统数据，可以在终端上面显示动态的系统数据变化：CPU，磁盘，网络等等，以及内核，负载，I/O等消耗资源的进程，交互性很好，可读性很好，一眼能看到很多信息
 
下面简单安装，使用一下
 
其中安装之前必须安装了Python
 
下面先装一下python：
```shell
yum install wget#安装编译Python需要的组件
yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel
 
#下载并解压Python的源代码
wget https://www.python.org/ftp/python/2.7.9/Python-2.7.9.tgz
tar zxvf Python-2.7.9.tgz
mv Python-2.7.9 /usr/local/
cd /usr/local/
 
#编译安装Python
cd Python-2.7.9
./configure –prefix=/usr/local
 
make && make install
#将系统python命令指向Python 2.7
mv /usr/bin/python /usr/bin/python2.6.6
ln -s /usr/local/bin/python2.7 /usr/bin/python
 
#将yum需要执行的python指定为2.6.6 
vi /usr/bin/yum 
#将文件头部的
 
#!/usr/bin/python
#改成
 
#!/usr/bin/python2.6.6
  
#我们可以用ipython进行交互，比python自带的shell好用的多
#安装
ipython-0.13.1.tar.gz
tar zvxf ipython-0.13.1.tar.gz
cd ipython-0.13.1
python setup.py install
```

下面我装一下pip：

首先安装setup-tools：
```shell
wget https://pypi.python.org/packages/2.7/s/setuptools/setuptools-0.6c11-py2.7.egg  --no-check-certificate
chmod +x setuptools-0.6c11-py2.7.egg
sh setuptools-0.6c11-py2.7.egg

#或者
wget https://pypi.python.org/packages/2.7/s/setuptools/setuptools-1.4.2.tar.gz  --no-check-certificate
cp setuptools-1.4.2.tar.gz /usr/local/
cd /usr/local/
tar zxvf setuptools-1.4.2.tar.gz
cd /usr/local/setuptools-1.4.2sudo python setup.py install
```
```shell
#安装pip
wget https://pypi.python.org/packages/source/p/pip/pip-1.3.1.tar.gz --no-check-certificatecp pip-1.3.1.tar.gz /usr/src/
tar zxvf pip-1.3.1.tar.gz
cd pip-1.3.1
python setup.py install
ln -s /usr/local/Python2.7/bin/pip /usr/bin/pip
```
```shell
#安装 glances
pip install psutil
pip install pysensors
pip install hddtemp
pip install glances

python setup.py install
```
glances的使用方法可以--help得到：
```shell
 -b：显示网络连接速度 Byte/ 秒
 -B @IP|host ：绑定服务器端 IP 地址或者主机名称
 -c @IP|host：连接 glances 服务器端
 -C file：设置配置文件默认是 /etc/glances/glances.conf 
 -d：关闭磁盘 I/O 模块
 -e：显示传感器温度
 -f file：设置输出文件（格式是 HTML 或者 CSV）
 -m：关闭挂载的磁盘模块
 -n：关闭网络模块
 -p PORT：设置运行端口默认是 61209 
 -P password：设置客户端 / 服务器密码
 -s：设置 glances 运行模式为服务器
 -t sec：设置屏幕刷新的时间间隔，单位为秒，默认值为 2 秒，数值许可范围：1~32767 
 -h : 显示帮助信息
 -v : 显示版本信息
```

 

![](https://okwbu9s8e.qnssl.com/glances1.png)

 

让glances让输出 HTML 格式文件
```shell
pip install jinja2
glances -o HTML -f  /usr/local/nginx/html/glances.html
#在浏览器里面输入网址：http://localhost/usr/local/nginx/html/glances.html 就可以访问到
```
输出 csv 格式
```
glances -o CSV -f /usr/local/glances_test/glances.csv
```

glances 可以实现客户端/服务器 的工作方式：可以实现远程监控：

服务器Ip地址：111.111.111.111

客户端Ip地址：222.222.222.222

而且两个都已经安装了glances，首先应该现在服务器端启动
```shell
[root@Z ~]# glances -s -B 111.111.111.111
Glances server is running on 111.111.111.111:61209
```
可以看到glances 使用的端口号是61209，所以用户需要确保防火墙打开这个端口。

下面在客户端使用如下命令连接服务器
`glances -c 111.111.111.111`

客户端Ip地址：222.222.222.222

![](https://okwbu9s8e.qnssl.com/glances2.png)
 
----------


#关于作者

```python
  {
    author  : "NULL&ERROR",
    site : "www.mingyueli.com/2016/11/glances-and-python-pip-install-1.html"
  }
```
