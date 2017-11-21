---
title: react源码解析-2Children
date: 2017-11-21 12:40:38
tags: react javascript
---

React.Children提供了对this.props.children的操作的函数。
<!-- more -->

react/src/ReactChildren文件export的函数如下

    export {
      forEachChildren as forEach,
      mapChildren as map,
      countChildren as count,
      onlyChild as only,
      toArray,
    };

# forEachChildren

    function forEachChildren(children, forEachFunc, forEachContext) {
      if (children == null) {
        return children;
      }
      var traverseContext = getPooledTraverseContext(
        null,
        null,
        forEachFunc,
        forEachContext,
      );
      traverseAllChildren(children, forEachSingleChild, traverseContext);
      releaseTraverseContext(traverseContext);
    }

traverseContext在一个池中获取了一个遍历的上下文对象，这里只是把forEachFunc和forEachContext封装到一个对象里。

traverseAllChildren中调用了traverseAllChildrenImpl。

    function traverseAllChildrenImpl(
      children,
      nameSoFar,
      callback,
      traverseContext,
    ) {
      ...
    }

traverseAllChildrenImpl的参数children如果是数组，会遍历children，会把每一个child递归调用自身。
参数children是一个单独的react element或portal（16新增类型）时会调用callback

    callback(
      traverseContext,
      children,
      // If it's the only child, treat the name as if it was wrapped in an array
      // so that it's consistent if the number of children grows.
      nameSoFar === '' ? SEPARATOR + getComponentKey(children, 0) : nameSoFar,
    );

forEachChildren的回调函数是forEachSingleChild，它的内容如下，这里重新生成的key值没有用到

    function forEachSingleChild(bookKeeping, child, name) {
      var {func, context} = bookKeeping;
      func.call(context, child, bookKeeping.count++);
    }

它首先在bookKeeping（遍历上下文）里，拿到我们在forEachChildren传入的遍历函数和上下文，用call调用一下，我们在下面就可以拿到child和index了。

    React.Children.forEach(childrenArr,(child,index)=>{
      ...
    });


最后释放上下文对象

# mapChildren

    function mapChildren(children, func, context) {
      if (children == null) {
        return children;
      }
      var result = [];
      mapIntoWithKeyPrefixInternal(children, result, null, func, context);
      return result;
    }

mapIntoWithKeyPrefixInternal的内容如下，和forEachChildren基本一致，增加了存放返回值的数组和escapedPrefix=‘’。

    function mapIntoWithKeyPrefixInternal(children, array, prefix, func, context) {
      var escapedPrefix = '';
      if (prefix != null) {
        escapedPrefix = escapeUserProvidedKey(prefix) + '/';
      }
      var traverseContext = getPooledTraverseContext(
        array,
        escapedPrefix,
        func,
        context,
      );
      traverseAllChildren(children, mapSingleChildIntoContext, traverseContext);
      releaseTraverseContext(traverseContext);
    }

不同之处在于traverseAllChildren的callback是mapSingleChildIntoContext。

    function mapSingleChildIntoContext(bookKeeping, child, childKey) {
      var {result, keyPrefix, func, context} = bookKeeping;

      var mappedChild = func.call(context, child, bookKeeping.count++);
      if (Array.isArray(mappedChild)) {
        mapIntoWithKeyPrefixInternal(
          mappedChild,
          result,
          childKey,
          emptyFunction.thatReturnsArgument,
        );
      } else if (mappedChild != null) {
        if (isValidElement(mappedChild)) {
          mappedChild = cloneAndReplaceKey(
            mappedChild,
            // Keep both the (mapped) and old keys if they differ, just as
            // traverseAllChildren used to do for objects as children
            keyPrefix +
              (mappedChild.key && (!child || child.key !== mappedChild.key)
                ? escapeUserProvidedKey(mappedChild.key) + '/'
                : '') +
              childKey,
          );
        }
        result.push(mappedChild);
      }
    }

var mappedChild = func.call(context, child, bookKeeping.count++)后如果是数组，再将结果数组通过mapIntoWithKeyPrefixInternal递归一遍。(之后的key值会加上.$)
得到单独的元素时，将结果clone并且重设一遍key值，最后将结果push进result。

# countChildren

    function countChildren(children, context) {
      return traverseAllChildren(children, emptyFunction.thatReturnsNull, null);
    }

traverseAllChildren，traverseAllChildrenImpl时有计数，将结果返回就好了。

# onlyChild

    function onlyChild(children) {
      invariant(
        isValidElement(children),
        'React.Children.only expected to receive a single React element child.',
      );
      return children;
    }

通过isValidElement判断传入的参数是否是“一个”react元素，否则就报错。

    export function isValidElement(object) {
      return (
        typeof object === 'object' &&
        object !== null &&
        object.$$typeof === REACT_ELEMENT_TYPE
      );
    }

# toArray

    function toArray(children) {
      var result = [];
      mapIntoWithKeyPrefixInternal(
        children,
        result,
        null,
        emptyFunction.thatReturnsArgument,
      );
      return result;
    }

和map类似，返回一个重设过key的元素的数组。
