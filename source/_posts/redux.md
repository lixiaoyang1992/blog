---
title: redux源码分析
date: 2016-12-18 22:51:25
tags: reactjs
category : javascript
---

如果你使用过react，那你应该听说过或用过另一个库[reactjs/redux](https://github.com/reactjs/redux)。

react的核心思想之一是根据state来渲染页面，而state应该是immutable的，redux很好的维护了state。

redux的api一共只用5个，可以说是相当的简洁。
<!-- more -->
# createStore

    function createStore(reducer, preloadedState, enhancer)

创建一个redux store来维护state，唯一的修改数据的方法就是dispatch()。
store在应用中应该只有一份，这是reudx和flux的区别，使得整个架构更加的精简。

reducer：根据现有的state和action，来生成一个新的state。
如果你有多个reducer，应该使用combineReducers合并成一个。

preloadedState：初始化的state。

enhancer：对store处理的增强，用来拓展第三方的支持，比如用applyMiddleware加载中间件，react-redux就是一个非常优秀的用来和react结合的中间件。

随后声明了一些变量和函数，并且return

    return {
        dispatch,
        subscribe,
        getState,
        replaceReducer,
        [$$observable]: observable
    }

在之前dispatch({ type: ActionTypes.INIT })，做了一个初始化的操作。

## dispatch

在dispatch方法中，唯一的参数是action，action应该是一个简单对象，type属性是必备的，其他的数据部分是可选的。
dispatch方法首先对action进行了检查，然后是isDispatching的判断，这里做了一个简单的锁，调用了currentReducer来根据currentState和action来生成新的state。
然后是listeners监听方法的调用。
return结果是action本身。

其余的几个函数没有被调用，是供外部调用的。

## subscribe

检查参数listener是否为函数。
ensureCanMutateNextListeners拷贝currentListeners到nextListeners。
将参数listener push 到 nextListeners。
返回的是将参数listener 从 nextListeners去除的函数。

## getState

返回currentState。

## replaceReducer

顾名思义，用nextReducer来替换currentReducer，然后dispatch({ type: ActionTypes.INIT })。
可用于多次添加reducer，不需一次性初始化所有。

## observable

返回一个订阅函数，用于外部订阅state的变化。

# combineReducers

combineReducers也只有一个参数reducers，常常是这种形式,一个巨大的对象：

    combineReducers({
        myReducer:reducer,
        offlineOrder
    })

它的作用是将参数中包含的不同的reducer函数组合成一个reducer函数，着个函数可以调用每一个child函数，并且将它们的结果放到一个state对象中。

首先按照键值对遍历参数reducers，依次检查值的类型是否为函数,并存放在finalReducers中。
然后调用assertReducerSanity来检查每个reducer是否能处两个测试action，需要保证每个reducer都有一个初始state。

combineReducers返回的也是一个reducer函数，在调用getUnexpectedStateShapeWarningMessage检查过参数之后，依次按照reducers的key，使用对应的state和action来调用对应的reducer方法，将返回结果放到nextState中，并且根据nextStateForKey !== previousStateForKey 前后state是否发生了变化来判断，没变化返回之前的state，变化了返回新的state。

# bindActionCreators

    function bindActionCreators(actionCreators, dispatch)

actionCreators如果是一个function，直接return

    function bindActionCreator(actionCreator, dispatch) {
      return (...args) => dispatch(actionCreator(...args))
    }

如果actionCreators是一个对象，按照键值对依次执行bindActionCreator，将返回结果放到boundActionCreators中return回去。

# applyMiddleware

    export default function applyMiddleware(...middlewares) {
      return (createStore) => (reducer, preloadedState, enhancer) => {
        var store = createStore(reducer, preloadedState, enhancer)
        var dispatch = store.dispatch
        var chain = []

        var middlewareAPI = {
          getState: store.getState,
          dispatch: (action) => dispatch(action)
        }
        chain = middlewares.map(middleware => middleware(middlewareAPI))
        dispatch = compose(...chain)(store.dispatch)

        return {
          ...store,
          dispatch
        }
      }
    }

这个方法把中间件加入dispatch的过程中，来增强store，例如异步的dispatch，多个dispatch，或者日志的记录。

redux-thunk是一个非常好的中间件，我也在生产环境中使用。

由于中间件基本都是异步的，所有在构成store的过程链中，这个函数应该第一个被调用。

注意，每个中间件都会被给予`dispatch` 和 `getState` 函数作为同名参数。


首先创建store，拿到dispatch函数。

然后依次执行所有的中间件，参数是dispatch和getState，将返回结果保存在数组中，组合起来，依次增强dispatch函数。

最后

    return {
        ...store,
        dispatch
      }

这时的dispatch就不是初始的状态，而是在基础上增强了许多其他的功能。

# compose

    function compose(...funcs)

首先过滤参数，只保留函数类型的。

如果过滤后长度为0，return

    arg => arg

如果过滤后长度为1，直接把那1个函数返回

大于1时，return

    funcs.reduce((a, b) => (...args) => a(b(...args)))

一个函数执行完了，结果作为参数继续执行下一个函数

For example, compose(f, g, h) is identical to doing (...args) => f(g(h(...args))).
