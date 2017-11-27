---
title: react源码解析-4ReactElement与ReactElementValidator
date: 2017-11-25 17:26:20
tags: react javascript
---

顾名思义，这两个文件里面的都是对于react元素的一些操作，一个用于生产环境，一个用于开发环境的校验。
<!-- more -->
# ReactElement

先不看其他的方法，我们首先来看一下ReactElement到底是什么样的。
ReactElement函数是一个工厂函数，创建新的react元素；不支持class模式，不要new它；instanceof也不奏效，可以检测$$typeof是不是Symbol.for('react.element')。

首先创建一个element对象，参数里的type、key、ref、props、owner放进去，然后就可以把这个对象return了。

不过在开发模式下，我们在这个对象里面额外添加了_store作为验证标记。

再把_store、_self、_source赋值并且设置为不能重新定义属性，不能枚举，不能写入，最后调用Object.freeze使对象不可再改变。

    var ReactElement = function(type, key, ref, self, source, owner, props) {
      var element = {
        // This tag allow us to uniquely identify this as a React Element
        $$typeof: REACT_ELEMENT_TYPE,

        // Built-in properties that belong on the element
        type: type,
        key: key,
        ref: ref,
        props: props,

        // Record the component responsible for creating this element.
        _owner: owner,
      };

      if (__DEV__) {
        // The validation flag is currently mutative. We put it on
        // an external backing store so that we can freeze the whole object.
        // This can be replaced with a WeakMap once they are implemented in
        // commonly used development environments.
        element._store = {};

        // To make comparing ReactElements easier for testing purposes, we make
        // the validation flag non-enumerable (where possible, which should
        // include every environment we run tests in), so the test framework
        // ignores it.
        Object.defineProperty(element._store, 'validated', {
          configurable: false,
          enumerable: false,
          writable: true,
          value: false,
        });
        // self and source are DEV only properties.
        Object.defineProperty(element, '_self', {
          configurable: false,
          enumerable: false,
          writable: false,
          value: self,
        });
        // Two elements created in two different places should be considered
        // equal for testing purposes and therefore we hide it from enumeration.
        Object.defineProperty(element, '_source', {
          configurable: false,
          enumerable: false,
          writable: false,
          value: source,
        });
        if (Object.freeze) {
          Object.freeze(element.props);
          Object.freeze(element);
        }
      }

      return element;
    };

# createElement

    createElement(type, config, children)

用jsx写的代码都会被转换成createElement，你无需直接调用它。
type是你要创建的元素的类型，可以是html的div或者span，也可以是其他的react组件，注意大小写。
从config中获取props、key、ref、self、source。
向props加入children，如果是一个就放一个对象，如果是多个就放入一个数组。
那如果type.defaultProps有默认的props时，并且对应的props里面的值是undefined，把默认值赋值到props中。
这时就可以直接return一个调用ReactElement的执行结果了。
在开发环境里，我们还会对key和ref进行校验。

    export function createElement(type, config, children) {
      var propName;

      // Reserved names are extracted
      var props = {};

      var key = null;
      var ref = null;
      var self = null;
      var source = null;

      if (config != null) {
        if (hasValidRef(config)) {
          ref = config.ref;
        }
        if (hasValidKey(config)) {
          key = '' + config.key;
        }

        self = config.__self === undefined ? null : config.__self;
        source = config.__source === undefined ? null : config.__source;
        // Remaining properties are added to a new props object
        for (propName in config) {
          if (
            hasOwnProperty.call(config, propName) &&
            !RESERVED_PROPS.hasOwnProperty(propName)
          ) {
            props[propName] = config[propName];
          }
        }
      }

      // Children can be more than one argument, and those are transferred onto
      // the newly allocated props object.
      var childrenLength = arguments.length - 2;
      if (childrenLength === 1) {
        props.children = children;
      } else if (childrenLength > 1) {
        var childArray = Array(childrenLength);
        for (var i = 0; i < childrenLength; i++) {
          childArray[i] = arguments[i + 2];
        }
        if (__DEV__) {
          if (Object.freeze) {
            Object.freeze(childArray);
          }
        }
        props.children = childArray;
      }

      // Resolve default props
      if (type && type.defaultProps) {
        var defaultProps = type.defaultProps;
        for (propName in defaultProps) {
          if (props[propName] === undefined) {
            props[propName] = defaultProps[propName];
          }
        }
      }
      if (__DEV__) {
        if (key || ref) {
          if (
            typeof props.$$typeof === 'undefined' ||
            props.$$typeof !== REACT_ELEMENT_TYPE
          ) {
            var displayName =
              typeof type === 'function'
                ? type.displayName || type.name || 'Unknown'
                : type;
            if (key) {
              defineKeyPropWarningGetter(props, displayName);
            }
            if (ref) {
              defineRefPropWarningGetter(props, displayName);
            }
          }
        }
      }
      return ReactElement(
        type,
        key,
        ref,
        self,
        source,
        ReactCurrentOwner.current,
        props,
      );
    }

# createFactory

这个就是相当于createElement的第一个参数type给制定了，返回给你个createElement函数。

    export function createFactory(type) {
      var factory = createElement.bind(null, type);
      factory.type = type;
      return factory;
    }

# cloneElement



    export function cloneElement(element, config, children) {
      var propName;

      // Original props are copied
      var props = Object.assign({}, element.props);

      // Reserved names are extracted
      var key = element.key;
      var ref = element.ref;
      // Self is preserved since the owner is preserved.
      var self = element._self;
      // Source is preserved since cloneElement is unlikely to be targeted by a
      // transpiler, and the original source is probably a better indicator of the
      // true owner.
      var source = element._source;

      // Owner will be preserved, unless ref is overridden
      var owner = element._owner;

      if (config != null) {
        if (hasValidRef(config)) {
          // Silently steal the ref from the parent.
          ref = config.ref;
          owner = ReactCurrentOwner.current;
        }
        if (hasValidKey(config)) {
          key = '' + config.key;
        }

        // Remaining properties override existing props
        var defaultProps;
        if (element.type && element.type.defaultProps) {
          defaultProps = element.type.defaultProps;
        }
        for (propName in config) {
          if (
            hasOwnProperty.call(config, propName) &&
            !RESERVED_PROPS.hasOwnProperty(propName)
          ) {
            if (config[propName] === undefined && defaultProps !== undefined) {
              // Resolve default props
              props[propName] = defaultProps[propName];
            } else {
              props[propName] = config[propName];
            }
          }
        }
      }

      // Children can be more than one argument, and those are transferred onto
      // the newly allocated props object.
      var childrenLength = arguments.length - 2;
      if (childrenLength === 1) {
        props.children = children;
      } else if (childrenLength > 1) {
        var childArray = Array(childrenLength);
        for (var i = 0; i < childrenLength; i++) {
          childArray[i] = arguments[i + 2];
        }
        props.children = childArray;
      }

      return ReactElement(element.type, key, ref, self, source, owner, props);
    }

# isValidElement

