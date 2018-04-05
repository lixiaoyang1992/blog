---
title: react 中的数据
date: 2018-04-04 14:14:26
tags: react javascript
---

<!-- more -->

# state

state 是组件自身的状态。不允许直接修改，只能通过 setState 更新.

setState 会将旧的状态和新的合并，所以在调用时是需要将更新的数据传入就行。

为了性能，setState 函数会将多个修改做合并成一次修改，所以根据 state 原有状态而来的变更应当使用函数来修改，如

    this.setState((prevState, props) => ({
      counter: prevState.counter + props.increment
    }));

# props

props 是父组件传入子组件的参数。

# context

context 是储存在祖先组件内的数据。
