---
title: reactjs－开始写项目
date: 2016-05-23 23:09:01
tags: reactjs
category : javascript
---

## Starter Kit  

再回到我们的购物车项目（这个项目会一直作为我们的Starter Kit）。

## 程序入口

我们在执行 npm start 命令的时候，实际上是执行了 node server.js。这是在package.json中配置的。  
<!-- more -->
    "scripts": {
    "start": "node server.js",
    "test": "cross-env NODE_ENV=test mocha --recursive --compilers js:babel-register",
    "test:watch": "npm test -- --watch"
    },

  我们还可以执行一下测试命令 npm test

  在 server.js (这里涉及express，暂时不管)中  

      app.get("/", function(req, res) {
      res.sendFile(\__dirname + '/index.html')
      })

  这段代码指定了‘／’请求返回index.html页面。

## 单页应用   

我们要做的是一个单页应用（SPA），所以html页面只有一个就够了  

    <!DOCTYPE html>
    <html>
    <head>
    <title>Redux shopping cart example</title>
    </head>
    <body>
    <div id="root">
    </div>
    <script src="/static/bundle.js"></script>
    </body>
    </html>

## Redux  

在Facebook的定义中react只是Just the UI，要解决数据流、各个组件之间交互的问题，官方提出了FLUX解决方案，我们选择了其中一种实现——redux。  
其核心思想是保持数据的单向流动，主要包含三个部分dispatcher、store和views。  
正常的使用场景是：我们或view发起一个action，产生或者获取一些数据，然后由dispatcher分发，在store更新和维护数据，再根据store来渲染UI。  

这里我们先按照官方的指导写好最外层的代码。

    import 'babel-polyfill'
    import React from 'react'
    import { render } from 'react-dom'
    import { createStore, applyMiddleware } from 'redux'
    import { Provider } from 'react-redux'
    import logger from 'redux-logger'
    import thunk from 'redux-thunk'
    import reducer from './reducers'
    import { getAllProducts } from './actions'
    import App from './containers/App'

    const middleware = process.env.NODE_ENV === 'production' ?
      [ thunk ] :
      [ thunk, logger() ]

    const store = createStore(
      reducer,
      applyMiddleware(...middleware)
    )

    store.dispatch(getAllProducts())

    render(
      <Provider store={store}>
        <App />
      </Provider>,
      document.getElementById('root')
    )

这里的Provider，store等内容，稍后讲到。

## container 与 component  

我们常把视图分为容器和组件两部分，为了让视图更加的清晰。

在容器中我们定义各种方法,统一维护各种组件的状态；在组件里，只负责显示UI和可以被复用就可以了。

明确容器和组件各自的职责非常重要！！！
