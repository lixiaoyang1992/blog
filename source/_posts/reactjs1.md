---
title: reactjs教程-Demo体验
date: 2016-05-17 22:25:50
tags: reactjs
category : javascript
---

这是一系列介绍reactjs＋redux+react-router等开发的教程

## 准备  

  读到这篇文章的可能是一个萌萌的妹子，她可能还有点天然呆，所以要讲得更细一点。  

  首先她要安装以下几个软件:

  [git](https://git-scm.com/download/) 最新的就可以   
[node.js](https://nodejs.org/en/) 4.4.4LTS版本

## Demo体验  

  先下几个demo，体验一下react的神奇。

  执行

        git clone git@github.com:ruanyf/react-demos.git

  就可以在demo中感受react的与众不同了。

## npm  

  项目是需要构建的，我们做项目的时候，肯定不能像做demo一样直接静态文件引入就完成了的。这里介绍的是npm，npm用于构建项目，可以调试、测试、发布等等，由于它很灵活，你可以用它做很多事情。

  这里我们也直接clone一个starter project

  执行  

    git clone git@github.com:reactjs/redux.git
    cd examples/shopping-cart/
    npm install
    npm start

  npm主要的配置文件是package.json  


      {
        "name": "redux-shopping-cart-example",
        "version": "0.0.0",
        "description": "Redux shopping-cart example",
        "scripts": {
          "start": "node server.js",
          "test": "cross-env NODE_ENV=test mocha --recursive --compilers js:babel-register",
          "test:watch": "npm test -- --watch"
        },
        "repository": {
          "type": "git",
          "url": "https://github.com/reactjs/redux.git"
        },
        "license": "MIT",
        "bugs": {
          "url": "https://github.com/reactjs/redux/issues"
        },
        "homepage": "http://redux.js.org",
        "dependencies": {
          "babel-polyfill": "^6.3.14",
          "react": "^0.14.7",
          "react-dom": "^0.14.7",
          "react-redux": "^4.2.1",
          "redux": "^3.2.1",
          "redux-thunk": "^1.0.3"
        },
        "devDependencies": {
          "babel-core": "^6.3.15",
          "babel-loader": "^6.2.0",
          "babel-preset-es2015": "^6.3.13",
          "babel-preset-react": "^6.3.13",
          "babel-preset-react-hmre": "^1.1.1",
          "cross-env": "^1.0.7",
          "enzyme": "^2.0.0",
          "expect": "^1.20.1",
          "express": "^4.13.3",
          "json-loader": "^0.5.3",
          "react-addons-test-utils": "^0.14.7",
          "redux-logger": "^2.0.1",
          "mocha": "^2.2.5",
          "node-libs-browser": "^0.5.2",
          "webpack": "^1.9.11",
          "webpack-dev-middleware": "^1.2.0",
          "webpack-hot-middleware": "^2.9.1"
        }
      }

  我们通过以上文件大概可以了解配置的信息。

  我们在 [http://localhost:3000](http://localhost:3000) 访问到你刚刚启动的项目，是一个简单的购物车页面。


  参考链接：[React 入门实例教程](http://www.ruanyifeng.com/blog/2015/03/react.html) by 阮一峰
