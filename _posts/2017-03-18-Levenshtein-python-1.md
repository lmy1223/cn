---
layout: post
title: ' 超好玩的编辑距离 '
tags: [IT]
comments = true
date = 2014-08-09T05:04:40Z 
description: >
  
---

## 编辑距离

编辑距离（Edit Distance），又称Levenshtein距离，是指两个字串之间，由一个转成另一个所需的最少编辑操作次数。许可的编辑操作包括将一个字符替换成另一个字符，插入一个字符，删除一个字符。一般来说，编辑距离越小，两个串的相似度越大。

> 最小编辑距离通常作为一种相似度计算函数被用于多种实际应用中

* DNA分析
* 拼写纠错

> 又拼写检查（Spell Checker），将每个词与词典中的词条比较，英文单词往往需要做词干提取等规范化处理，如果一个词在词典中不存在，就被认为是一个错误，然后试图提示N个最可能要输入的词——拼写建议。常用的提示单词的算法就是列出词典中与原词具有最小编辑距离的词条。

* 命名实体抽取

> 由于实体的命名往往没有规律，如品牌名，且可能存在多种变形、拼写形式，如“IBM”和“IBM Inc.”，这样导致基于词典完全匹配的命名实体识别方法召回率较低，为此，我们可以使用编辑距离由完全匹配泛化到模糊匹配，先抽取实体名候选词。

* 实体共指
* 机器翻译
* 字符串核函数

许可的编辑操作包括：将一个字符替换成另一个字符，插入一个字符，删除一个字符。

例如将eeba转变成abac：

eba（删除第一个e）
aba（将剩下的e替换成a）
abac（在末尾插入c）
所以eeba和abac的编辑距离就是3


1.  1. 最上和最左相等为左上方数字，不等则为左上方加一
	2.  左方加一
	3.  上方加一

上面三个取最小值

2. 取右下角值 为 最小编辑距离

speak   &     safe

|     # |   #   |   s  |   p  |  e  |  a  |  k  |
| :--: | :--: | :--: | :--: | :--: | :--: | :--: | 
| # | 0 | 1 | 2 | 3 | 4 | 5 |
|s|1|0|1|2|3|4|
|a|2|1|1|2|2|3|
|f|3|3|2|2|3|3|
|e|4|4|3|2|3|4|

所以  speak   &     safe  的编辑距离是4


python 通俗的实现方式：

```python
# 定义初始矩阵，初始为0
In [1]: len_str1 = 3

In [2]: len_str2 = 4

In [3]: [0 for n in range(len_str1 * len_str2)]
Out[3]: [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]

In [4]: len([0 for n in range(len_str1 * len_str2)])
Out[4]: 12

```

```python

def edit(str1, str2):
    len_str1 = len(str1) + 1
    len_str2 = len(str2) + 1
    # 定义一个矩阵
    matrix = [0 for n in range(len_str1 * len_str2)]
    # 初始化
    for x in range(len_str1):
        matrix[x] = x
    for y in range(0, len(matrix), len_str1):
        if y % len_str1 == 0:
                matrix[y] = y // len_str1
    '''
    1.  1. 最上和最左相等为左上方数字，不等则为左上方加一
        2.  左方加一
        3.  上方加一

    上面三个取最小值
    '''
    for x in range(1, len_str1):
        for y in range(1, len_str2):
            if str1[x - 1] == str2[y - 1]:
                cost = 0
            else:
                cost = 1
            matrix[y * len_str1 + x] = min(matrix[(y - 1) * len_str1 + x] + 1,
                                           matrix[y * len_str1 + (x - 1)] + 1,
                                           matrix[(y - 1) * len_str1 + (x - 1)] + cost)

    '''2. 取右下角值 为 最小编辑距离'''
    return matrix[-1]


if __name__ == '__main__':
    i = edit('speak', 'safe')
    print i
```

## 附上一小部分拼写检查

