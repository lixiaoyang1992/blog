---
title: react-redux源码分析
date: 2016-12-22 13:07:46
tags: reactjs
category : javascript
---

redux要和react结合使用，官方出品的[reactjs/react-redux](https://github.com/reactjs/react-redux)是必不可少的。

一共只有3个api：Provider, connectAdvanced, connect，非常的精简。
<!-- more -->
# Provider

Provider是一个react Component，作用就是调用connect()是可以使用store。

    export default class Provider extends Component {
      //使全局都能拿到唯一的store
      getChildContext() {
        return { store: this.store }
      }

      constructor(props, context) {
        super(props, context)
        this.store = props.store
      }

      render() {
        return Children.only(this.props.children)
      }
    }

Provider store和children是必需的props。

# connect

connect的作用是把一个react component和store关联起来。


# connectAdvanced

connectAdvanced
