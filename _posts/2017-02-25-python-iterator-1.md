---
layout: post
title: ' 你了解女巫们的黑魔法么？-Python 迭代器 '
tags: [IT]
description: >
  
---


> 迭代器（iterator）有时又称游标（cursor）是程序设计的软件设计模式，可在容器（container，例如链表或阵列）上遍访的接口，设计人员无需关心容器的内容。

迭代器：每次调用 实现 next 方法，返回一个结果 ，知道结束 返回 ` StopIteration  `   异常

**迭代器只能迭代一次**

很多时候我们使用的 for 循环 遍历一些 列表集合字符串 等对象，而这些对象通常就被称为可迭代对象 ，迭代器协议 就是 实现 可迭代对象的协议 
简单来说： for 循环使用了迭代器来迭代对象

```python
In [14]: a = [ 1, 2, 3, 4 ]

In [15]: max(a)
Out[15]: 4

In [16]: min(a)
Out[16]: 1

In [17]: sum(a)
Out[17]: 10

In [18]: type(a)
Out[18]: list

In [19]: b = iter(a)

In [20]: sum(b)
Out[20]: 10

In [21]: type(b)
Out[21]: listiterator

In [22]: b
Out[22]: <listiterator at 0x4a04278>

```

## 函数式的处理

`map()`  ,  `reduce()`  ，`filter()`    函数

map 就是把一些操作分给不同的机器 ，reduce 就是把操作的结果汇总

在Python中 `map`  就是对列表里面的每一项应用一个操作
`reduce`   就是把所有的结果给累计起来
`filter `   过滤的作用，map  没有

```python
a = (1, 2, 3, 4)
map(abs, (-2, 3, -4))
reduce(lambda x, y: x*y, a)
filter(lambda x: x % 2 != 0, a)


In [127]: map(abs, (-2, 3, -4))
Out[127]: [2, 3, 4]

In [130]: a = (1, 2, 3, 4)

In [131]: reduce(lambda x, y: x*y, a)
Out[131]: 24

In [132]: filter(lambda x: x % 2 != 0, a)
Out[132]: (1, 3)

```
--------------

## itertools 库 

一个实现迭代器的 库

```python
In [133]: import itertools
In [134]: itertools?
Type:        module
String form: <module 'itertools' (built-in)>
Docstring:
Functional tools for creating and using iterators.

Infinite iterators:
count([n]) --> n, n+1, n+2, ...
cycle(p) --> p0, p1, ... plast, p0, p1, ...
repeat(elem [,n]) --> elem, elem, elem, ... endlessly or up to n times

Iterators terminating on the shortest input sequence:
chain(p, q, ...) --> p0, p1, ... plast, q0, q1, ...
compress(data, selectors) --> (d[0] if s[0]), (d[1] if s[1]), ...
dropwhile(pred, seq) --> seq[n], seq[n+1], starting when pred fails
groupby(iterable[, keyfunc]) --> sub-iterators grouped by value of keyfunc(v)
ifilter(pred, seq) --> elements of seq where pred(elem) is True
ifilterfalse(pred, seq) --> elements of seq where pred(elem) is False
islice(seq, [start,] stop [, step]) --> elements from
       seq[start:stop:step]
imap(fun, p, q, ...) --> fun(p0, q0), fun(p1, q1), ...
starmap(fun, seq) --> fun(*seq[0]), fun(*seq[1]), ...
tee(it, n=2) --> (it1, it2 , ... itn) splits one iterator into n
takewhile(pred, seq) --> seq[0], seq[1], until pred fails
izip(p, q, ...) --> (p[0], q[0]), (p[1], q[1]), ...
izip_longest(p, q, ...) --> (p[0], q[0]), (p[1], q[1]), ...

Combinatoric generators:
product(p, q, ... [repeat=1]) --> cartesian product
permutations(p[, r])
combinations(p, r)
combinations_with_replacement(p, r)
```


```python

#方法：

itertools.chain		# 一次迭代多个列表，并且不会生成中间列表
itertools.combinations		# 列表元素的组合
itertools.combinations_with_replacement
itertools.compress
itertools.count		# 创建无限序列
itertools.cycle		# 重复遍历列表中的值
itertools.dropwhile		# 过滤列表中的元素， 返回True或False
itertools.groupby		# 返回一个产生按照key进行分组后的值集合的迭代器
itertools.ifilter		# 类似于列表中的filter(),但是返回的是一个循环器
itertools.ifilterfalse		# 和ifilter 函数相反 ， 返回 false 项的迭代器
itertools.imap		# 与map()函数功能相似,只不过返回的不是序列，而是一个循环器
itertools.islice		# 返回输入迭代器根据索引来选取的项
itertools.izip		# 列表连接
itertools.izip_longest		# 按照最长的列表进行连接，没有对应的就返回 None
itertools.permutations		# 所有可能的排列
itertools.product		# 笛卡尔积
itertools.repeat		# 创建返回对象多次的迭代器
itertools.starmap
itertools.takewhile
itertools.tee		# 生成多个迭代器

```

