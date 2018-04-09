---
title: react-dom源码解析-1初探
date: 2017-12-01 17:06:17
tags: react
category : javascript
---

react-dom是负责将react渲染的包，分为浏览器渲染和服务器渲染。
浏览器渲染的内容在/src/client/ReactDOM。
服务器渲染的内容在/src/server/ReactDOMServerNode。

# ReactDOM

ReactDOM对象里有很多方法。

    const ReactDOM: Object = {
      createPortal,
      findDOMNode
      hydrate
      render
      unstable_renderSubtreeIntoContainer
      unmountComponentAtNode
      unstable_createPortal
      unstable_batchedUpdates
      unstable_deferredUpdates
      flushSync
      __SECRET_INTERNALS_DO_NOT_USE_OR_YOU_WILL_BE_FIRED
    }

先从最熟悉的render看起。

# render

我们都会在应用的入口里面写一句

    ReactDOM.render(<App />, document.getElementById('root'));

render的内容如下

    render(
      element: React$Element<any>,
      container: DOMContainer,
      callback: ?Function,
    ) {
      return legacyRenderSubtreeIntoContainer(
        null,
        element,
        container,
        false,
        callback,
      );
    },

在legacyRenderSubtreeIntoContainer中，首先检查container是否是有效,

