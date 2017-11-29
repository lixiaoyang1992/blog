---
title: react v16.2.0 
date: 2017-11-29 12:51:23
tags: react javascript
---

几个小时之前react发布了v16.2.0版本，最大的变化就是新增了Fragment语法。
<!-- more -->
之前需要返回多个children的场景，现在可以用Fragment来更优雅的实现了。

    render() {
      return (
        <>
          <h1>React v16.2 has been released!</h1>
          <h2>Introduces a new syntax for fragments!</h2>
          Thanks to all our collaborators!
        </>
      );
    }

或者

    render() {
      return (
        <Fragment>
          <h1>React v16.2 has been released!</h1>
          <h2>Introduces a new syntax for fragments!</h2>
          Thanks to all our collaborators!
        </Fragment>
      );
    }

各种包和Babel等配套的升级可以在官网博客里看到。

# React

现在返回包含

    import {REACT_FRAGMENT_TYPE} from 'shared/ReactSymbols';

    var React = {
      。。。
      Fragment: REACT_FRAGMENT_TYPE,

    }

# shared

在shared包下面新增了ReactSymbols，之前在各个文件里面的Symbol都集中到了这个文件中。

    export const REACT_FRAGMENT_TYPE = hasSymbol
      ? Symbol.for('react.fragment')
      : 0xeacb;
