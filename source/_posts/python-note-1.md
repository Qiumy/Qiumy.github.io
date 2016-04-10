layout: post
title: Python学习笔记（一）
comment: true
date: 2016-04-08 15:31:50
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
上面的代码会导致无限不循环，原因是浮点数的存储规则决定了不是所有的浮点数都能准确表示，有些事不准确的，只是无限接近。如0.1转换为二进制表示形式为0.000110011001···在内存中根据浮点数的位数规定，多余部分直接截断，因此当循环到底5次的时候i的实际值为1.5000000000000004，则条件表达式i!=1.5显然为True，循环陷入无终止状态。
如果计算对精度要求较高，可以使用Decimal来进行处理或者将浮点手尽量扩大为整数，计算完毕后在转换回去。对于在while中使用i!=1.5这种条件表达式也要避免，浮点数的比较最好能够指明精度。

## 4.警惕eval()的安全漏洞
Python中的eval()函数将字符串str当成有效地表达式来求值并返回计算结果。
```python
eval('__import__("os").system("dir")')
# 将输入当前所在目录的所有文件
```
如果使用对象不是信任源，应该尽量避免使用eval，在需要使用eval的地方可用安全性更好的ast.literal_eval替代

## 5.使用enumerate()获取序列迭代的索引和值
```python
li = ['a','b','c','d','e']
for i ,e in enumerate(li):
    print "index:",i,"element:",e
```

## 6.分清==与is的使用场景
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

## 7.Python中的字符串
1. 进行源文件编码声明
```python
# 三种声明方式

# coding=<encoding name>

# !/usr/bin/python
# -*- coding: <encoding name> -*-

# !/usr/bin/python
# vim: set fileencoding=<encoding name> :
```
2. 使用+操作符来进行字符串的连接是，如`"中文测试" + u"Chinese Test"`，左边为中文字符串，类型为str，右边为Unicode字符串。Python中默认的编码是ASCII编码，当编码值在0~127的时候Unicode和ASCII是兼容的，但当其值大于128的时候，ASCII编码便不能正常处理这种情况，解决方法有以下两种
- 指定str转为Unicode时的编码方式
```python
# coding=utf-8
s = "中文测试".decode('gbk')+u"Chinese Test"
```
- 将Unicode字符串进行UTF-8编码
```python
s = "中文测试" + u"Chinese Test".encode("utf-8")
```

## 8.使用with自动关闭资源
```python
with 表达式 [as 目标]
    代码块
    
with oepn('test.txt','w') as f:
    f.write('test"
```

## 9.避免finally中可能发生的陷阱
```python
def returnTest(a):
    try:
        if a<=0:
            raise ValueError("data can not be negative")
        else:
            return a
    except ValueError as e:
        print e
    finally:
        print "The End!"
        return -1
```
程序returnTest(0)和returnTest(2)的返回值都为-1.第一个调用returnTest(0)在抛出ValueError异常后直接执行finally语句，返回值为-1；第二个调用returnTest(2)会执行else分支，但是由于**存在finally语句，在执行else语句的return 啊之前会先执行finally中的语句return -1，程序就直接返回了**。

## 10.sort()和sorted()
```python
sorted(iterable[, cmp[,key[,reverse]]])
s.sort([cmp[,key[,reverse]]])
```
- cmp为用户定义的任何比较函数，函数的参数为两个可比较的元素
- key是一个带参数的函数，用来为每个元素提取比较值
- reverse表示排序结果是够反转

## 11.lambda函数
```python
map(lambda x:x+1,[1,2,3])

a = [1,2,3]
r = []
for each in a:
    r.append(each+1)
```

## 12.python的深拷贝和浅拷贝
```python
a = [1,2,3]
b = a
b.append(4)  #a = [1,2,3,4], b = [1,2,3,4]
```
复制操作b=a，是浅拷贝，当b的值发生改变时，a的值也改变
```python
a = [1,2,3]
c = [i for i in a]
c.append(4)   #a = [1,2,3] c = [1,2,3,4]
```
上面的例子是深拷贝吗？
```python
a = [1,2,[3,4]]
d = [i for i in a]  # 等同于 d = a[:]
d[-1].append(5) #a = [1,2,[3,4,5]] d = [1,2,[3,4,5]] 
```
使用for循环操作时候，把a中的每个对象的引用拷贝，a中的前3个元素是字符串对象，在python中，字符串对象复制操作是：变量指向存放字符串的位置，实际上还是浅拷贝
```python
In [25]: a = [1,2,[3,4,5]]

In [26]: c = a[:]

In [27]: map(lambda x:id(x),a)
Out[27]: [6580408L, 6580384L, 63220936L]

In [28]: map(lambda x:id(x),c)
Out[28]: [6580408L, 6580384L, 63220936L]
```
可以看到a和c中每个对象的地址都是一样的
```python
import copy
a = [1,2,[3,4]]
e = copy.deepcopy(a)
e[-1].append(5)   # a = [1,2,[3,4]] e = [1,2,[3,4,5]]
```
上面的例子就是深拷贝

## 参考资料
[编写高质量代码：改善Python程序的91个建议](https://book.douban.com/subject/25910544/)