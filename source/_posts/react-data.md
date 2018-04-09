---
title: react 中的数据
date: 2018-04-04 14:14:26
tags: react
category : javascript
---

本文基于 react16.3 讲一下 state,props,context 三种数据使用方式。

<!-- more -->

# state

state 是组件自身的状态。不允许直接修改，只能通过 setState 更新.

setState 会将旧的状态和新的合并，所以在调用时是需要将更新的数据传入就行。

为了性能，setState 函数会将多个修改做合并成一次修改，所以根据 state 原有状态而来的变更应当使用函数来修改，如

    this.setState((prevState, props) => ({
      counter: prevState.counter + props.increment
    }));

# props

props 用于父组件向子组件传入参数。props 也是只读的。

props 可以用 prop-types 进行类型校验。

# context

context 用于跨越多级组件传递数据。

在 16.3 中，推出了新的 context API。

## legacy

旧版的使用需要定义 childContextTypes 和 getChildContext 来设置和获取 context。
react-redux，react-router 都使用这个 API。

如果没有定义 childContextTypes，context 就是一个空对象。

## 16.3

新版中加入了 React.createContext API，更优雅的实现了数据的传递。

### React.createContext

    const {Provider, Consumer} = React.createContext(defaultValue);

生成的 Provider, Consumer 用来传递 context。

### Provider

    <Provider value={/* some value */}>

Provider 包裹在 Consumer 的外部，供其订阅 context 的变化。  
一个 Provider 可以对应多个 Consumer。

### Consumer

    <Consumer>
      {value => /* render something based on the context value */}
    </Consumer>

Consumer 组件订阅了 context 的变化。  
Consumer 需要一个函数作为 child。这个函数收到 context 作为参数，返回一个 react 节点。  
Consumer 会寻找最近的 Provider 里面的 value 使用。  
当没有 Provider 时，createContext 中的 defaultValue 是 Consumer 获取到的 context。
