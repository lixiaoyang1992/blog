---
title: redux-thunk源码分析
date: 2016-12-23 14:46:08
tags: reactjs
category : javascript
---

Redux Thunk是一个redux的thunk中间件，项目源码[gaearon/redux-thunk](https://github.com/gaearon/redux-thunk)。
<!-- more -->

所谓thunk函数，就是"传名调用"的一种实现策略，实现惰性求值。

    // calculation of 1 + 2 is immediate
    // x === 3
    let x = 1 + 2;

    // calculation of 1 + 2 is delayed
    // foo can be called later to perform the calculation
    // foo is a thunk!
    let foo = () => 1 + 2;

我在没有使用x的时候，1+2就已经被计算完了，而foo只有被调用时，foo()才是3.

___

在使用redux的时候，我们可能会使用发两种action。

    const INCREMENT_COUNTER = 'INCREMENT_COUNTER';

    function increment() {
      //同步action
      return {
        type: INCREMENT_COUNTER
      };
    }

    //thunk action creators
    function incrementAsync() {
      return dispatch => {
        setTimeout(() => {
          // Yay! Can invoke sync or async actions with `dispatch`
          //异步action
          dispatch(increment());
        }, 1000);
      };
    }

同步的action正常就会dispatch出去，而异步的action是无法return的，导致网络请求之后无法发出action。
而redux-thunk很巧妙的根据返回的类型，如果是function，就把dispatch和getState作为参数传入了回调函数，在回调函数中完成了dispatch。

    function createThunkMiddleware(extraArgument) {
      return ({ dispatch, getState }) => next => action => {
        if (typeof action === 'function') {
          return action(dispatch, getState, extraArgument);
        }

        return next(action);
      };
    }

    const thunk = createThunkMiddleware();
    thunk.withExtraArgument = createThunkMiddleware;

    export default thunk;
