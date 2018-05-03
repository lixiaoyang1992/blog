---
title: canvas 笔记
date: 2018-05-03 18:00:41
tags: javascript
category : javascript
---

canvas;标签  
canvas.getContext("2d");获取画笔

context.moveTo(100, 100);坐标移动  
context.lineTo(700, 100);画线

context.ineWidth;线宽  
context.strokeStyle;颜色  
context.stroke();画

context.beginPath();开始新路线  
context.closePath();结束

context.fillStyle;填充样式  
context.fill();填充

context.rect(x,y,width,height);画矩形

## 线条属性概述

线条的属性共有以下四个：

#### 1、lineCap属性

lineCap 定义上下文中线的端点，可以有以下 3 个值。

* butt：默认值，端点是垂直于线段边缘的平直边缘。
* round：端点是在线段边缘处以线宽为直径的半圆。
* square：端点是在选段边缘处以线宽为长、以一半线宽为宽的矩形。

#### 2、lineJoin属性

lineJoin 定义两条线相交产生的拐角，可将其称为连接。在连接处创建一个填充三角形，可以使用 lineJoin 设置它的基本属性。

* miter：默认值，在连接处边缘延长相接。miterLimit 是角长和线宽所允许的最大比例(默认是 10)。
* bevel：连接处是一个对角线斜角。
* round：连接处是一个圆。

#### 3、线宽

lineWidth 定义线的宽度(默认值为 1.0)。

#### 4、笔触样式

strokeStyle 定义线和形状边框的颜色和样式。
