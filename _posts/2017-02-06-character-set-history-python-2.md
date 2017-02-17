---
layout: post
title: '字符集编码与Python（二）Unicode与utf-8'
tags: [IT]
description: >
  上一篇提过了字符集的历史其中也简单的提到了 `Unicode` 与 `utf-8` 的关系，下面本文主要介绍了Python中的 Unicode 和 utf-8 ：
 
  ``utf-8和utf-16 、utf-32是一类，实现的功能是一样的，只是utf-8使用的最为广泛，但是Unicode和utf-8并不是同一类，Unicode是表现形式，utf-8是存储形式``


---


<!--more-->

#Python中的Unicode和utf-8



* **unicode是表现形式（utf-8可以解码成unicode）**

* **utf-8 、utf-16 、utf-32 是存储形式（unicode可以编码成utf-8）**

 
 >> **理解**：存储的时候需要编码成utf-8，表现的时候是一个utf-8需要解码成为Unicode，换句话说，在代码中处理的是Unicode，在文件中存储的时候是以utf-8的形式存储。

&nbsp;
 
##不使用Unicode的形式

 
```python
    In [1]: name = '张三'
     
    In [2]: print name     
    张三
     
    In [3]: name
    Out[3]: '\xe5\xbc\xa0\xe4\xb8\x89'     #utf8编码，存储形式
     
    In [4]: len(name)
    Out[4]: 6
     
    In [5]: name[0:2]     #分片操作
    Out[5]: '\xe5\xbc'
     
    In [6]: print name[0:1]
    �
     
    In [7]: type(name)     #类型是字符串类型
    Out[7]: str
     
    In [8]: type
```    

&nbsp;
     

##使用Unicode的形式：

 
Python2里面，是直接在字符串前面加一个u
 
```python
    In [8]: name = u'张三'
     
    In [9]: name
    Out[9]: u'\u5f20\u4e09'     #Unicode编码   表现形式
     
    In [10]: print name
    张三
     
    In [11]: print name[0:1]
    张
     
    In [12]: name[0:1]
    Out[12]: u'\u5f20'
     
    In [13]:  len(name)
    Out[13]: 2
     
    In [15]: type(name)
    Out[15]: unicode     #类型是一个unicode
```
 

下面重点来了
 
&nbsp;

#解码函数与编码函数

Unicode与utf-8的互相转换：
在Python里面提供了内置的方法：`decode()`、`encode()`
 
>编码： `encode()` ：从表现形式到存储形式

>解码： `decode()` ：从存储形式到表现形式

&nbsp;

**其中Unicode并没有和某一种解码形式绑定起来，**
 
```python
    In [37]: name = u'张三'
     
    In [38]: b_name = name.encode('utf-8')     #编码为不同的存储形式，既可以编码为utf-8
     
    In [39]: b_name
    Out[39]: '\xe5\xbc\xa0\xe4\xb8\x89'
     
    In [47]: type(b_name)     #类型为str
    Out[47]: str
     
    In [40]: b_name2 = name.encode('utf-16')     #也可以编码为utf-16
     
    In [41]: b_name2
    Out[41]: '\xff\xfe _\tN'
     
    In [42]: b_name3 = name.encode('utf-32')     #还可以编码为utf-32
     
    In [43]: b_name3
    Out[43]: '\xff\xfe\x00\x00 _\x00\x00\tN\x00\x00'
     
    In [44]: j_name = b_name.decode('utf-8')     #把utf-8解码为Unicode
     
    In [45]: j_name
    Out[45]: u'\u5f20\u4e09'
     
    In [46]: type(j_name)     #类型为Unicode
    Out[46]: unicode
     
```
&nbsp;

> 所以综上所述 `Unicode` 写入到一个文件里面的时候出错，错误提示为：`ASCII`编码不能大于128，`ASCII` 编码范围为0-128，当然汉字超出了 `ASCII` 的编码范围
 
> 对 `error` 的理解：Unicode为表现形式，具体存储的时候必须要编码成某一种编码的方式，Python2 中默认使用ASCII编码，所以存储ASCII，但是我现在存的是中文，中文的范围比ASCII大很多，所以存不下导致报错：
 
&nbsp;
```python
    In [47]: name = u'张三'
     
    In [50]: with open('/tmp/test', 'w') as f:
        ...:     f.write(name)
        ...:
    ---------------------------------------------------------------------------
    UnicodeEncodeError                        Traceback (most recent call last)
    <ipython-input-4-0d87fa01de83> in <module>()
          1 with open('/tmp/test', 'w') as f:
    ----> 2     f.write(name)
     
    UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-1: ordinal not in range(128)

```
     
&nbsp;
 
所以解决办法就有了，先编码为 `utf-8` 或者 `utf-16` 等等

