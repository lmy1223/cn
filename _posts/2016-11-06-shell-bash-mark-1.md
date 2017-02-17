---
layout: post
title: 'Shell编程之 bash 引号'
tags: [IT]
description: >
  突然发现bash是一个很有意思的东西，对于空格，单引号，双引号，反引号都有特殊的含义，而且在空格的方式来看还有很多说法

  下面我就这几天遇到的问题来逐一总结一下常见的知识：（其中单引号和双引号直接的对比比较明显，反引号可能在书写方面和单引号比较像）

---


我们先说这三个引号

#反引号：

* 反引号位于tab键的上方数字1的左侧，
* 反引号作用是命令替换，也就是说将一个命令的标准输出插入在一个命令行中的任何位置
* 反引号里面的内容会被解释成一个命令 ，也就是说被解释成 $(command)

```
[root@iZ28b4rx8dxZ mysql_shell]# echo `date +%Y%m%d`
20161106
```
　　或者

```
[root@iZ28b4rx8dxZ mysql_shell]# echo "the date is `date +%Y%m%d`"
the date is 20161106
```

　　以上的说明也就是把date解释成一个命令输出结果

#单引号：

* 就是用单引号引起来的字符串，也就保持原始的字符串　

```
[root@iZ28b4rx8dxZ ~]# echo '$PWD'
$PWD
```

　　但是要用单引号打印出来的内容里面有单引号呢：会出现如下的状况：（也就是说bash会认为输入的单引号还没有结束）

```
[root@iZ28b4rx8dxZ ~]# echo 'The answer isn't right'
> 
> '
The answer isnt right
```

　　解决方法是有的：

>> $'str' 之间到反斜杠都将转义字符来实现，百度看了看这个方法是bash特有的方法


```
[root@iZ28b4rx8dxZ ~]#  echo $'The answer isn\'t right'
The answer isn't right
```


# 双引号

* 两个双引号包围起来的字符串，部分特殊字符将起到它们本身的作用，

* 特殊字符包括: $, \, 反引号, !

我们可以和上面的单引号对比，$ 发生变量的引用, 而在单引号中的$, 就是它的字面意思

```
[root@iZ28b4rx8dxZ ~]# echo "$PWD"
/root
```

另外反斜杠\单独说一下，反斜杠是一个转义字符，但是如果在单引号中的话就是一个普通的字符

 
```
[root@iZ28b4rx8dxZ ~]# echo "He said, \"this is mine\""
He said, "this is mine"
  [root@iZ28b4rx8dxZ ~]# echo 'He said, \"this is mine\"'
  He said, \"this is mine\"
```

其他的特殊字符都是同理

#``注意！！！！！！！！``

反引号和单引号很像，所以切勿弄混

而当你需要得到一个命令的输出时，请使用反引号；当你需要输出的是一个字符串时，请使用单引号

----------
