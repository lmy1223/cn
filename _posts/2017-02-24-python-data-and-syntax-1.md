---
layout: post
title: ' Python 数据 & 语法 '
tags: [IT]
description: >
  
---

## is 和 ==

==   两个被引用的对象是否具有相同的值，也就是两个对象的值相等

is   两个被引用的对象是否是同一个对象   id( a ) == id( b )

```python
In [13]: a=(1,2)

In [14]: b = (1,2)

In [15]: a is b
Out[15]: False

In [16]: a == b
Out[16]: True

In [17]: id (a)
Out[17]: 63264456L

In [19]: id (b)
Out[19]: 63286856L

In [20]: a=(1,2)

In [21]: b=a

In [22]: a==b
Out[22]: True

In [23]: a is b
Out[23]: True

In [24]:

```

## 函数参数

对于Python中 函数的参数   既不是传值也不是传引用，传递的是 对象的引用，函数在参数传递的过程中，把整个对象传进去，对不可变的对象（比如说 字符串） 由于不能修改  所以 如果修改的话是通过生成一个新的对象 来实现，对于可以改变的对象来说 （比如说列表 ）  修改的话对不论是对外部还是内部都都可变

例如：

### 字符串

```python

In [38]: def a(string):
    ...:     string = "li"
    ...:

In [39]: name = "zhang"

In [40]: print name
zhang

In [41]: a(name)

In [42]: print name
zhang
```

### 列表

```python
In [1]: def a(list_1):
   ...:     list_1[0]=0
   ...:

In [2]: list_2=[5,10]

In [3]: a(list_2)

In [4]: print list_2
[0, 10]

```

字符串是不可变的，如果要修改字符串的话可以通过切割  连接	比如  ` +`  或者 `join`  
另外还有 `%` 和 ` format `

但是通常情况下 推荐使用 join 
因为  `+ `   会生成很多中间结果
比如说有`a1 a2 a3 a4 a5 a6 ...... an`个字符串需要连接  如果用  `+ `  的话会生成很多中间结果 ，占用很多内存 `a1a2   a1a2a3   a1a2a3a4  a1a2a3a4a5   a1a2a3a4a5a6  .......       a1a2a3a4a5a6...an  `

但是  join  会一次性连接生成字符串

## 列表 与 列表解析

### 列表支持计算操作

`[ 1 ] * 10`

```python
In [16]: [ 1 ] * 10
Out[16]: [1, 1, 1, 1, 1, 1, 1, 1, 1, 1]
```

### 列表的修改

```python
In [17]: a = [1,2,3,4,5]

In [18]: a = a[1:] + [6]

In [19]: a
Out[19]: [2, 3, 4, 5, 6]

```

### 列表解析

` [ i*i for i in range(10) ]`

```python
In [8]: [ i*i for i in range(10) ]
Out[8]: [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```
` [ i*i for i in range(10) if i % 2 == 0 ]`

```python
In [9]:  [ i*i for i in range(10) if i % 2 == 0 ]
Out[9]: [0, 4, 16, 36, 64]
```

### 列出某个目录下面所有的目录的列表

` [ items for items in os.listdir('/usr/local')] `

```python
In [14]: import os

In [15]: [ items for items in os.listdir('/usr/local')]
Out[15]: 
['bin',
 '.git',
 'aegis',
 'games',
 'yagmail-0.5.148',
 'include',
.......................
```

### 列表输出以   /usr/local   下   .py 结尾的所有文件

```python
In [28]: import glob

In [29]: [os.path.realpath(items) for items in glob.glob("/usr/local/*.py")]
Out[29]: 
['/usr/local/login_auth.py',
 '/usr/local/yagmail.py',
 '/usr/local/mysql_backup.py']

```


## 字典 

初始化有三种方式：

* ` a = {}; a["id"] = 1; a["name"] = "zhangsan"; ` 

* ` a = { "id": 1, "name": "zhangsan" }`

* ` a = dict( id = 1, name = "zhangsan" )`

字典也有字典推导
和列表解析相似

` { i : i * i for i in range(5, 10) if i % 2 != 0}`

```python
In [23]: { i : i * i for i in range(5, 10) if i % 2 != 0}
Out[23]: {5: 25, 7: 49, 9: 81}

```

## 介绍一个 collection 标准库里面的 defaultdict 方法

```python

In [27]: bb = '1111222233334444'

In [28]: dict_1 ={}

In [29]: for b in bb:
    ...:     dict_1[b] += 1
    ...: print dict_1
    ...:
---------------------------------------------------------------------------
KeyError                                  Traceback (most recent call last)
<ipython-input-29-5f5c9103785d> in <module>()
      1 for b in bb:
----> 2     dict_1[b] += 1
      3 print dict_1

KeyError: '1'

```

因为：当请求字典对象里面没有的key时,python会抛出异常KeyError

所以此时可以用到 defaultdict 方法
给字典value元素添加默认类型

```python

In [30]: import collections

In [31]: bb = '1111222233334444'

In [32]: dict_2 = collections.defaultdict(int)

In [33]: for b in bb:
    ...:     dict_2[b] += 1
    ...: print dict_2
    ...:
defaultdict(<type 'int'>, {'1': 4, '3': 4, '2': 4, '4': 4})

```
 
## if

if   elif  else


```python
a = int(raw_input("Please input a = "))
b = int(raw_input("Please input b = "))
if a > b:
    print "The b is bigger"
elif a < b :
    print "The a is bigger"
else :
    print "a = b !"
```

else并不是和最近的if是一对，因为Python通过代码缩进 实现代码块 比如说

```python
if x:
	fi y:
		a1
else：
	a2	
```


## while 

```python
while 1 :
	print "假"
else ：		# 可选
	print "真"
```

## for

序列迭代器 ， 一般迭代对象集合列表 字典 等等

```python
In [60]: for i in range(10):
   ...:     if i % 2 == 0:
   ...:         print i*i
   ...:     if i % 2 != 0:
   ...:         print 0
   ...: else:
   ...:     print "finish"
   ...:
0
0
4
0
16
0
36
0
64
0
finish

```


```python
In [5]: a = dict( a1 = 1, a2 = 2, a3 = 3 )

In [6]: a
Out[6]: {'a1': 1, 'a2': 2, 'a3': 3}

In [7]: for key,value in a :
   ...:     print key,value
   ...:
a 1
a 3
a 2

```

## zip 

查看一下帮助文档

```python
In [8]: zip?
Docstring:
zip(seq1 [, seq2 [...]]) -> [(seq1[0], seq2[0] ...), (...)]

Return a list of tuples, where each tuple contains the i-th element
from each of the argument sequences.  The returned list is truncated
in length to the length of the shortest argument sequence.
Type:      builtin_function_or_method

```

经常被用于字典初始化

```python
In [10]: aa = ("a", "b", "c")

In [11]: bb = (1, 2, 3)

In [12]: dict_1 = dict(zip(aa, bb))

In [13]: dict_1
Out[13]: {'a': 1, 'b': 2, 'c': 3}
```


