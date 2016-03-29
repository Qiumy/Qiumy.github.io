layout: post
title: ASCII Unicode UTF-8概念
comment: true
date: 2016-03-29 10:51:41
tags: [编码,Unicode,UTF-8,笔记] 
---

## ASCII
用8位表示一个字符，最高位为0，所以真正有效地为7位，共定义了128个字符，其中33个字符无法显示；例如A表示为0100 0001（65），1表示为0011 0001（49）。

## EASCII
将ASCII有7位扩展成8位，即最高位可以为1，则最多可以表示256个字符。

<!-- more -->

## Unicode
因为上面两种的编码方案字符数量有所限制，面对不同的语言，需要更多的字符编码去表示，所以就有了Unicode。其一直处于更新阶段，旨在将世界上所有的付好都纳入其中，给予每个符号独一无二的编码。
Unicode只是一个符号集，但是不同的符号所需要的字节数目却各不相同，这就导致了一个问题：如何区分ASCII和Unicode？计算机怎么知道三个字节表示一个符号，而不是表示三个不同的符号？另外，我们知道英文字符只使用一个字节就足够了，但是若Unicode规定，每个符号都用三个或者四个字节表示，则在存储方面将造成极大地浪费。

## UTF-8
UTF-8是互联网上使用最广的Unicode的实现方式，其他的实现方式还有UTF-16和UTF-32。
UTF-8最大的特点就是可变长的编码方式，可以使用1~4个字节表示一个符号，根据不同的符号改变字节长度。
UTF-8的编码规则如下：
1）对于单字节的符号，字节的第一位设为0，后面7位为这个符号的unicode码。因此对于英语字母，UTF-8编码和ASCII码是相同的。
2）对于n字节的符号（n>1），第一个字节的前n位都设为1，第n+1位设为0，后面字节的前两位一律设为10。剩下的没有提及的二进制位，全部为这个符号的unicode码。

## 参考资料
[字符编码笔记：ASCII，Unicode和UTF-8](http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html)
[十分钟搞清字符集和字符编码](http://cenalulu.github.io/linux/character-encoding/)
[程序员趣味读物：谈谈Unicode编码](http://pcedu.pconline.com.cn/empolder/gj/other/0505/616631_1.html)
