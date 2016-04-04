layout: post
title: '通配符和正则表达式'
comment: true
date: 2016-04-04 20:06:02
tags: [通配符,正则表达式,JavaScript] 
---
## 通配符
在某些命令中，使用通配符创建一个模式匹配表达是，指定多个对象。使用的通配符取决于发出命令时所处于的操作系统。
例如可使用通配符（例如星号*）匹配任意个字符，或者可使用问号（？）或者百分号（%）精确匹配一个字符。

## 正则表达式
使用单个字符串来描述、匹配一系列符合某个句法规则的字符串。正则表达式的用处：1）验证字符串是否符合指定特征 2）用来查找字符串，从一个长的文本中查找符合指定特征的字符串 3）用来替换

<!-- more -->

## JavaScript中的正则表达式
### 创建一个正则表达式
```javascript
var re = /ab+c/
var re = new RegExp("ab+c")
```
### 编写一个正则表达式的模式
- 简单模式
有你找的直接匹配所构成。例如`/abc/`这个模式匹配了在一个字符串中，仅字符'abc'同时出现并按照这个顺序
- 使用特殊字符
搜素一个比直接匹配需要更多条件的匹配。例如`/ab*c/`匹配了一个单独的'a'后面跟了零个或者多个b，且后面跟着'c'的任何字符的组合
- 使用插入语
任何使用正则表达是的插入语都会使这部分匹配的副字符串被记忆。一旦被记忆，这个副字符就可以被调用于其他用途。比如，`/Chapter (\d+)\.\d*/`解释了额外转义的和特殊的字符，并说明了这部分pattern应该被记忆。它精确地匹配后面跟着一个以上数字字符的字符 'Chapter '  (\d 意为任何数字字符，+意为1次以上)，跟着一个小数点（在这个字符中本身也是一个特殊字符；小数点前的 \ 意味着这个pattern必须寻找字面字符'.')，跟着任何数字字符0次以上。 (\d 意为数字字符， * 意为0次以上)。另外，插入语也用来记忆第一个匹配的数字字符。
### 使用正则表达式
使用正则表达式的方法

| 方法 | 描述 |
| ------ | ------ |
| exec | 一个在字符串中执行查找匹配的RegExp方法，它返回一个数组（未匹配到则返回null）。 |
| test | 一个在字符串中测试是否匹配的RegExp方法，它返回true或false。|
| match | 一个在字符串中执行查找匹配的String方法，它返回一个数组或者在未匹配到时返回null。|
| search | 一个在字符串中测试匹配的String方法，它返回匹配到的位置索引，或者在失败时返回-1。|
| replace | 一个在字符串中执行查找匹配的String方法，并且使用替换字符串替换掉匹配到的子字符串。|
| split | 一个使用正则表达式或者一个固定字符串分隔一个字符串，并将分隔后的子字符串存储到数组中的String方法。|

例如，使用exec查找一个匹配
```javascript
var myRe = /d(b+)d/g;
var myArray = myRe.exec("cdbbdbsbz");

var myRe = new RegExp("d(b+)d", "g");
var myArray = myRe.exec("cdbbdbsbz");
```
另外一个例子，
```javascript
var myRe = /d(b+)d/g;
var myArray = myRe.exec("cdbbdbsbz");
console.log("The value of lastIndex is " + myRe.lastIndex);
//The value of lastIndex is 5

var myArray = /d(b+)d/g.exec("cdbbdbsbz");
console.log("The value of lastIndex is " + /d(b+)d/g.lastIndex);
//The value of lastIndex is 0
```
#### 使用括号的字符串匹配
使用括号匹配的子字符串的数量是无限的。返回的数组中保存所有被发现的子匹配。
下面的脚本使用replace()方法来转换字符串中的单词。在匹配到的替换文本中，脚本使用替代的$1,$2表示第一个和第二个括号的子字符串匹配。
```javascript
var re = /(\w+)\s(\w+)/;
var str = "John Smith";
var newstr = str.replace(re,"$2, $1");
console.log(newstr);
// Simth John
```
#### 使用标志进行高级搜索
正则表达式标志

|标志|描述|
|----|----|
|g|全局搜索|
|i|不区分大小写搜索|
|m|多行搜索|
|y|执行“粘贴”搜索，匹配从目标字符串的当前位置，可以使用y标志|
例如，re = /\w+\s/g 将创建一个查找一个或多个字符后有一个空格的正则表达式，或者组合起来像此要求的字符串。
```javascript
var re = /\w+\s/g;
// var re = new RegExp("\\w+\\s","g");
var str = "fee fi fo fum";
var myArray = str.match(re);
console.log(myArray);
//["fee","fi","fo"]
```
## 参考资料
[正则表达式](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions#使用特殊字符)