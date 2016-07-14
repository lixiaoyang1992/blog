---
title: reactjs教程—npm
date: 2016-05-17 22:29:00
tags: reactjs
category : javascript
---

## npm介绍  

NPM的全称是Node Package Manager，是一个NodeJS包管理和分发工具。

<!-- more -->
## npm创建项目  
在任意目录下执行  

        npm init

  依次输入  
  name: (lixiaoyang) react  
  version: (1.0.0) 1.0.0  
  description: a demo for react  
  entry point: (index.js)   
  test command:   
  git repository:   
  keywords:   
  author:   
  license: (ISC)     
  当然不想填的项目你也可以一路回车过去，然后在当前目录下就会生成package.json文件

        {
        "name": "react",
        "version": "1.0.0",
        "description": "a demo for react",
        "main": "index.js",
        "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
        },
        "author": "",
        "license": "ISC"
        }

这样一个项目的基础就搭建好了，你需要做的就是将你需要的模块一点点的引进来。

## Complementary Tools  

NodeJS的社区的一大特点就是提供了大量的工具供你随意挑选。在[Complementary Tools](https://github.com/facebook/react/wiki/Complementary-Tools)，有官方收集的各种支持工具，可以随意探索。
