---
title: react源码解析-3ReactBaseClasses
date: 2017-11-25 14:58:47
tags: react javascript
---

之前说过ReactBaseClasses中包含了我们常用的组件类Component, PureComponent, AsyncComponent。
<!-- more -->
# Component

这里的代码非常简单，和我们熟悉的Component很不一样，我们熟悉的生命周期等都没有。原因是真正的updater会由renderer来注入，等到react-dom的时候再来分析。

    function Component(props, context, updater) {
      this.props = props;
      this.context = context;
      this.refs = emptyObject;
      // We initialize the default updater but the real one gets injected by the
      // renderer.
      this.updater = updater || ReactNoopUpdateQueue;
    }

    Component.prototype.isReactComponent = {};

    Component.prototype.setState = function(partialState, callback) {
      invariant(
        typeof partialState === 'object' ||
          typeof partialState === 'function' ||
          partialState == null,
        'setState(...): takes an object of state variables to update or a ' +
          'function which returns an object of state variables.',
      );
      this.updater.enqueueSetState(this, partialState, callback, 'setState');
    };

    Component.prototype.forceUpdate = function(callback) {
      this.updater.enqueueForceUpdate(this, callback, 'forceUpdate');
    };

# ReactNoopUpdateQueue

我们也可以看看ReactNoopUpdateQueue和warnNoop。
isMounted：检查组件是否mounted过了
enqueueForceUpdate：强制更新，注意它的调用时机，否则还是用setState比较好
enqueueReplaceState：完全替换state，使用它或setState来改变state，并且把`this.state`当作不可变对象，并且`this.state`不会立即更改
enqueueSetState：设置state的子集

    var ReactNoopUpdateQueue = {

      isMounted: function(publicInstance) {
        return false;
      },

      enqueueForceUpdate: function(publicInstance, callback, callerName) {
        warnNoop(publicInstance, 'forceUpdate');
      },

      enqueueReplaceState: function(
        publicInstance,
        completeState,
        callback,
        callerName,
      ) {
        warnNoop(publicInstance, 'replaceState');
      },

      enqueueSetState: function(
        publicInstance,
        partialState,
        callback,
        callerName,
      ) {
        warnNoop(publicInstance, 'setState');
      },

    };


    function warnNoop(publicInstance, callerName) {
      if (__DEV__) {
        var constructor = publicInstance.constructor;
        const componentName =
          (constructor && (constructor.displayName || constructor.name)) ||
          'ReactClass';
        const warningKey = `${componentName}.${callerName}`;
        if (didWarnStateUpdateForUnmountedComponent[warningKey]) {
          return;
        }
        warning(
          false,
          '%s(...): Can only update a mounted or mounting component. ' +
            'This usually means you called %s() on an unmounted component. ' +
            'This is a no-op.\n\nPlease check the code for the %s component.',
          callerName,
          callerName,
          componentName,
        );
        didWarnStateUpdateForUnmountedComponent[warningKey] = true;
      }
    }

# PureComponent

PureComponent继承了Component，并添加了isPureReactComponent方法

    function PureComponent(props, context, updater) {
      // Duplicated from Component.
      this.props = props;
      this.context = context;
      this.refs = emptyObject;
      // We initialize the default updater but the real one gets injected by the
      // renderer.
      this.updater = updater || ReactNoopUpdateQueue;
    }

    function ComponentDummy() {}
    ComponentDummy.prototype = Component.prototype;
    var pureComponentPrototype = (PureComponent.prototype = new ComponentDummy());
    pureComponentPrototype.constructor = PureComponent;
    // Avoid an extra prototype jump for these methods.
    Object.assign(pureComponentPrototype, Component.prototype);
    pureComponentPrototype.isPureReactComponent = true;

# AsyncComponent

大同小异AsyncComponent也继承了Component，并添加了unstable_isAsyncReactComponent方法，并指定了render返回children

    function AsyncComponent(props, context, updater) {
      // Duplicated from Component.
      this.props = props;
      this.context = context;
      this.refs = emptyObject;
      // We initialize the default updater but the real one gets injected by the
      // renderer.
      this.updater = updater || ReactNoopUpdateQueue;
    }

    var asyncComponentPrototype = (AsyncComponent.prototype = new ComponentDummy());
    asyncComponentPrototype.constructor = AsyncComponent;
    // Avoid an extra prototype jump for these methods.
    Object.assign(asyncComponentPrototype, Component.prototype);
    asyncComponentPrototype.unstable_isAsyncReactComponent = true;
    asyncComponentPrototype.render = function() {
      return this.props.children;
    };