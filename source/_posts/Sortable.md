---
title: 拖放排序库 Sortable 使用
date: 2016-06-21 23:15:27
tags: 前端
category : javascript
---

今天发现了一个非常棒的库[Sortable](https://github.com/RubaXa/Sortable)，用起来非常的好用。  

## 介绍

他自己的介绍是这样的：  
Sortable — is a minimalist JavaScript library for reorderable drag-and-drop lists on modern browsers and touch devices. No jQuery. Supports Meteor, AngularJS, React, Polymer and any CSS library, e.g. Bootstrap.

非常轻量级的拖放排序库，适用于现代浏览器和触摸设备。不需要jQuery，支持很多前端库。
<!-- more -->
## 使用  

最基本的使用很简单。  

    <ul id="items">
      <li>item 1</li>
      <li>item 2</li>
      <li>item 3</li>
    </ul>  

然后

    var el = document.getElementById('items');
    var sortable = Sortable.create(el);

还可以加上许多配置项

    var sortable = new Sortable(el, {
        group: "name",  // or { name: "...", pull: [true, false, clone], put: [true, false, array] }
        sort: true,  // sorting inside list
        delay: 0, // time in milliseconds to define when the sorting should start
        disabled: false, // Disables the sortable if set to true.
        store: null,  // @see Store
        animation: 150,  // ms, animation speed moving items when sorting, `0` — without animation
        handle: ".my-handle",  // Drag handle selector within list items
        filter: ".ignore-elements",  // Selectors that do not lead to dragging (String or Function)
        draggable: ".item",  // Specifies which items inside the element should be draggable
        ghostClass: "sortable-ghost",  // Class name for the drop placeholder
        chosenClass: "sortable-chosen",  // Class name for the chosen item
        dataIdAttr: 'data-id',

        forceFallback: false,  // ignore the HTML5 DnD behaviour and force the fallback to kick in
        fallbackClass: "sortable-fallback",  // Class name for the cloned DOM Element when using forceFallback
        fallbackOnBody: false,  // Appends the cloned DOM Element into the Document's Body

        scroll: true, // or HTMLElement
        scrollSensitivity: 30, // px, how near the mouse must be to an edge to start scrolling.
        scrollSpeed: 10, // px

        setData: function (dataTransfer, dragEl) {
            dataTransfer.setData('Text', dragEl.textContent);
        },

        // dragging started
        onStart: function (/**Event*/evt) {
            evt.oldIndex;  // element index within parent
        },

        // dragging ended
        onEnd: function (/**Event*/evt) {
            evt.oldIndex;  // element's old index within parent
            evt.newIndex;  // element's new index within parent
        },

        // Element is dropped into the list from another list
        onAdd: function (/**Event*/evt) {
            var itemEl = evt.item;  // dragged HTMLElement
            evt.from;  // previous list
            // + indexes from onEnd
        },

        // Changed sorting within list
        onUpdate: function (/**Event*/evt) {
            var itemEl = evt.item;  // dragged HTMLElement
            // + indexes from onEnd
        },

        // Called by any change to the list (add / update / remove)
        onSort: function (/**Event*/evt) {
            // same properties as onUpdate
        },

        // Element is removed from the list into another list
        onRemove: function (/**Event*/evt) {
            // same properties as onUpdate
        },

        // Attempt to drag a filtered element
        onFilter: function (/**Event*/evt) {
            var itemEl = evt.item;  // HTMLElement receiving the `mousedown|tapstart` event.
        },

        // Event when you move an item in the list or between lists
        onMove: function (/**Event*/evt) {
            // Example: http://jsbin.com/tuyafe/1/edit?js,output
            evt.dragged; // dragged HTMLElement
            evt.draggedRect; // TextRectangle {left, top, right и bottom}
            evt.related; // HTMLElement on which have guided
            evt.relatedRect; // TextRectangle
            // return false; — for cancel
        }
    });

其中onStart，onEnd可以获取到拖放前后的序号。

## 结合react使用  

有两种方式使用，一种是使用mixins，由于react官方blog说明在es2015（es6）中不在支持mixins，现在已经2016过半了，我们也不用这种方式了，使用另一种基于ref的方式。  

    import * as React from "react";
    import Sortable from 'sortablejs';

    export class SortableExampleEsnext extends React.Component {

      sortableContainersDecorator = (componentBackingInstance) => {
        // check if backing instance not null
        if (componentBackingInstance) {
          let options = {
            handle: ".group-title" // Restricts sort start click/touch to the specified element
          };
          Sortable.create(componentBackingInstance, options);
        }
      };

      sortableGroupDecorator = (componentBackingInstance) => {
        // check if backing instance not null
        if (componentBackingInstance) {
          let options = {
            draggable: "div", // Specifies which items inside the element should be sortable
            group: "shared"
          };
          Sortable.create(componentBackingInstance, options);
        }
      };

      render() {
        return (
          <div className="container" ref={this.sortableContainersDecorator}>
            <div className="group">
              <h2 className="group-title">Group 1</h2>
              <div className="group-list" ref={this.sortableGroupDecorator}>
                <div>Swap them around</div>
                <div>Swap us around</div>
                <div>Swap things around</div>
                <div>Swap everything around</div>
              </div>
            </div>
            <div className="group">
              <h2 className="group-title">Group 2</h2>
              <div className="group-list" ref={this.sortableGroupDecorator}>
                <div>Swap them around</div>
                <div>Swap us around</div>
                <div>Swap things around</div>
                <div>Swap everything around</div>
              </div>
            </div>
          </div>
        );
      }
    }

以上是官方的示例。
