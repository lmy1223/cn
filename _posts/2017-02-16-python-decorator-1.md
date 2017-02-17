---
layout: post
title: 'Python 装饰器'
tags: [IT]
description: >
  不只是在 Java C++ 中有面向对象的说法

  在 Python中 也经常被 这么说 `一切皆对象`

  在OOP （ 面向对象 ）的设计模式中，decorator 被称为装饰模式。其中这种装饰模式需要通过继承和组合来实现，而Python 则直接从语法层次支持 decorator 。


---


<!--more-->


从 Python 2.4版本开始 提供了装饰器(decorator) 
装饰器本质上就是一个函数，这个函数接收其他函数作为参数，（有点类似于回调函数  callback）并将其以一个新的修改后的函数进行替换。

* 其中无参的装饰器 参数就是要装饰的函数 

* 有参装饰器参数是需要的参数 最后返回的是内部函数 

装饰器本身可以使用多次，也可以同时使用多个装饰器
&nbsp;


----------


#  装饰器定义 & 使用
&nbsp;

>> 函数嵌套就是定义一个外部函数，里面又定义了一个内部的函数，结果返回这个内部函数 

>> 而装饰器就是这个外部函数 传进来一个参数（这个参数可以是一个函数），内部函数里面调用这个参数（函数）然后返回这个内部的函数。

&nbsp;

使用一个装饰器就是 ：  @ 装饰器的名字， 如果装饰器有参数，里面可以加上装饰器的参数 ，换一种说法：装饰器可以理解为 一个语法糖（ 语言自身提供,用更加简便的方式表示某种行为 ）， 就是将被装饰的函数作为参数 传递给装饰器函数

&nbsp;

```python

In [1]: import time 

In [2]: def wait_five_seconds(f):   # 接收一个函数作为参数
   ...:     # 修改这个参数 
   ...:     def wrapper():
   ...:         print "Begin--------"
   ...:         print "Please wait five seconds"
   ...:         time.sleep(5)
   ...:         f()     # 中间也可能调用这个函数
   ...:         print "Finish--------"
   ...:     return wrapper     # 然后return一个修改后的新的函数，也就是替换后的函数
   ...: 

# 调用装饰器

In [3]: @wait_five_seconds   
   ...: def say_hello(name="Word"):
   ...:     print "Hello {}".format(name)
   ...:     

In [4]: say_hello()
Begin--------
Please wait five seconds
Hello Word
Finish--------

# def say_hello(name="Word"):
# 	print "Hello {}".format(name)
# say_hello_wait = wait_five_seconds(say_hello)
# say_hello_wait

```

&nbsp;

>> 语法糖（Syntactic sugar），也译为糖衣语法，是由英国计算机科学家彼得·约翰·兰达（Peter J. Landin）发明的一个术语，指计算机语言中添加的某种语法，这种语法对语言的功能并没有影响，但是更方便程序员使用。通常来说使用语法糖能够增加程序的可读性，从而减少程序代码出错的机会。

>> 例如 ：简单来说， for、while 循环就是语法糖，就是一种很便捷写法，它的存在可以增加语言的可读性，简化一些操作。

&nbsp;

`` 使用洽到好处的装饰器 瞬间就可以让 你的代码优美很多，适当使用装饰器,（注意 适当使用，使用太多，代码反而可读性变差 ）能够有效的提高代码的可读性以及 可维护性 ``


----------


##  什么时候会用到装饰器 & 装饰器应该怎么用

### 使用场景

* 装饰器可以对函数进行预处理 & 后处理

预处理 就是我在调用函数的时间进行参数的检查，后处理 就不用提了

* 可以打印日志

得到函数的名字和函数的参数，就可以把调用的过程打印到日志里面，这样我们就知道什么时候调用过这个函数，以及调用过程中的函数参数是什么，这样的过程

`f.__name__`  得到函数名 

`*args`  位置参数
`**kwargs`   关键字参数
 
 &nbsp;
 
```python

def logging(func):
	def wrapper(*args, **kwargs):
		res = func(*args, **kwargs)
		print func.__name__,args,kwargs    # 打印函数名和参数 可以用logging 打印到日志文件里面
		return res
	return wrapper
	
@logging
def reverse_string(string):
    return str(reversed(string))
```

&nbsp;

* 计时行为

就是调用之前记录一下开始时间，函数结束之后调用一下时间，然后返回时间差，就可以知道函数执行了多少时间
如下例子：

```python

import time 

def benchmark(func):
	def wrapper(*args, **kwargs):
		t = time.clock()    # 调用钱记录开始时间
		res = func(*args, **kwargs)
		print func.__name__    #打印日志
		print time.clock() - t    # 计算用了多长时间
		return res
	return wrapper

@benchmark
def reverse_string(string):
	return str(reversed(string))
```
&nbsp;

## 注意事项

* 函数调用装饰器获得的帮助文档以及函数名字都是装饰器的帮助文档和函数名字，而不是原来函数的
想要获取原来函数的函数名或者帮助文档可以使用Python的一个标准库 ： `functools`    调用装饰器    `@functools.wraps(f)`
&nbsp;

```python

In [7]: import time 
# In [25]: import functools

In [8]: def say_hello(f):
   ...:     # @functools.wraps(f)
   ...:     def wait_five_seconds(*args, **kwargs):
   ...:         print ''' "Begin--------"
   ...: "Please wait five seconds" '''
   ...:         time.sleep(5)
   ...:         f()
   ...:         print "Finish--------"
   ...:     return wait_five_seconds    
   ...: 

In [9]: @say_hello   
   ...: def hello(name="Lmy"):
   ...:     """ Say Hello to Lmy """
   ...:     print "Hello {}".format(name)
   ...:     

In [10]: 

In [10]: hello()
Begin--------
Please wait five seconds
Hello Lmy
Finish--------

In [11]: print hello.func_doc 
None

In [12]: print hello.func_name
wait_five_seconds

# In [29]: print hello.func_doc 
#  Say Hello to Lmy 
# 
# In [30]: print hello.func_name
# hello

```
&nbsp;

* 给装饰器传递的参数：

为了给装饰器传递参数可以在 函数的外围再套一个函数，传递参数也就是说一层一层的返回
调用装饰器的时候加上所需要的参数
就比如说 Flask 的 `route()` 装饰器，告诉 Flask 什么样的 URL 能触发函数。

```python

from flask import Flask
app = Flask(__name__)

@app.route('/')
def index():
    return 'Index Page'

@app.route('/hello')
def hello():
    return 'Hello World'
    
@app.route('/user/<username>')
def show_user_profile(username):
    # show the user profile for that user
    return 'User %s' % username

@app.route('/post/<int:post_id>')
def show_post(post_id):
    # show the post with the given id, the id is an integer
    return 'Post %d' % post_id

if __name__ == "__main__":
    app.run()
```

此例中，使用  `app.route`  装饰器，完成了以下几个 URL 与处理函数的 route：  

```python
{ 
    '/': index(), 
    '/hello': hello(),
    '/user/<username>': show_user_profile(username)
    '/post/<int:post_id>' : show_post(post_id)
}
```

也就是 当  HTTP  请求的 URL 为 xxxx 时，Flask 会调用 某一个 函数；


----------


## 缺点： 

* 封装的越多可能越慢

* 装饰器装饰过的函数，不能够拆掉，也就是说一个函数经过装饰器装饰后，如果想要去掉装饰器的装饰，可能实现不了


* 装饰器封装的太多，调试很困难


----------

# 参考资料

1. https://wiki.python.org/moin/PythonDecoratorLibrary


-------

