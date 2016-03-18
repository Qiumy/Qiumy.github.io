layout: post
title: CSS学习笔记（一）之float和position
comment: true
date: 2016-03-18 09:40:00
tags: [CSS,技术,float,position,笔记] 
---

## float
float属性主要用于设置元素的浮动,浮动于被包含容器的左边或者右边
```css
{float:left | right | none ;}
```
被设置了float属性的元素会脱离正常的文档流，直到遇到容器边缘或者另一个设置了float属性的元素
### 清除float
- 方法一(不推荐)

```css
.parent{clear:both;}/* So dirty! */
```

<!-- more -->

- 方法二

```css
.parent{
    overflow: hidden; /* Clearfix! */
    zoom: 1;  /* Triggering "hasLayout" in IE */
    display: block; /* Element must be a block to wrap around contents. Unnecessary if only using block-level elements. */
}
```

- 方法三

```css
.parent:after{
    content:"";
    display:block;
    clear:both;
}
```

## position
```css
{position:static | relative | absolute | fixed ;}
```
- static
这个关键字使得这个元素使用正常的表现，即元素处在文档流中它当前的布局位置，top, right, bottom, left 和 z-index 属性无效。

- relative
使用这个关键字来布局元素就好像这个元素没有被设置过定位一样。即会适应该元素的位置，并不改变布局（这样会在此元素原本所在的位置留下空白）。**相对于原来位置进行偏移**position:relative对table-*-group, table-row, table-column, table-cell, table-caption无效。

- absolute
不为元素预留空间，元素位置通过指定其与它最近的非static定位的祖先元素的偏移来确定。绝对定位的元素可以设置外边距（margins），并且不会与其他边距合并。**脱离文档流**

- fixed
不为元素预留空间。通过指定相对于屏幕视窗的位置来指定元素的空间，并且该元素的位置在屏幕滚动时不会发生改变。打印时元素会出现在的每页的固定位置。fixed属性通常会创建新的栈环境。

## float和position:absolute
设置float属性的元素可以通过一些方法撑开父元素，但是设置position：absolute的元素则没有办法撑开设置了position：relative的父元素。
但是，大多数时候（上面说到的那种情况例外），绝对定位元素的 height 和 width 属性的值为 auto，它们会自动计算以适合元素的内容。

### 参考资料
[MDN:position](https://developer.mozilla.org/zh-CN/docs/Web/CSS/position)
[MDN:float](https://developer.mozilla.org/en-US/docs/Web/CSS/float)