---
layout: post
title: ' Python 生成器 '
tags: [IT]
description: >
  
---

首先提一下lambda

### lambda

相当于很小的函数，不需要return 
 
适当使用可以写出更简洁代码结构
和函数的区别：lambda可以写在  列表解析里面，不支持语句的代码块，只允许单个返回值的表达式 

lambda 参数1， 参数2......参数n：表达式	（只能一句）

### map

对一个可迭代对象中的项，应用一个函数操作


```python
def inc(x): return x + 10


def my_map(func, seq):
	res = []
	for x in seq:
		res.append(func(x))
	return res

	
print list(map(inc, [1, 2, 3]))
print my_map(inc, [1, 2, 3])
print my_map(lambda x: x + 10, [1, 2, 3])
```


### reduce


```python

def my_reduce(func, seq):
	current = seq[0]
	for next in seq[1:]:
		current = func(current, next) # 回调函数
	return current


print my_reduce(lambda x, y: x + y, (1, 2, 3, 4))

```


### 迭代器

迭代器协议：对象提供 __next__ 方法，返回迭代中的下一项，直到    迭代完成   引发一个  `StopIteration`   异常终止迭代，  类似于  `游标`   和  `指针`  ， 可以记住上次操作结束的位置，下次调用的时候会接着进行操作下一个元素


协议是一种约定，“可迭代对象” （实现了迭代器协议的对象    比如说文件对象 ）实现迭代器协议，Python的内置工具(for循环，sum，min，max等)使用迭代器协议访问对象



### 生成器

生成器是支持迭代协议的对象，该对象提供 ` next`   方法，返回迭代中的下一项，直到    迭代完成   引发一个  `StopIteration`   异常终止迭代。

在 Python 中，有两种方法可以产生生成器

1. 用  `def+yield`  产生一个生成器函数

2. 用  圆括号  的列表解析”产生一个生成器


* 生成器函数：和普通的函数相似，但是， 普通def 函数 返回结果是  `return`  语句 ， 而 生成器函数是 用  `yield`  语句    一次返回一个结果  在每个结果之间，保存 当前 状态， 便于下次从此处继续


* 创建生成器的时候会自动实现迭代器协议， 也就是说      **生成器实现了迭代器协议**

```python
In [7]: def test_yield(a):
   ...:     for b in range(a):
   ...:         yield b
   ...:

In [8]: a = test_yield(5)

In [9]: a		# 生成器对象
Out[9]: <generator object test_yield at 0x0000000003C0F3F0>

In [12]: a.next()
Out[12]: 0

In [13]: a.next()
Out[13]: 1

# 生成器方法
# a.close      a.gi_running a.throw
# a.gi_code    a.next
# a.gi_frame   a.send


# 使用 for 循环返回生成器里面的结果
In [8]: for c in test_yield(5):
   ...:     print c
   ...:
0
1
2
3
4

```

* * 生成器表达式：和  列表解析  相似 ，列表解析是 一次返回一个结果的列表 ， 但是，生成器按照操作 需要一次返回一个结果，而不是一个结果列表 

如果值很大的情况下适合使用生成器

生成器表达式的写法：

```python
# 生成器表达式
In [14]: ( a*a for a in range(5) )
Out[14]: <generator object <genexpr> at 0x0000000003D2E900>
# In [15]: list( a*a for a in range(5) )
# Out[15]: [0, 1, 4, 9, 16]


# 列表解析：
In [17]: [ a*a for a in range(5) ]
Out[17]: [0, 1, 4, 9, 16]

```

#### 生成器的优点

可以用于处理  数据量大的列表

* 当输入的数据量较大时，列表推导可能会因为占用太多内存而出现问题

* 由生成器表达式所返回的迭代器，可以逐次产生输出值，从而避免了内存用量问题

#### 生成器缺点

* 生成器只能迭代一次


```python

def normalize(numbers):
	total = sum(numbers)
	result = []
	for value in numbers:
		percent = 100 * value / total
		result.append(percent)
	return result


def read_visits(path):
	with open(path) as f:
		for line in f:
			yield int(line)



it = read_visits('/tmp/mydata.txt')
percentages = normalize(it)
print(percentages)

>>> []

```
