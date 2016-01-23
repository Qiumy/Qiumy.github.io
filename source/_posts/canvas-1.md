layout: post
title: Canvas学习笔记（一）
comment: true
date: 2016-01-21 10:51:52
tags: [Canvas,技术,Html5,笔记] 
---
## Canvas
- HTML
```javascript
<canvas id="canvas"><canvas>
```
- Javascript
```javascript
var canvas = document.getElementById('canvas');
var context = canvas.getContext('2d')
//使用context进行绘制
```
<!-- more -->

- 相关接口
```javascript
canvas.width
canvas.height
canvas.getContext('2d')
```

## Context的相关接口
- 绘制直线和弧线
```javascript
context.moveTo(x,y);
context.lineTo(x,y);
context.arc(cx,cy,r,sAng,eAng,anticlock=false);
```
- 重新开始一段路径或者结束当前路径
```javascript
context.beginPath();
context.closePath();
```
- canvas 根据当前状态进行绘图
```javascript
context.lineWidth
context.strokeStyle
context.fillStyle

context.stroke();
context.fill();
```
- 对画布上的矩形区域进行刷新
```javascript
context.clearRect(x,y,width,height);
```
- 找到上下文绘制环境对应的画布
```javascript
context.canvas
```

## 其他
- Animation
```javascript
setInterval(
    function(){
        render();//画面的渲染和绘制
        update();//底层数据更新
    },50
);
```