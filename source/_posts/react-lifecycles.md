---
title: react 生命周期
date: 2018-04-05 19:48:07
tags: react
category : javascript
---

为了异步渲染，在 16.3 及之后的更新中，生命周期函数将有一些巨大的改动。  
dan 在 twitter 上贴出了一张图来理解最新的生命周期。

{% asset_img img1.jpg %}

<!-- more -->

如图，一个 react 组件的生命周期主要分为三个部分：Mounting、Updating、Unmounting。  
Mounting，从创建到第一次显示在页面上的过程。  
Updating，更新的过程，会多次的执行。  
Unmounting，组件从页面上销毁的过程。

# virtual dom 与 dom

首先要明确 react 组件的 render 返回是虚拟节点，在 react-dev-tools 中看到的是 virtual dom tree，所以在图的左侧分为三个阶段。

## Render Phase

这个阶段就是根据组件的 state、props 或 context 渲染出 virtual dom。中途是没有副作用，不允许做 fetch 等操作。

## Pre-Commit Phase

在 render 之后，已经可以拿到了 dom 了，但是还没有到更新到浏览器上。

## Commit Phase

组件已经被渲染到浏览器上了，可以拿到真实的 dom 和 refs 等，也可以做一些副作用，准备组件的更新了。

# Mounting

## constructor

# Unmounting

## componentWillUnmount

组件销毁前唯一的生命周期，这里通常做一些任务销毁，资源释放之类的操作。  
这里一定要将一些异步的任务终止，否则容易报空。
