---
title: react源码解析-1Children
date: 2017-11-20 12:40:38
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

forEachChildren

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

# mapChildren

    function mapChildren(children, func, context) {
      if (children == null) {
        return children;
      }
      var result = [];
      mapIntoWithKeyPrefixInternal(children, result, null, func, context);
      return result;
    }