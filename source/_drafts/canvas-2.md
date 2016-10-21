layout: post
title: Canvas学习笔记（二）
comment: true
date: 2016-01-23 15:46:00
tags: [Canvas,技术,Html5,笔记] 
---

## 线条绘制
- 单线条绘制
```javascript
//状态设置
context.moveTo(100,100);
context.lineTo(700,700);
context.lineWidth = 10;
context.strokeStyle = "#058";
//绘制
context.stroke();
```
- 多线条绘制
```javascript
context.beginPath();
//...绘制线条（其他图形）
context.closePath();
```

<!-- more -->

- 绘制矩形
```javascript
// 设置路径
context.rect(x,y,width,height);
// stroke矩形
context.strokeRect(x,y,width,height);
// fill矩形
context.fillRect(x,y,width,height);
```

### fillStyle && strokeStyle
color = `#ffffff` `#642` `rgb( 255 , 128 , 0 )` `rgba( 100 , 100 , 100 , 0.8 )` `hsl( 20 , 62% , 28% )` `hsla( 40 , 82% , 33% , 0.6 )` `red`

## 线条的属性
- lineWidth
设置线条的宽度
- lineCap
线条的端点样式：butt(default)、round、square
- lineJoin
两条线段的拼接方式：miter(default)、bevel、round
    - miterLimit:对于设置lineJoin属性为miter可以指定miterLimit的值

## 图形变换
- 位移
```javascript
context.translate(x,y);
```
相对于(0,0)的位移
- 旋转
```javascript
context.rotate(deg);
```
相对于当前位置顺时针旋转
- 缩放
```javascript
context.scale(sx,sy);
```
整体缩放（lineWidth、路径坐标...）
- canvas状态的保存和恢复
图形变换的效果会产生叠加，所以在进行变换前先保存当前状态，**变换后，进行绘图**，恢复原来的状态。
```javascript
context.save();
//...变换&绘图
context.restore();
```
- 使用变换矩阵

```math
[ [a,c,e],
  [b,d,f],
  [0,0,1] ]
```

a 水平缩放(1) b 水平倾斜(0) c 垂直倾斜(0) d 垂直缩放(1) e 水平位移(0) f 垂直位移(0)
```javascript
context.transform(a,b,c,d,e,f);
// 不叠加效果设置transform
context.setTransform(a,b,c,d,e,f);
```

## 填充样式
- 线性渐变
```javascript
//Step 1
var grd = context.createLinearGradient(xstart,ystart,xend,yend);
//Step 2
grd.addColorStop(stop,color);

//e.g.
var linearGrad = context.createLinearGradient(0,0,600,0);
linearGrad.addColorStop(0.0,"#fff");
linearGrad.addColorStop(0.2,"yellow");
linearGrad.addColorStop(0.4,"blue");
linearGrad.addColorStop(1.0,"#000");
context.fillStyle = linearGrad;
context.fillRect(0,0,600,600);
```
- 径向渐变
```javascript
var radialGrad = context.createRadialGradient(100,100,200,300,300,700);
radialGrad.addColorStop(0.0,"#fff");
radialGrad.addColorStop(0.2,"yellow");
radialGrad.addColorStop(0.4,"blue");
radialGrad.addColorStop(1.0,"#000");
context.fillStyle = radialGrad;
context.fillRect(0,0,600,600);
```
- 图片或者纹理填充
    - createPattern( img , repeat-style )
    - createPattern( canvas , repeat-style )
    - createPattern( video , repeat-style )
    - repeat-style:no-repeat repeat-x repeat-y repeat

```javascript
var bgImg = new Image();
bgImg.src = "paw.png";
bgImg.onload = function(){
	var pattern = context.createPattern(bgImg,"repeat-x");
	context.fillStyle = pattern;
	context.fillRect(0,0,600,600);
}
```

```javascript
var bgCanvas = createBgCanvas();
var pattern = context.createPattern(bgCanvas,"repeat");
context.fillStyle = pattern;
context.fillRect(0,0,600,600);
function createBgCanvas(){
	var bgCanvas = document.createElement("canvas");
	bgCanvas.width = 100;
	bgCanvas.height = 100;
	var bgCanvasContext = bgCanvas.getContext("2d");
	drawStar(bgCanvasContext,50,50,50,0);
	return bgCanvas;
}
```
fillStyle = `color` `gradient` `image` `canvas` `video`

PS: strokeStyle与fillStyle类似

## 曲线的绘制
- 绘制普通的圆弧
```javascript
context.arc(
	centerx, centery, radius,
	startingAngle, endingAngle,
	anticlockwise = false
)
```
- 绘制与两条直线相切的圆弧
```javascript
//圆弧与直线(x0,y0,x1,y1)、直线（x1,y1,x2,y2)相切
//圆弧的起点是(x0,y0),但不一定是切点;(x2,y2)不一定是终点，终点是切点
context.moveTo(x0,y0);
context.arcTo(x1 , y1 , x2 , y2 ,radius );
```
- 绘制贝塞尔曲线 Bezier
```javascript
//一个控制点
context.moveTo( x0 , y0 );
context.quadraticCurveTo( x1, y1, x2, y2 );
//两个控制点
context.moveTo( x0 , y0 );
context.bezierCurveTo( x1, y1, x2, y2, x3, y3 );
```

## 文字渲染
- 渲染基础
```javascript
context.font = "bold 40px Arial"
context.fillText( string , x , y , [maxlen] );
context.strokeText( string , x , y  , [maxlen] );
```
- font设置
默认值：`"20px sans-serif"`;
context.font = font-style font-variant font-weight font-size font-family

- font属性解析
font-style:normal(default) italic oblique
font-variant:normal(default) small-caps
font-weight:lighter normal(default) bold bolder 100-900
font-size:20px(default) 2em 150% x-small large 
font-family:@font-face web安全字体

- 文本对齐
context.textAlign= left/center/right;
context.textBaseLine = top/middle/bottom/alphabetic/ideographic/hanging
拉丁语言：alphabetic(default);
日语和中文等方块字：ideographic
印度文：hanging

- 文本度量
```javascript
var textWidth = context.measureText("hello World!").width;
context.fillText("the width of the string is " + textWidth + "px",300,700);
```

## 其他
- shadow
```javascript
context.shadowColor = "gray";
context.shadowOffsetX = -20;
context.shadowOffsetY = 20;
context.shadowBlur = 10;
```
- global全局属性
```javascript
context.globalAlpha = 0.8;
//0-1.0 default:1
context.globalCompositeOperation = "lighter";
//source-over source-atop source-in source-out
//destination-over destination-atop destination-in destination-out
//lighter copy xor
```
- 剪辑区域
```javascript
//根据当前设置的路径对canva进行剪辑，只绘制剪辑后的区域
context.clip();
```

- 封闭区域的着色（非零环绕原则）
- 清除矩形区域内的绘制
```javascript
context.clearRect( x , y , width , height )
```
- 判断某点是否在绘制区域内
```javascript
context.isPointInPath( x , y )
```

PS:
Canvas与IE6、7、8等浏览器的兼容性问题
explorecanvas
[https://code.google.com/p/explorercanvas/](https://code.google.com/p/explorercanvas/)

Canvas图形库
canvasplus
[https://code.google.com/p/canvasplus/](https://code.google.com/p/canvasplus/)

Artisan JS
[http://artisanjs.com/](http://artisanjs.com/)

Rgraph
[https://roopons.com.au/wp-content/plugins/viral-optins/js/rgraph/](https://roopons.com.au/wp-content/plugins/viral-optins/js/rgraph/)