```python
# 字母拼写的所有可能的结果
In [11]: word = "ab"

In [12]: letters = 'abcdefghijklmnopqrstuvwxyz'

In [13]: splits = [(word[:i], word[i:]) for i in range(len(word) + 1)]
    ...: # 删除
    ...: deletes = [L + R[1:] for L, R in splits if R]
    ...: # 调换位置
    ...: transposes = [L + R[1] + R[0] + R[2:] for L, R in splits if len(R) > 1
    ...: ]
    ...: # 取代字母
    ...: replaces = [L + c + R[1:] for L, R in splits if R for c in letters]
    ...: # 添加字母
    ...: inserts = [L + c + R for L, R in splits for c in letters]
    ...:

In [17]: set(deletes + transposes + replaces + inserts)
    ...:
Out[17]:
{'a',
 'aa',
 'aab',
 'ab',
 'aba',
 'abb',
 'abc',
 'abd',
 'abe',
 'abf',
 'abg',
 'abh',
 'abi',
 'abj',
 'abk',
 'abl',
 'abm',
 'abn',
 'abo',
 'abp',
 'abq',
 'abr',
 'abs',
 'abt',
 'abu',
 'abv',
 'abw',
 'abx',
 'aby',
 'abz',
 'ac',
 'acb',
 'ad',
 'adb',
 'ae',
 'aeb',
 'af',
 'afb',
 'ag',
 'agb',
 'ah',
 'ahb',
 'ai',
 'aib',
 'aj',
 'ajb',
 'ak',
 'akb',
 'al',
 'alb',
 'am',
 'amb',
 'an',
 'anb',
 'ao',
 'aob',
 'ap',
 'apb',
 'aq',
 'aqb',
 'ar',
 'arb',
 'as',
 'asb',
 'at',
 'atb',
 'au',
 'aub',
 'av',
 'avb',
 'aw',
 'awb',
 'ax',
 'axb',
 'ay',
 'ayb',
 'az',
 'azb',
 'b',
 'ba',
 'bab',
 'bb',
 'cab',
 'cb',
 'dab',
 'db',
 'eab',
 'eb',
 'fab',
 'fb',
 'gab',
 'gb',
 'hab',
 'hb',
 'iab',
 'ib',
 'jab',
 'jb',
 'kab',
 'kb',
 'lab',
 'lb',
 'mab',
 'mb',
 'nab',
 'nb',
 'oab',
 'ob',
 'pab',
 'pb',
 'qab',
 'qb',
 'rab',
 'rb',
 'sab',
 'sb',
 'tab',
 'tb',
 'uab',
 'ub',
 'vab',
 'vb',
 'wab',
 'wb',
 'xab',
 'xb',
 'yab',
 'yb',
 'zab',
 'zb'}

In [18]:

```

```python
def edits1(word):
    letters = 'abcdefghijklmnopqrstuvwxyz'
    # 字母分割
    splits = [(word[:i], word[i:]) for i in range(len(word) + 1)]
    # 删除
    deletes = [L + R[1:] for L, R in splits if R]
    # 调换位置
    transposes = [L + R[1] + R[0] + R[2:] for L, R in splits if len(R) > 1]
    # 取代字母
    replaces = [L + c + R[1:] for L, R in splits if R for c in letters]
    # 添加字母
    inserts = [L + c + R for L, R in splits for c in letters]
    return set(deletes + transposes + replaces + inserts)

# 由于我们需要修正要检查的单词。所以需要一个生成器，用来进行迭代时返回值，这里用生成器表达式的形式解析
def edits2(word):
    return (e2 for e1 in edits1(word) for e2 in edits1(e1))

```

```python
# -*- coding:utf-8 -*-


def edits1(word):
    letters = 'abcdefghijklmnopqrstuvwxyz'
    # 字母分割
    splits = [(word[:i], word[i:]) for i in range(len(word) + 1)]
    # 删除
    deletes = [L + R[1:] for L, R in splits if R]
    # 调换位置
    transposes = [L + R[1] + R[0] + R[2:] for L, R in splits if len(R) > 1]
    # 取代字母
    replaces = [L + c + R[1:] for L, R in splits if R for c in letters]
    # 添加字母
    inserts = [L + c + R for L, R in splits for c in letters]
    return set(deletes + transposes + replaces + inserts)


def edits2(word):
    return (e2 for e1 in edits1(word) for e2 in edits1(e1))



if __name__ == '__main__':
    # edits1('study')
    i = edits2('study')
    print i


'''
结果是一个生成器
<generator object <genexpr> at 0x00000000029AD120>
'''
```

```python
# -*- coding:utf-8 -*-

import re
from collections import Counter


def words(text):
    return re.findall(r'\w+', text.lower())

# 统计出现的单词
WORDS = Counter(words(open('big.txt').read()))


# word_list = {}
# with open('big.txt') as f:
#     for line in f:
#         line = line.replace(",", " ")
#         line = line.replace(".", " ")
#         line = line.replace("!", " ")
#         words = line.split()
#         for word in words:
#             if word_list.has_key(word):
#                 word_list[word] += 1
#             else:
#                 word_list[word] = 1
#     result = sorted(word_list.items(), key=lambda k: k[1], reverse=True)


def edits1(word):
    letters = 'abcdefghijklmnopqrstuvwxyz'
    # 字母分割
    splits = [(word[:i], word[i:]) for i in range(len(word) + 1)]
    # 删除
    deletes = [L + R[1:] for L, R in splits if R]
    # 调换位置
    transposes = [L + R[1] + R[0] + R[2:] for L, R in splits if len(R) > 1]
    # 取代字母
    replaces = [L + c + R[1:] for L, R in splits if R for c in letters]
    # 添加字母
    inserts = [L + c + R for L, R in splits for c in letters]
    return set(deletes + transposes + replaces + inserts)


def edits2(word):
    return (e2 for e1 in edits1(word) for e2 in edits1(e1))

# 在已知的单词中进行匹配
def known(words):
    return set(w for w in words if w in WORDS)


if __name__ == '__main__':
    # edits1('study')
    # i = edits2('study')
    # print i
    a = known(edits2('studa'))
    print a


'''
结果
set(['tula', 'stump', 'sta', 'sturdy', 'sudan', 'stung', 'studio', 'stuck', 'stuff', 'stead', 'squad', 'stud', 'soda', 'souza', 'study', 'shuya'])

'''
```

### 参考文章：

1. [http://norvig.com/spell-correct.html](http://norvig.com/spell-correct.html)
