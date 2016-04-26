layout: post
title: Python函数——map()和reduce()
comment: true
date: 2016-04-14 19:56:10
tags: [Python, 技术, 笔记] 
---
# `map()`函数
`map()`函数接收两个参数，一个是函数，另外一个是迭代器，`map`的作用就是将传入的函数一次作用到序列的每个元素，并把结果作为新的迭代器返回。
```python
# 使用map
print map(lambda x:x%3,range(6))              # [0,1,2,0,1,2]
# 使用列表解析
print [x%3 for x in range(6)]                 # [0,1,2,0,1,2]


# 将字符串转成list
print map(str, [1, 2, 3, 4, 5, 6, 7, 8, 9])   # ['1', '2', '3', '4', '5', '6', '7', '8', '9']

# 多个迭代器
print map(lambda x,y: x*y,[1,2,3],[4,5,6])    # [4,10,18]

print map(lambda x,y: (x*y, x+y),[1,2,3],[4,5,6] )  # [(4, 5), (10, 7), (18, 9)]
```
<!-- more -->

# `reduce()`函数
`reduce()`函数也接收两个参数，一个是函数，一个是迭代器；这个函数必须接收两个参数，`reduce`的作用就是把结果和序列的下一个元素做累积计算。
`reduce( func, [1, 2,3] ) = func( func(1, 2), 3)`
```python
# 阶乘
n = 5
print reduce(lambda x, y: x * y, range(1, n + 1))  # 120

# 2倍阶乘
m = 2
n = 5
print reduce( lambda x, y: x * y, range( 1, n + 1 ), m )  # 240

# 把序列[1, 3, 5, 7, 9]变换成整数13579
from functools import reduce  #python 3 之后需要这样引用
def fn(x, y):
    return x * 10 + y
    reduce(fn, [1, 3, 5, 7, 9])
```
一道可使用`reduce()`函数解答的Leetcode
[https://leetcode.com/problems/single-number/](https://leetcode.com/problems/single-number/)

二者结合的例子,实现str转int
```python
from functools import reduce

def char2num(s):
    return {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}[s]

def str2int(s):
    return reduce(lambda x, y: x * 10 + y, map(char2num, s))
```

## 参考资料
[map/reduce](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014317852443934a86aa5bb5ea47fbbd5f35282b331335000)
[Python函数式编程——map()、reduce()](http://www.pythoner.com/46.html)