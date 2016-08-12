---
title: 用react-native开发知乎日报 1.搭建项目
date: 2016-07-24 18:45:48
tags: react-native
category : javascript
---

本项目源码:[lixiaoyang1992/zhihu](https://github.com/lixiaoyang1992/zhihu)

项目参考了[f8 app](https://github.com/fbsamples/f8app) 的设计。  

所有 API 均由 知乎（Zhihu.Inc） 提供，参考开源项目[izzyleung/ZhihuDailyPurify](https://github.com/izzyleung/ZhihuDailyPurify/wiki/%E7%9F%A5%E4%B9%8E%E6%97%A5%E6%8A%A5-API-%E5%88%86%E6%9E%90)，获取与共享之行为或有侵犯知乎权益的嫌疑。若被告知需停止共享与使用，本人会及时删除此页面与整个项目。  
请您了解相关情况，并遵守知乎协议。

<!-- more -->
### 一份代码，两个平台  

既然要用react－native了，android和iOS当然要只写一份代码了。  

初始化项目后，根目录创建js文件夹，作为我们的源码文件夹。

首先要统一首页，创建js/index.js  

    import React, {Component} from 'react';
    import {
        AppRegistry,
        StyleSheet,
        Text,
        View
    } from 'react-native';

    export default class zhihu extends Component {
        render() {
            return (
                <View style={styles.container}>
                    <Text style={styles.welcome}>
                        Welcome to React Native!
                    </Text>
                    <Text style={styles.instructions}>
                        To get started, edit index.android.js
                    </Text>
                    <Text style={styles.instructions}>
                        Double tap R on your keyboard to reload,{'\n'}
                        Shake or press menu button for dev menu
                    </Text>
                </View>
            );
        }
    }

    const styles = StyleSheet.create({
        container: {
            flex: 1,
            justifyContent: 'center',
            alignItems: 'center',
            backgroundColor: '#F5FCFF',
        },
        welcome: {
            fontSize: 20,
            textAlign: 'center',
            margin: 10,
        },
        instructions: {
            textAlign: 'center',
            color: '#333333',
            marginBottom: 5,
        },
    });

就是把初始代码挪了个位置。

把index.ios.js和index.android.js都改为

    import index from './js/index'
    import {
        AppRegistry
    } from 'react-native';

    AppRegistry.registerComponent('zhihu', ()=>index);

这样连个平台就使用同一份代码了。

### redux  

react要和官方推出的flux架构结合使用，redux是一种非常优秀的实现，在之前的文章中介绍过如何使用。
