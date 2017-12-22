---
title: immutablejs 介绍
date: 2017-12-18 14:20:24
tags: javascript
---

你是否有过类似的困扰

    const oldArray=[1,2];
    const newArray=oldArray;
    newArray.push(3);
    oldArray===newArray // true
    const othorArray=oldArray.concat([3]);
    oldArray===othorArray // false

js中的比较是引用比较，要判断两个数组是否相等要么做深比较，或者大量的使用...扩展运算符创建新的对象。这里就需要一种不可变数据，比如在push之后就返回一个新的数组对象。

不可变数据鼓励使用纯函数，更易用，支持函数式编程。
面向对象的API，与array，map，set等相似，从简单对象从转换过来很方便。

