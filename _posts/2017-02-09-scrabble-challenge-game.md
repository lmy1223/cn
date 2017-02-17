---
layout: post
title: 'Python实现拼字游戏的挑战'
tags: [jekyll]
description: >
  这是一个拼字的小游戏，也不能说是游戏吧，反正就是一个看上去好玩的东西，下面就是它的说明。

  >>写一个Python脚本，以拼字游戏作为命令行参数的脚本和 打印所有有效的拼字，可以由这个脚本，连同他们的拼的字按照分数列出来，示例调用和输出为一下内容

---



<!--more-->


```
$ python scrabble.py ZAEFIEE
17 feeze
17 feaze
16 faze
15 fiz
15 fez
12 zee
12 zea
11 za
6 fie
6 fee
6 fae
5 if
5 fe
5 fa
5 ef
2 ee
2 ea
2 ai
2 ae
```


URL = [http://wiki.openhatch.org/Scrabble_challenge][1]

[1]: http://wiki.openhatch.org/Scrabble_challenge

这上面有具体的要求和步骤：有兴趣的可以好好看看，里面说明了要实现什么样的功能，以及分析每一步都需要做什么，然后你可以根据他的问题自己实现这个功能。

# 准备工作

## 1、 word list

" http://courses.cms.caltech.edu/cs11/material/advjava/lab1/sowpods.zip contains all words in the official SOWPODS word list, one word per line. "

要有一个源文件也就是word list，下面给出了这个word list 的下载地址。

[点此处下载 sowpods.zip][2]

[2]: http://courses.cms.caltech.edu/cs11/material/advjava/lab1/sowpods.zip

文件里面的单词我大概截取了一小小小部分，大概是这个样子的

```
AA
AAH
AAHED
AAHING
AAHS
AAL
AALII
AALIIS
AALS
AARDVARK
AARDVARKS
AARDWOLF
AARDWOLVES
AARGH
AARRGH
AARRGHH
AARTI
AARTIS
AAS
AASVOGEL
AASVOGELS
AB
ABA
ABAC
ABACA
ABACAS
ABACI

```

## 2、 letters and values （字母和值）

" Here is a dictionary containing all letters and their Scrabble values "

内容如下：

```python
scores = {"a": 1, "c": 3, "b": 3, "e": 1, "d": 2, "g": 2,
         "f": 4, "i": 1, "h": 4, "k": 5, "j": 8, "m": 3,
         "l": 1, "o": 1, "n": 1, "q": 10, "p": 3, "s": 1,
         "r": 1, "u": 1, "t": 1, "w": 4, "v": 4, "y": 4,
         "x": 8, "z": 10}
```


----------


# 解决问题

## 第一步 construct a word list

" Write the code to open and read the sowpods word file. Create a list, where each element is a word in the sowpods word file. Note that each line in the file ends in a newline, which you'll need to remove from the word. "

大概就是说

>> 写代码来打开并且读 sowpods Word 文件。创建一个列表，其中每个元素都是 sowpods Word 文件里面的单词。请注意，我们需要删除在文件里面的每一个换行符。

具体的步骤请参考上面的链接。

```python
# 以小写的形式读出来，并且去除换行符
def get_word_to_list(file_name):
    word_list = []
    with open(file_name, 'r') as f:
        for line in f:
            lines = line.lower()
            word_list.append(lines.strip('\n'))
    return word_list
```





## 第二步 get the rack

" Write the code to get the Scrabble rack (the letters available to make words) from the command line argument passed to your script. For example if your script were called `scrabble_cheater.py`, if you ran python scrabble_cheater.py RSTLNEI, RSTLNEI would be the rack.
Handle the case where a a user forgets to supply a rack; in this case, print an error message saying they need to supply some letters, and then exit the program using the exit() function. Make sure you are consistent about capitalization "

>>说白了就是运行脚本的后面要加上你要操作的单词作为参数，如果没有就打印错误信息，并且退出。


```python
def get_argument_word(word_list):
    if len(sys.argv) == 2:
        rack = sys.argv[1]
        lower_word = rack.lower()
        c = coll.Counter(lower_word)
        return [word for word in word_list if not (coll.Counter(word) - c)]
    else:
        print('Usage: scrabble_change.py ABC')
        sys.exit()
```





## 第三步 find valid words

"Write the code to find all words from the word list that are made of letters that are a subset of the rack letters. There are many ways to do this, but here's one way that is easy to reason about and is fast enough for our purposes: go through every word in the word lishe word in a valid_words list. Make sure you handle repeat letters: once a letter from the rack has been used, it can't be used again."

>> 写的代码要查找所有单词表里面的单词，这些单词都是由字母构成的，然后检查每一个单词表里面符合的单词，确定重复的字母，一旦这个字母被用到了，就不会再次被用到。







## 第四步 scoring

"Write the code to determine the Scrabble scores for each valid word, using the scores dictionary from above."


>> 写代码 用上面的成绩字典 来确定每个有效词的拼字游戏的分数。

```python
def get_scores(words):
    word_dic = coll.defaultdict(int)
    scores = {"a": 1, "c": 3, "b": 3, "e": 1, "d": 2, "g": 2,
              "f": 4, "i": 1, "h": 4, "k": 5, "j": 8, "m": 3,
              "l": 1, "o": 1, "n": 1, "q": 10, "p": 3, "s": 1,
              "r": 1, "u": 1, "t": 1, "w": 4, "v": 4, "y": 4,
              "x": 8, "z": 10}
    for word in words:
        for s in word:
            word_dic[word] += scores[s]
    return word_dic

```


##完整代码

```python
# !/usr/bin/python
# -*- coding: UTF-8 -*-


from __future__ import print_function
import sys
import collections as coll


# lower
def get_word_to_list(file_name):
    word_list = []
    with open(file_name, 'r') as f:
        for line in f:
            lines = line.lower()
            word_list.append(lines.strip('\n'))
    return word_list


def get_argument_word(word_list):
    if len(sys.argv) == 2:
        rack = sys.argv[1]
        lower_word = rack.lower()
        c = coll.Counter(lower_word)
        return [word for word in word_list if not (coll.Counter(word) - c)]
    else:
        print('Usage: scrabble_change.py ABC')
        sys.exit()


def get_scores(words):
    word_dic = coll.defaultdict(int)
    scores = {"a": 1, "c": 3, "b": 3, "e": 1, "d": 2, "g": 2,
              "f": 4, "i": 1, "h": 4, "k": 5, "j": 8, "m": 3,
              "l": 1, "o": 1, "n": 1, "q": 10, "p": 3, "s": 1,
              "r": 1, "u": 1, "t": 1, "w": 4, "v": 4, "y": 4,
              "x": 8, "z": 10}
    for word in words:
        for s in word:
            word_dic[word] += scores[s]
    return word_dic


# def get_scores(*args):
#     dict_set1 = {}  
#     if 'key' not in dict_set1:  
#         dict_set1['key'] = set()  
#     scores = {"a": 1, "c": 3, "b": 3, "e": 1, "d": 2, "g": 2,
#               "f": 4, "i": 1, "h": 4, "k": 5, "j": 8, "m": 3,
#               "l": 1, "o": 1, "n": 1, "q": 10, "p": 3, "s": 1,
#               "r": 1, "u": 1, "t": 1, "w": 4, "v": 4, "y": 4,
#               "x": 8, "z": 10}
#     for word in args:
#         for s in word:
#             total_scores += scores[s]
#     return total_scores, word


def main():
    word_list = get_word_to_list('sowpods.txt')
    valid_words = get_argument_word(word_list)
    d = get_scores(valid_words)
    for key, val in sorted(d.items(), key=lambda item: item[1], reverse=True):
        print(val, key)


if __name__ == '__main__':
    main()

```

解决几处问题：

```python
def get_word_to_list(file_name):
    word_list = []
    with open(file_name, 'r') as f:
        for line in f:
            lines = line.lower()
            word_list.append(lines.strip('\n'))
    return word_list
```

上面的代码是把文件里面的单词全部转换为小写读到数组里面，但是文件很大，比如我只需要列出几个或者几十个单词，并不需要把所有的单词都列出来

像这样
```python
$ python scrabble.py ZAEFIEE
17 feeze
17 feaze
16 faze
15 fiz
15 fez
12 zee
12 zea
11 za
6 fie
6 fee
6 fae
5 if
5 fe
5 fa
5 ef
2 ee
2 ea
2 ai
2 ae
```

所以，可以改正一下，就是把输出的单词全部以小写的形式表现出来。

## 更改一番


```python
# !/usr/bin/python
#-*- coding: UTF-8 -*-

from __future__ import print_function
import sys
from collections import Counter


scores = {"a": 1, "c": 3, "b": 3, "e": 1, "d": 2, "g": 2,
         "f": 4, "i": 1, "h": 4, "k": 5, "j": 8, "m": 3,
         "l": 1, "o": 1, "n": 1, "q": 10, "p": 3, "s": 1,
         "r": 1, "u": 1, "t": 1, "w": 4, "v": 4, "y": 4,
         "x": 8, "z": 10}

def get_word_list(file_name):
    word_list = []
    with open(file_name) as f:
        for line in f:
            word_list.append(line.strip('\n'))
    return word_list

def get_valid_words(word_list, rack):
    c = Counter(rack)
    return [ word for word in word_list if not (Counter(word) - c) ]

def lower_valid_words(words):
    return [ word.lower() for word in words ]

def get_scores(words):
    d = {}
    for word in words:
        d[word] = sum(scores[c] for c in word)
    return d

def main():
    if len(sys.argv) != 2:
        raise SystemExit('Usage: scrabble_change.py RACK')

    rack = sys.argv[1]

    word_list = get_word_list('sowpods.txt')
    valid_words = get_valid_words(word_list, rack.upper())

    valid_words = lower_valid_words(valid_words)

    d = get_scores(valid_words)
    for val, key in sorted(zip(d.values(), d.keys()), reverse=True):
        print(val, key)

if __name__ == '__main__':
    main()

```


----------


#关于作者

```python
  {
    author  : "NULL&ERROR",
    site : "www.mingyueli.com/2017/02/scrabble-challenge-game.html"
  }
```