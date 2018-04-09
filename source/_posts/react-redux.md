---
title: react-redux源码分析
date: 2016-12-22 13:07:46
tags: react
category : javascript
---

redux要和react结合使用，官方出品的[reactjs/react-redux](https://github.com/reactjs/react-redux)是必不可少的。

一共只有2个api：Provider, connect，非常的精简。
本文基于4.x
<!-- more -->
# Provider

Provider是一个react Component，作用就是把store作为ChildContext,这样后代组件也可以拿到。

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
        //children只能是一个节点
        return Children.only(this.props.children)
      }
    }

Provider必须声明childContextTypes

    Provider.propTypes = {
      store: storeShape.isRequired,
      children: PropTypes.element.isRequired
    }
    Provider.childContextTypes = {
      store: storeShape.isRequired
    }

而store的必需项是对应的redux store的subscribe、dispatch和getState。

    export default PropTypes.shape({
      subscribe: PropTypes.func.isRequired,
      dispatch: PropTypes.func.isRequired,
      getState: PropTypes.func.isRequired
    })

# connect

connect接收四个参数mapStateToProps、mapDispatchToProps、mergeProps和options，返回一个“接收一个组件作为参数，返回一个高阶组件”的函数。

mapStateToProps默认值

    const defaultMapStateToProps = state => ({})

或者

    const mapStateToProps = state => ({
      loginState: state.loginReducer,
    });

用于从store中获取想要的一部分state

mapDispatchToProps默认值

    const defaultMapDispatchToProps = dispatch => ({ dispatch })

或者

    const mapDispatchToProps = dispatch => bindActionCreators(action, dispatch);

mergeProps默认值

    const defaultMergeProps = (stateProps, dispatchProps, parentProps) => ({
      ...parentProps,
      ...stateProps,
      ...dispatchProps
    })