详细介绍几个常用的方法：

#### itertools.chain

一次迭代多个列表，把多个列表连接起来， 一个一个的访问， 并且不会生成中间列表

```python
In [8]: itertools.chain?
Docstring:
chain(*iterables) --> chain object

Return a chain object whose .next() method returns elements from the
first iterable until it is exhausted, then elements from the next
iterable, until all of the iterables are exhausted.
Type:      type

```

例如：

```python
In [9]: import itertools

In [10]: a = (5, 6, 7, 8)

In [11]: a
Out[11]: (5, 6, 7, 8)

In [12]: for b in itertools.chain((1, 2, 3, 4), a):
    ...:     print b
    ...:
1
2
3
4
5
6
7
8

```

#### itertools.cycle

重复遍历列表中的值 ， 有一个参数就是遍历的列表

```python

In [29]: itertools.cycle?
Docstring:
cycle(iterable) --> cycle object

Return elements from the iterable until it is exhausted.
Then repeat the sequence indefinitely.
Type:      type
```

例如：

```python
In [30]: for b in itertools.cycle( ("a1", "a2", "a3" ) ):
    ...:     print b
    ...:
a1
a2
a3
a1
a2
a3
a1
.......................................
# Ctrl + c 结束，或者指定结束

In [35]: a = 0

In [36]: for b in itertools.cycle( ("a1", "a2", "a3" ) ):
    ...:     a += 1
    ...:     if a ==10:
    ...:         break
    ...:     print (a ,b)
    ...:
(1, 'a1')
(2, 'a2')
(3, 'a3')
(4, 'a1')
(5, 'a2')
(6, 'a3')
(7, 'a1')
(8, 'a2')
(9, 'a3')

```

#### itertools.count

创建 一个无限值的 序列

```python
In [3]: itertools.count?
Docstring:
count(start=0, step=1) --> count object

Return a count object whose .next() method returns consecutive values.
Equivalent to:

    def count(firstval=0, step=1):
        x = firstval
        while 1:
            yield x
            x += step
Type:      type

```

`itertools.count`  有两个参数：
`start `  开始  默认为0
` step`  步长  默认为1 

```python
In [24]: a = itertools.count(1,1)

In [24]: a
Out[24]: count(1)

In [25]: for b in a :
    ...:     print b
    ...:
1
2
3
4
5
6
.......................................
# Ctrl + c 结束，或者指定结束

In [26]: a = itertools.count(1,1)

In [27]: a
Out[27]: count(1)

In [28]: for b in a :
    ...:     if b == 5:
    ...:         break
    ...:     print b
    ...:
1
2
3
4


```


```python

In [1]: import itertools

In [2]: a = ("a1", "a2", "a3", "a4")

In [3]: a
Out[3]: ('a1', 'a2', 'a3', 'a4')

In [4]: zip (itertools.count(1,1), a)
Out[4]: [(1, 'a1'), (2, 'a2'), (3, 'a3'), (4, 'a4')]

# 不生成列表的形式，而是返回一个迭代器对象，当循环调用的时候，循环一次生成一个元素
In [6]: itertools.izip (itertools.count(1,1), a)
Out[6]: <itertools.izip at 0x431b9c8>

In [7]: for b in itertools.izip (itertools.count(1,1), a):
   ...:     print b
   ...:
(1, 'a1')
(2, 'a2')
(3, 'a3')
(4, 'a4')

```

#### itertools.dropwhile

当函数执行时返回True的时候 迭代结束，否则继续进行迭代


```python
In [61]: itertools.dropwhile?
Docstring:
dropwhile(predicate, iterable) --> dropwhile object

Drop items from the iterable while predicate(item) is true.
Afterwards, return every element until the iterable is exhausted.
Type:      type

```


例如：

```python

In [64]: def whether_one(x):
    ...:     print "not is one", x
    ...:     return (x!=1)
    ...:

In [65]: for b in itertools.dropwhile(whether_one, [ 2, 5, -3, 1, 2, -4, 1, 0 ]):
    ...:     print "is one", b
    ...:
not is one 2
not is one 5
not is one -3
not is one 1
is one 1
is one 2
is one -4
is one 1
is one 0

```

#### itertools.ifilter

用法类似于列表里面的 ` filter( )`   函数

```python
In [86]: itertools.ifilter?
Docstring:
ifilter(function or None, sequence) --> ifilter object

Return those items of sequence for which function(item) is true.
If function is None, return the items that are true.
Type:      type

```

例如：

```python
In [83]: def whether_one(x):
    ...:     print "not is one :", x
    ...:     return (x!=1)
    ...:

In [84]: for b in itertools.ifilter(whether_one, [ 2, 5, -3, 1, 2, -4, 1, 0 ]):
    ...:     print "is one :", b
not is one : 2
is one : 2
not is one : 5
is one : 5
not is one : -3
is one : -3
not is one : 1
not is one : 2
is one : 2
not is one : -4
is one : -4
not is one : 1
not is one : 0
is one : 0
```


#### itertools.islice

类似于序列切片方式返回迭代器

