layout: post
title: Python学习笔记（二）
comment: true
date: 2016-04-08 19:26:59
tags: [Python,技术,笔记] 
---
## 1.`__init__()`和`__new__()`方法
- `__new__()`方法是静态方法，而`__init__()`为实例方法
- `__new__()`方法一般需要返回类的对象，当返回类的对象十五讲自动调用`__init__()`方法进行初始化，如果没有类的对象放回，则`__init__()`方法不会被调用
- 当需要控制实例创建的时候可使用`__new__()`方法，而控制实例初始化的时候使用`__init__()`方法
- 一般情况下不需要覆盖`__new__()`方法，但当子类集成自不可变类型，如`str`、`int`或者`tuple`的时候，往往需要覆盖该方法

<!--more-->

## 2.迭代器
迭代器对象要求支持迭代器协议的对象，在Python中，支持迭代器协议就是实现对象的`__iter__()`和`next()`方法，前者返回的是一个迭代器，后者返回当前的元素，并指向下一个元素的位置。
```python
class Fib(object):
	def __init__(self):
		self._a = 0
		self._b = 1
	def __iter__(self):
		return self
	def next(self):
		self._a,self._b = self._b, self._a + self._b
		return self._a
for i,f in enumerate(Fib()):
	print f
	if i > 10:
		break
```
python中可用的迭代器的标准库有itertools

## 3.生成器
生成器，按照一定的算法生成一个序列。生成器实现了迭代器协议，可以一定程度上看做迭代器。Python中有专门的关键字`yield `实现生成器。
```python
def fib(n):
    a,b = 1,1
    while a < n:
        yield a
        a,b = b,a+b

for i, f in enumerate(fib(10)):
    print f
```
递归生成器的例子
```python
def permutations(li):
    if len(li) == 0:
        yield li
    else:
        for i in range(len(li)):
            li[0], li[i] = li[i], li[0]
            for item in permutations(li[1:]):
                yield [li[0]] + item

for item in permutations(range(3)):
    print item
```

## 参考资料
[编写高质量代码：改善Python程序的91个建议](https://book.douban.com/subject/25910544/)