&nbsp;
 
```python
    In [51]: with open('/tmp/test', 'w') as f:
        ...:     f.write(name.encode('utf-8'))     #编码为utf-8形式写入到文件里面
        ...:
     
     
    In [52]: with open('/tmp/test', 'r') as f:
        ...:     new_name=f.read()
        ...:
     
    In [53]: new_name.decode('utf-8')     #把utf-8解码为Unicode
    Out[53]: u'\u5f20\u4e09'
```      
&nbsp;

# Python2和Python3关于字符集方面的区别

&nbsp;

##Python2和Python3的在字符集方面的差别：

 - **Python 3**有两种表示字符序列的类型：`bytes` 和 `str` 。前者的实例包含原始的8位值；后者的实例包含Unicode字符

 - **Python 2**也有两种表示字符序列的类型，分别叫做 `str` 和 `unicode` 。与Python 3不同的是，str的实例包含原始的8位值；而unicode的实例，则包含Unicode字符

&nbsp;
 
>>1、Python2里面str表示普通的字符串，而unicode表示的就是一个unicode
也就是说：不指定类型的时候就是一个str，指定为Unicode的时候就是Unicode类型
 
```python
    In [15]: name = u'张三'
     
    In [16]: type(name)
    Out[16]: unicode
```
     
>> 2、Python3里面不指定字符串类型的时候是一个str。
    
>> 3、Python3里面的str就是Python2里面的unicode，Python2里面的str是Python3里面的bytes！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！
 
&nbsp;
 
##open函数
&nbsp;

###Python2

Python2 中有一个标准库 `codecs` 模块 帮我们自动编码解码
 `codecs` 模块提供的 `open` 函数提供一个 `encoding` 参数

```python 

    In [55]: import codecs
     
    In [56]: name = u'张三'
     
    In [57]: with open('/tmp/test', 'w', encoding='utf-8') as f:
        ...:     f.write(name)
        ...:
     
    In [58]: with open('/tmp/test', 'r', encoding='utf-8') as f:
        ...:     new_name=f.read()
        ...:
     
    In [59]: new_name
    Out[59]: u'\u5f20\u4e09'
```    
&nbsp;
 
### Python3  


Python3 的 `open` 函数本身就提供了 `encoding` 参数
我们可以通过 `encoding` 指定编码，在使用上和 `python2` 的 `codecs` 模块一样，
```python
    >>> name = '张三'
    >>> name
    '张三'
    >>> with open('/tmp/test', 'w', encoding='utf-8') as f:
    ... f.write(name)
    ...
```     
&nbsp;

# ``总结！！！！！！！！！！！！！！！！！！！``
 

 - 把Unicode字符表示为二进制数据有许多种办法，最常见的编码方式就是utf-8。！！！！！！！！！！！！！ Python3的str和Python2的Unicode，并没有和特定的二进制编码相关联。若想把Unicode字符转换成二进制数据，就必须使用 `encode` 方法，若想把二进制数据转换成Unicode字符，就必须使用 `decode`

 

 - 在编程的时候，一定要把编码和解码操作放在界面最外围来做，程序的核心部分应该使用Unicode字符类型，而不要对字符编码做任何假设。
 
&nbsp;

##Python3
```python   
    #在Python3中，我们需要编写接受str或bytes，并总是返回str的方法：
    def to_str(bytes_or_str):
      if isinstance(bytes_or_str, bytes):
        value = bytes_or_str.decode('utf-8')
      else:
        value = bytes_or_str
      return value # Instance of str
    
    
    #另外，还需要编写接受str或bytes，并总是返回bytes的方法：
    def to_bytes(bytes_or_str):
      if isinstance(bytes_or_str, str):
        value = bytes_or_str.encode('utf-8)
      else:
        value = bytes_or_str
      return value # Instance of bytes
```     
&nbsp;

##Python2 

```python     
    #在Python2中，需要编写接受str或unicode，并总是返回unicode的方法：
    #python2
    def to_unicode(unicode_or_str):
      if isinstance(unicode_or_str, str):
        value = unicode_or_str.decode('utf-8')
      else:
        value = unicode_or_str
      return value # Instance of unicode
    
    
    #另外，还需要编写接受str或unicode，并总是返回str的方法：
    #Python2
    def to_str(unicode_or_str):
      if isinstance(unicode_or_str, unicode):
        value = unicode_or_str.encode('utf-8')
      else:
        value = unicode_or_str
      reutrn vlaue # Instance of str
```
 

----------

    
#参考资料

1. [文章下面的部分内容摘自《Effective Python：编写高质量Python代码的59个有效方法》第3 条：了解bytes、str 与unicode 的区别][1]
 
&nbsp;

-------------

  [1]: http://product.dangdang.com/23895603.html