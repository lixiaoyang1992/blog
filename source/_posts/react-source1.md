---
title: react源码解析-1初探
date: 2017-11-19 14:24:55
tags: react
category : javascript
---

react这个项目分为多个package，其中核心部分react主要负责虚拟节点，由不同的render将虚拟节点渲染到不同的平台，如react-dom，react-native-renderer等。完全解耦，来实现跨平台。
<!-- more -->

# 模块声明

首先看一下react/src/React.js中export了哪些东西。

    var React = {
      Children: {
        map,
        forEach,
        count,
        toArray,
        only,
      },

      Component,
      PureComponent,
      unstable_AsyncComponent: AsyncComponent,

      createElement: __DEV__ ? createElementWithValidation : createElement,
      cloneElement: __DEV__ ? cloneElementWithValidation : cloneElement,
      createFactory: __DEV__ ? createFactoryWithValidation : createFactory,
      isValidElement: isValidElement,

      version: ReactVersion,

      __SECRET_INTERNALS_DO_NOT_USE_OR_YOU_WILL_BE_FIRED: {
        ReactCurrentOwner,
        // Used by renderers to avoid bundling object-assign twice in UMD bundles:
        assign,
      },
    };

    if (enableReactFragment) {
      React.Fragment = REACT_FRAGMENT_TYPE;
    }

这就是通常情况下我们通过 import react from 'react' 所能拿到的react模块包含的所有内容了。
虽然现在v16已经发布了，但是实际上还有很多功能是没有发布的，例如enableReactFragment目前是false的，React.Fragment功能目前还没有正式加入react，你只能在react-addons-create-fragment中使用。

# 初探

## Children

ReactChildren下面包含 map,forEach,count,toArray,only 五个方法，明明上和数组的一些方法很像，一般是用来对this.props,children进行一些操作。
如在react-dedux的Provider中

    Children.only(this.props.children)

来确保Provider组建下只有一个子组件

## Component，PureComponent，unstable_AsyncComponent

这些就是我们最常用的组件类了，他们都来自于ReactBaseClasses。

定义了Component函数，以及setState，forceUpdate等常用函数。dev模式下还有几个废弃的api，这里不说明了。

    function Component(props, context, updater) {
      this.props = props;
      this.context = context;
      this.refs = emptyObject;
      // We initialize the default updater but the real one gets injected by the
      // renderer.
      this.updater = updater || ReactNoopUpdateQueue;
    }

    Component.prototype.isReactComponent = {};
    Component.prototype.setState
    Component.prototype.forceUpdate 

PureComponent基本上是复制的Component，只多了isPureReactComponent。

AsyncComponent在Component的基础上增加了unstable_isAsyncReactComponent。当然从命名上知道这还是一个不稳定的组件，不建议使用。

    asyncComponentPrototype.render = function() {
      return this.props.children;
    };

## createElement,cloneElement,createFactory,isValidElement

这几个方法在开发环境下来自ReactElementValidator文件，生产环境来自ReactElement。
ReactElementValidator可以在开发环境下检查传递的参数，也可以被其他静态类型检查替代，如flow，typescript等。

createElementWithValidation ， createElement：创建一个react element。
cloneElementWithValidation ， cloneElement：克隆一个react element。
createFactoryWithValidation ， createFactory：创建一个制定类型的react element的工厂函数。
isValidElement：判断一个对象是否是react元素。

## version

返回react的版本号

## __SECRET_INTERNALS_DO_NOT_USE_OR_YOU_WILL_BE_FIRED

里面是react内部访问的部分，不要使用，否则你会被开掉 (^_^)

# 总结

我们对react的代码基本情况有了一定的了解，接下来会每个文件详细分析。
react的代码可读性极强，少量的注释能够清晰的解释代码的作用，值得学习。