layout: post
title: Python学习笔记（一）
comment: true
date: 2016-04-05 15:31:50
tags: [Python,技术,笔记] 
---
## 1.将常量集中到一个文件中
将存放常量的文件命名为constant.py，并在其中定义一系列常量
```python
class _const:
    class ConstError(TypeError): pass
    class ConstCaseError(ConstError): pass
    
    def __setattr__(self,name,value):
        if self.__dict__.has_key(name):
            raise self.ConstError, "Can't change const. %s" % name
        if not name.isupper():
            raise self.ConstCaseError, \
            'const name "%s" is not all uppercase' % name
        self.__dict__[name] = value

import sysy
sys.modules[__name__] = _const()
import const
const.MY_CONSTANT = 1
const.MY_SECOND_CONSTANT = 2
const.MY_THIRD_CONSTANT = 'a'
```
当在其他模块中引用这些变量时，按照如下方式进行即可
```
from constant import const
print const.MY_CONSTANT
print const.MY_SECOND_CONSTANT*2
```

<!-- more -->
## 2.充分利用Lazy evaluaion的特性
对于or条件表达式应该将值为真可能性较好的变量写在or前面，而and则应该推后——对于Python中的条件表达式if x and y，在x为false的情况下y表达式的值将不再计算。而对于if x or y，当x的值为true的时候，将直接返回，不再计算y的值。

## 3.浮点数准确吗？
```python
i = 1
while i!=1.5:
    i = i + 0.1
    print i
```
上面的代码会导致无限不循环，原因是浮点数的存储规则决定了不是所有的浮点数都能准确表示，有些事不准确的，只是无限接近。如0.1转换为二进制表示形式为0.000110011001···在内存中根据浮点数的位数规定，多余部分直接阶段，因此当循环到底5次的时候i的实际值为1.5000000000000004，则条件表达式i!=1.5显然为True，循环陷入无终止状态。
如果计算对精度要求较高，可以使用Decimal来进行处理或者将浮点手尽量扩大为整数，计算完毕后在转换回去。对于在while中使用i!=1.5这种条件表达式也要避免，浮点数的比较最好能够指明精度。

## 警惕eval()的安全漏洞
Python中的eval()函数将字符串str当成有效地表达式来求值并返回计算结果。
```python
eval('__import__("os").system("dir")')
# 将输入当前所在目录的所有文件
```
如果使用对象不是信任源，应该尽量避免使用eval，在需要使用eval的地方可用安全性更好的ast.literal_eval替代

## 使用enumerate()获取序列迭代的索引和值
```python
li = ['a','b','c','d','e']
for i ,e in enumerate(li):
    print "index:",i,"element:",e
```

## 分清==与is的使用场景
```
a = "HI"
b = "HI"
a is b  # True
a == b  # False

a1 = "I am using long string for testing"
b1 = "I am using long string for testing"
a1 is b1  # False
a1 == b1  # True

str1 = "string"
str2 = "".join(['s','t','r','i','n','g'])
str1 is str2  # False
str1 == str2  # True
```
两种操作的意义

| 操作符 | 意义 |
|--------|------|
| is | object identity |
| == | equal |
is的作用是用来检查对象的标示符是否相等。x is y仅当x和y是同一个对象的时候才会返回True，相当于id(x) == id(y)。==是用来检验两个对象的值是否相等的，实际调用的是内部`__eq__()`方法，所以==操作符可以被重载，而is不能被重载。
