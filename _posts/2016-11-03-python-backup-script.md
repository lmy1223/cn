---
layout: post
title: 'Python简单脚本之一 （备份压缩文件）'
tags: [IT]
description: >
  主要实现的功能如下：

  * 备份系统重要文件以及mongodb文件。
  * /etc/ /usr/local/mongodb/等
  * 备份路径：/data/backup/20161103/system_backup.tar.gz
  * 备份完成打印信息
---

<!--more-->


首先我要建立一备份的目录  ，我需要先判断有没有这个目录

```python
import time
import os
d_dir='/usr/local/py/backup/'    #备份目标目录
d_files='system_back.tar.gz'    #命名文件
s_dir =['/etc/','/usr/local/mongodb/']    #源目录
data=time.strftime('%Y%m%d')    #时间
if os.path.exists(d_dir) == False:
        os.mkdir(d_dir)
        print ('Successfully create dir!')
else:
        print ('the dir {0} is exists !'.format(d_dir))
```


效果如下

![](https://okwbu9s8e.qnssl.com/pyb1.png)

下面我来处理备份文件的名字：

我想加一个时间的目录  把备份信息放在时间的目录下面

然后我需要判断这个时间的目录是否存在并且打印信息

```python
import time
import os
d_dir='/usr/local/py/backup/'    
d_files='system_back.tar.gz'    #命名文件

s_dir =['/etc/','/usr/local/mongodb/']    #源目录
data=time.strftime('%Y%m%d')    #时间

d_dir1 = d_dir + data + '/'        #备份目标目录

if os.path.exists(d_dir1) == False:
        os.makedirs(d_dir1)
        print ('Successfully create {0}!'.format(d_dir1))
else:
        print ('the dir {0} is exists !'.format(d_dir1))
```

效果：

![](https://okwbu9s8e.qnssl.com/pyb2.png)


接下来我们就能够进行简单的备份了：

```python
import time
import os
d_dir='/usr/local/py/backup/'    #备份目标目录
d_files='system_back.tar.gz'    #命名文件

s_dir =['/etc/','/usr/local/mongodb/']    #源目录
data=time.strftime('%Y%m%d')    #时间
d_dir1 = d_dir + data + '/'        #创建时间的目录
r_name = d_dir1 + d_files        #压缩文件所在的目录以及名字

if os.path.exists(d_dir1) == False:
        os.makedirs(d_dir1)
        print ('Successfully create {0}!'.format(d_dir1))
else:
        print ('the dir {0} is exists !'.format(d_dir1))

tar_dir = 'tar -czvf {0} {1} '.format(r_name,' '.join(s_dir))    #定义备份文件
if os.system(tar_dir) == 0:        #执行备份的命令
    print ('the backup files is successfully!')
else :
    print ('the backup files is falsed!')

```

效果：


![](https://okwbu9s8e.qnssl.com/pyb3.png)


现在我们来优化一下这个脚本：


```python

#!/usr/bin python
#! backup system files
import time
import os
import sys
d_dir='/usr/local/py/backup/'    #备份目标目录
d_files='system_back.tar.gz'    #命名文件

s_dir =['/etc/','/usr/local/mongodb/']    #源目录
data=time.strftime('%Y%m%d')    #时间
d_dir1 = d_dir + data + '/'        #创建时间的目录
r_name = d_dir1 + d_files        #压缩文件所在的目录以及名字

def all_bak():        #写到函数里面
    print ('backup scripts start,please wait ...')
    print ('\033[32m--------------------------------------------\033[0m')
    time.sleep(2)    #等待两秒

    if os.path.exists(d_dir1) == False:
            os.makedirs(d_dir1)
            print ('Successfully create {0}!'.format(d_dir1))
    else:
            print ('the dir {0} is exists !'.format(d_dir1))

    tar_dir = 'tar -czvf {0} {1} '.format(r_name,' '.join(s_dir))    #定义备份文件
    if os.system(tar_dir) == 0:        #执行备份的命令
        print ('the backup files {0} is successfully!').format(r_name)        #打印出来备份的目录
    else :
        print ('the backup files is falsed!')
try:                                #不输入参数的时候处理异常
    if len(sys.argv[1]) == 0:
        pass
except IndexError:
        print ('warning:{please exec {0} help|all_bak}'.format(sys.argv[0]))
try:                                 #输入参数的时候处理异常
    if sys.argv[1] == 'all_bak':
        all_bak()                    #输入参数为all_bak的时候执行备份
    else:
        print ('warning:{please exec {0} help|all_bak}'.format(sys.argv[0]))    
except IndexError:
    pass
    ```

效果：

![](https://okwbu9s8e.qnssl.com/pyb4.png)

 ----------

