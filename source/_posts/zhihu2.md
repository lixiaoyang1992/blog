---
title: 用react-native开发知乎日报 2
date: 2016-07-28 07:10:26
tags: react-native
category : javascript
---

本项目源码[lixiaoyang1992/zhihu](https://github.com/lixiaoyang1992/zhihu)

今天完成了欢迎页面

{% asset_img ScreenShotWelcome.png %}



<!-- more -->
### 欢迎页  

#### 网络请求

欢迎页面需要获取启动图片，对应的api https://news-at.zhihu.com/api/4/start-image/1080*1776。

建立js/api/zhihu.js

    export default {

        getStartImage(cb){

            fetch('https://news-at.zhihu.com/api/4/start-image/1080*1776')
                .then((response) => response.json())
                .then((responseJson) => {
                    cb(responseJson);
                })
                .catch((error) => {
                    console.error(error);
                });

        },

    }
