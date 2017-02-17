---
layout: post
title: '简单的讲Python内置数据类型'
tags: [jekyll]
description: >
  在众多语言的学习和应用中，数据类型必不可少，当然了核心数据类型也无外乎就那么几种

  （很多语言中提供了数字，字符串，文件数据类型，有一部分形式的数据类型以标准库的形式表示 ） ，但是在 python 有很多数据类型都是内置的，不需要 import 。

---


<!--more-->


----------


#python基本内置数据类型


 1. 数字
 2. 字符串
 3. 列表list
 4. 字典dict
 5. 元组
 6. 集合
 7. None
 8. 布尔
 9. 文件


----------


#1、数字

（整数，浮点数）

python的数字是无限扩展的（没有溢出的概念，也就是说可以无限大）在python2里面整数有int和long，但是python3里面已经统一了


* 幂次方

数字（python支持一个特殊的方法（幂次方），比如说3的10次方，表示的方法就是3**10）
```python
    In [1]: 3**10
    Out[1]: 59049
    In [2]:
```

* 向下取整

```python
    In [15]: 5//2.0
    Out[15]: 2.0
    
    In [16]: 5/2.0
    Out[16]: 2.5
```

* 不支持 i ++， i --，（支持++ i ， -- i     但是 和我们平时理解的也不一样，所以不要用）但是可以 i + = 1
&nbsp; 

* 内置数学函数

例如：
abs：绝对值
round：四舍五入
float：转换成浮点型
bin：把十进制转换成二进制
oct：把十进制 转换成八进制
hex：把十进制 转换成十六进制
int：转换成十进制（int（"20"）,int（"20",8）,int（"20",16） ,int（"100000",2） ）


* 还有一些公共的模块

例如：
```python
    In [2]: import math
    
    In [3]: math.pi
    Out[3]: 3.141592653589793
    
    In [4]: math.sqrt(100)     #开根号                            
    Out[4]: 10.0
    
    In [1]: import random     #随机数
    
    In [2]: random.random()
    Out[2]: 0.10360162039961152
```
&nbsp;

#2、字符串

字符串有三种形式，单引号，双引号，和三引号（好处就是可以嵌套嘛，比如说你代码里面有一段话里面就有双引号，这样外面就可以用单引号，也就是说不需要进行特殊处理了，说白了就是不需要转义了，当然你完全可以用两个双引号或者是什么的）
```python
    In [3]: "my"
    Out[3]: 'my'
    
    In [4]: "\"my"
    Out[4]: '"my'
    
    In [5]: '"my'
    Out[5]: '"my'
```
（但是最好用双引号，因为其他语言中几乎都是双引号，这样能够让人看得懂）