```python
In [40]: itertools.islice?
Docstring:
islice(iterable, [start,] stop [, step]) --> islice object

Return an iterator whose next() method returns selected values from an
iterable.  If start is specified, will skip all preceding elements;
otherwise, start defaults to zero.  Step defaults to one.  If
specified as another value, step determines how many values are
skipped between successive calls.  Works like a slice() on a list
but returns an iterator.
Type:      type

```

例如：

```python
for a in itertools.islice(itertools.count(), 5):
    print a


In [43]: for a in itertools.islice(itertools.count(), 5):
    ...:     print a
    ...:
0
1
2
3
4

```


#### itertools.tee

由于迭代器只能迭代一次，所以使用itertools.tee可以生成多个迭代器， 默认是两个

```python
In [49]: itertools.tee?
Docstring: tee(iterable, n=2) --> tuple of n independent iterators.
Type:      builtin_function_or_method
```

例如：

```python
In [46]: a = (1, 2, 3)

In [47]: a
Out[47]: (1, 2, 3)

In [48]: a1, a2, a3 = itertools.tee(a,3)

In [49]: for b in a1:
    ...:     print b
    ...:
1
2
3

In [50]: for b in a1:
    ...:     print b
    ...:

In [51]: for b in a2:
    ...:     print b
    ...:
1
2
3

```






#### itertools.izip & itertools.izip_logest 

`itertools.izip_logest` 按照最长的列表进行连接，没有对应的就返回 None


```python
In [54]: for b in itertools.izip( ("a1", "a2", "a3"), (1, 2, 3, 4, 5)):
    ...:     print b
    ...:
('a1', 1)
('a2', 2)
('a3', 3)

In [55]: for b in itertools.izip_longest( ("a1", "a2", "a3"), (1, 2, 3, 4, 5)):
    ...:     print b
    ...:
('a1', 1)
('a2', 2)
('a3', 3)
(None, 4)
(None, 5)

```

#### itertools.permutations

列出所有可能的排列

```python
In [68]: itertools.permutations?
Docstring:
permutations(iterable[, r]) --> permutations object

Return successive r-length permutations of elements in the iterable.

permutations(range(3), 2) --> (0,1), (0,2), (1,0), (1,2), (2,0), (2,1)
Type:      type

```


例如：

```python
In [66]: a = ("a1", "a2", "a3")

In [67]: for b in itertools.permutations(a):
    ...:     print b
    ...:
('a1', 'a2', 'a3')
('a1', 'a3', 'a2')
('a2', 'a1', 'a3')
('a2', 'a3', 'a1')
('a3', 'a1', 'a2')
('a3', 'a2', 'a1')

```


#### itertools.combinations

列出所有的组合，和上面的 `itertools.permutations `  对比看

```python
In [70]: itertools.combinations?
Docstring:
combinations(iterable, r) --> combinations object

Return successive r-length combinations of elements in the iterable.

combinations(range(4), 3) --> (0,1,2), (0,1,3), (0,2,3), (1,2,3)
Type:      type
```

例如：

```python

In [74]: a = ("a1", "a2", "a3")

In [75]: for b in itertools.combinations(a, 2):
    ...:     print b
    ...:
('a1', 'a2')
('a1', 'a3')
('a2', 'a3')

In [76]: for b in itertools.combinations(a, 3):
    ...:     print b
    ...:
('a1', 'a2', 'a3')

```

#### itertools.product

笛卡尔积

第一个列表的某一项和第二个列表的某一项

```python
In [79]: itertools.product?
Docstring:
product(*iterables) --> product object

Cartesian product of input iterables.  Equivalent to nested for-loops.

For example, product(A, B) returns the same as:  ((x,y) for x in A for y in B).
The leftmost iterators are in the outermost for-loop, so the output tuples
cycle in a manner similar to an odometer (with the rightmost element changing
on every iteration).

To compute the product of an iterable with itself, specify the number
of repetitions with the optional repeat keyword argument. For example,
product(A, repeat=4) means the same as product(A, A, A, A).

product('ab', range(3)) --> ('a',0) ('a',1) ('a',2) ('b',0) ('b',1) ('b',2)
product((0,1), (0,1), (0,1)) --> (0,0,0) (0,0,1) (0,1,0) (0,1,1) (1,0,0) ...
Type:      type

```

例如：

```python
In [81]: itertools.product('ab', range(3))
Out[81]: <itertools.product at 0x44b4090>

In [82]: for b in itertools.product('ab', range(3)):
    ...:     print b
    ...:
('a', 0)
('a', 1)
('a', 2)
('b', 0)
('b', 1)
('b', 2)

```

#### itertools.repeat

创建返回对象多次的迭代器

```python

In [60]: itertools.repeat?
Docstring:
repeat(object [,times]) -> create an iterator which returns the object
for the specified number of times.  If not specified, returns the object
endlessly.
Type:      type

```

例如：

```python
In [59]: list(itertools.repeat("hello", 4))
Out[59]: ['hello', 'hello', 'hello', 'hello']

```