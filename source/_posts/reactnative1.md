---
title: reactnative1
date: 2016-05-19 23:32:00
tags: react-native
category : javascript
---

对于现在的我来说，有两个数据需要记录：体重和金钱，但是这通常需要两个app来实现，一个记账类的一个健康类的，对于我来说很不方便，所以我决定自己来做一个。

### 技术选型  

既然决定要做了,要覆盖多平台，那当然要用react来全栈开发。

### react-native  

Facebook推出的跨平台的用于构建原生应用的框架。

详细的安装教程在[react-native](https://facebook.github.io/react-native/releases/next/docs/getting-started.html#content)可以找到，光房的教程最近更新了，对应不同的系统都有。

### init

初始化项目  

    react-native init Record

    react-native run-ios
    react-native run-android

这样一个基本的demo跑起来了。

### 参考项目

当然是[f8app](https://github.com/fbsamples/f8app)。  
这是Facebook最近开源的f8的源代码，在其中我们可以学到很多官方推荐的最佳实践。