* 三引号：'''   """
（三引号可以换行，也就是会把换行转换成一个换行符，也是普通的字符，但是单引号和双引号就没有这个功能）
```python
    In [6]: """
       ...: a
       ...: b
       ...: """
    Out[6]: '\na\nb\n'
```

字符串是字符的一个容器，可以用下标来访问：
```python
    In [7]: a = "abcd"
    
    In [8]: a[0]
    Out[8]: 'a'
    
    In [9]: a[1]
    Out[9]: 'b'
    
    In [10]: a[-1]     #负数就是从右往左
    Out[10]: 'd'
    
    In [11]: a + "efg"
    Out[11]: 'abcdefg'
    
    In [13]: a[1:3]     #分片操作
    Out[13]: 'bc'
```

* 字符串不可变

但是我们也是可以通过别的办法来进行一定意义上的修改，可以通过表达式来创建新的对象，并将结果分配给变量来进行修改。

举个例子吧：比如说
```python
    In [7]: a = "abcd"
    
    In [8]: a
    Out[8]: 'abcd'
    
    #此时我们要修改a 变成bbcd
    #我们首先可能想到的会是这样
    
    In [9]: a[0]="b"
    ---------------------------------------------------------------------------
    TypeError                                 Traceback (most recent call last)
    <ipython-input-15-4820869b496e> in <module>()
    ----> 1 a[0]="b"
    
    TypeError: 'str' object does not support item assignment
    
    #所以我们可以这样
    
    In [10]: "b" + a[1:]     #终点不指定就是到最后
    Out[10]: 'bbcd'
    
    In [11]: a = "b"+a[1:]     #可以赋值给a
    
    In [12]: a
    Out[12]: 'bbcd'
```

* 字符串有很多操作的方法

例如：
```python
     a.capitalize a.endswith   a.isalnum    a.istitle    a.lstrip
     a.center     a.expandtabs a.isalpha    a.isupper    a.partition
     a.count      a.find       a.isdigit    a.join       a.replace
     a.decode     a.format     a.islower    a.ljust      a.rfind
     a.encode     a.index      a.isspace    a.lower      a.rindex
     a.rjust      a.splitlines a.translate
     a.rpartition a.startswith a.upper
     a.rsplit     a.strip      a.zfill
     a.rstrip     a.swapcase
     a.split      a.title
```

对于一些特定类型的操作，都是以方法调用的形式提供的
```python
    #比如
    
    In [21]: b = a.upper()
        
    In [22]: b
    Out[22]: 'BBCD'
    
    In [23]: b = b.lower()
    
    In [24]: b
    Out[24]: 'bbcd'
```

&nbsp;

#3 、列表 list

 1. （任意类型的集合）
 2. （大小可变）
 3. （可以嵌套）
 4. （支持列表综合，也可以叫做列表解析）

下面我们就来看一些例子

* 任意类型的集合
&nbsp; 

```python   
    In [25]: a = ["abc",123.1,"123"]
    
    In [26]: a
    Out[26]: ['abc', 123.1, '123']
    
    #大小可变
    
    In [27]: a.append("de")
    
    In [28]: a
    Out[28]: ['abc', 123.1, '123', 'de']     #默认添加到末尾，当然可以指定添加的位置
    
    In [29]: a.insert?     #查看帮助
    Docstring: L.insert(index, object) -- insert object before index
    Type:      builtin_function_or_method
    
    In [30]: a.insert(0,"li")
    
    In [31]: a
    Out[31]: ['li', 'abc', 123.1, '123', 'de']
    
    #可以嵌套（列表里面含有列表）
    
    In [33]: b = ["a","b",[1,2]]     #嵌套
    
    In [34]: b
    Out[34]: ['a', 'b', [1, 2]]
    
    
    #支持列表综合，也可以叫做列表推导
    
    In [47]: a = [i*2 for i in range(10)]
    
    In [48]: a
    Out[48]: [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]

```
&nbsp; 
* 列表支持这么多种方法
```python
    a.append     #添加元素
    a.index     #索引
    a.remove     #删除，以值的方式删除
    a.count     #统计列表里面某一个元素出现的次数
    a.insert     #指定位置添加元素
    a.reverse     #自己取反
    a.extend      #扩展，向列表里面增加另一个列表里面的元素 
    a.pop         #删除，以位置的方式删除
    a.sort     #排序
```

reverse的一个例子：
```python
    In [44]: b
    Out[44]: ['a', 'b', [1, 2]]
    
    In [45]: b.reverse()
    
    In [46]: b
    Out[46]: [[1, 2], 'b', 'a']

```
&nbsp; 
* 说明一些区别：

append和extend的区别
```python
    In [38]: a
    Out[38]: ['li', 'abc', 123.1, '123', 'de']
    
    In [39]: b
    Out[39]: ['a', 'b', [1, 2]]
    
    In [40]: a.append(b)     #以列表的形式添加到另一个列表中
    
    In [41]: a
    Out[41]: ['li', 'abc', 123.1, '123', 'de', ['a', 'b', [1, 2]]]
    
    In [42]: a.extend(b)     #向列表里面增加另一个列表里面的元素
    
    In [43]: a
    Out[43]: ['li', 'abc', 123.1, '123', 'de', ['a', 'b', [1, 2]], 'a', 'b', [1, 2]]
```

&nbsp;

#4、字典dict

（K：V），键值对

例如：
写出字典的形式：

```python
    In [50]: a = {
        ...:   "array": [
        ...:     1,
        ...:     2,
        ...:     3
        ...:   ],
        ...:   "number": 123,
        ...:   "object": {
        ...:     "a": "b",
        ...:     "c": "d",
        ...:     "e": "f"
        ...:   },
        ...:   "string": "Hello World"
        ...: }
    
    
    In [51]: a
    Out[51]:
    {'array': [1, 2, 3],
     'number': 123,
     'object': {'a': 'b', 'c': 'd', 'e': 'f'},
     'string': 'Hello World'}
    
    
    In [56]: a.keys()
    Out[56]: ['array', 'object', 'number', 'string']
    
    In [57]: a.values()
    Out[57]: [[1, 2, 3], {'a': 'b', 'c': 'd', 'e': 'f'}, 123, 'Hello World']
```
&nbsp; 
* 字典的方法们：
```python

     a.clear      a.has_key    a.itervalues a.setdefault a.viewkeys
     a.copy       a.items      a.keys       a.update     a.viewvalues
     a.fromkeys   a.iteritems  a.pop        a.values
     a.get        a.iterkeys   a.popitem    a.viewitems
```
&nbsp;

#5、元组

（可以理解为不可变的一个列表，很像）

支持像列表一样的 分片，索引操作等等

```python
    In [59]: a = (1,2,"ab")
    
    In [60]: a
    Out[60]: (1, 2, 'ab')
    
    In [61]: a[0]
    Out[61]: 1
    
    In [62]: a[1]
    Out[62]: 2
    
    In [63]: a[-1]
    Out[63]: 'ab'
    
    In [66]: a[1:]
    Out[66]: (2, 'ab')
```

***元组不可以修改！！！！！！！！！！！***

```python
    In [67]: a[0]= 42
    ---------------------------------------------------------------------------
    TypeError                                 Traceback (most recent call last)
    <ipython-input-67-675ed0d52101> in <module>()
    ----> 1 a[0]= 42
    
    TypeError: 'tuple' object does not support item assignment
```

&nbsp; 

* 元组的方法们：

```python
    a.count     #统计
    a.index     #索引，查找
```
&nbsp;

#6、集合

（可以参考数学上面的概念，所以集合会有一些与操作，或操作，并集，交集，补集，等）

* 集合的初始化
```python
    In [19]: set([1,2,3])
    Out[19]: {1, 2, 3}
    
    In [20]: {1,2,3}
    Out[20]: {1, 2, 3}
    
    In [22]: {i for i in range(1,4)}     #集合推导，和列表推导一样
    Out[22]: {1, 2, 3}
```

&nbsp;

#7、None

（与C语言的NULL指针类似）


None是一个常量，类型是NoneType，而且就是一个空值对象，既不是空字符串 ，不是False，也不是0，其数据遵循单例模式，也是唯一的，因而，不能创建None对象。

例如：
```python
    In [23]: id(None)
    Out[23]: 1377856600L
    
    In [25]: a = None
    
    In [26]: id(a)
    Out[26]: 1377856600L
    
    In [27]: type(None)
    Out[27]: NoneType
```

所有赋值为None的变量都相等，并且None与任何非None的对象比较结果都为False。

&nbsp;

#8、布尔

（bool）
非零数字，或者其他非空的对象，则为True（True在数字上面可以代表1，False可以代表0）
否则为False
None为False
```python
    In [68]: a = 2>4
    
    In [69]: a
    Out[69]: False
    
    In [70]: b = 2>1
    
    In [71]: b
    Out[71]: True
```

##声明！！！！！！！！！！！！！


 - C语言里面就没有 True 和 False 这个概念，用 0 表示 false ，用 1 表示 true  
 - Java 和 JavaScript里面true和false都是小写  
 - Ruby里面没有布尔型，但是 当 Ruby 需要一个布尔值时，nil表现为false，而 nil 和 false 之外的所有值都表现为 true。

&nbsp;

#9、文件

首先用open函数打开一个文件，打开的时候可以指定打开的模式：读 ，写  ，默认以读的方式打开

```python
    In [72]: f = open ('/usr/local/file/file2')
    
    In [73]: f
    Out[73]: <open file '/usr/local/file/file2', mode 'r' at 0xmcadb80>
    
    In [81]: f.readlines()
    Out[81]:
    ['Hello Word ! \n',
     'my friend, \n',
     'my name is ***,welcome to my blog.\n',
     '\n']
    
    
    In [82]: f = open ('/usr/local/file/file3','w')
    
    In [83]: f.write(("hello"*5) + '\n')
    
    In [84]: f.close()
    
    In [85]: myfile = open('/usr/local/file/file3')
    
    In [86]: text = myfile.read()
    
    In [87]: text
    
    'hellohellohellohello\n'
```

* 文件的方法们

```python
    f.close      f.flush      f.next       f.seek      #从第几个字节开始读
    f.writelines
    f.closed     f.isatty     f.read       f.softspace  f.xreadlines
    f.encoding   f.mode       f.readinto   f.tell
    f.errors     f.name       f.readline   f.truncate
    f.fileno     f.newlines   f.readlines  f.write
```

##（打开文件 读写完一定要关闭！！！！！！！！！！！！！！！）
所以Python 提供了with，with语句结束的时候文件就自动关闭了，
```python
    In [11]: with open('/usr/local/file/file3') as f:
        ...: for line in f:
        ...: ...........#操作
```

&nbsp;

#总结 

所有类型的分类
&nbsp; 


* 1、根据访问的形式来分类

>> 数字
>> 序列（字符串、元组、列表 ）（可以通过下标的形式来访问），支持索引和分片
>> 映射（字典dict），没有顺序，通过键进行访问
&nbsp; 

* 2、根据可变性分类

>> 不可变（数字、字符串、元组），不支持原处修改，可以通过表达式来创建新的对象，并将结果分配给变量来进行修改。

>> 可变（列表、字典、集合），可以原处修改，也就是说不用创建新的对象


----------


#关于作者

```python
  {
    author  : "NULL&ERROR",
    site : "www.mingyueli.com/2017/01/python-data-type-introduce.html"
  }
```
