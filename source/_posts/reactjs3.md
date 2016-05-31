---
title: reactjs－基础语法
date: 2016-05-19 10:35:17
tags: reactjs
category : javascript
---

## 基础react语法

react最常见的写法如下

    var HelloMessage = React.createClass({
      render: function() {
        return <div>Hello {this.props.name}</div>;
      }
    });

    ReactDOM.render(
      <HelloMessage name="John" />,
      document.getElementById('container')
    );

react使用jsx语法，支持html和js混编。遇到 HTML 标签（以 < 开头），就用 HTML 规则解析；遇到代码块（以 { 开头），就用 JavaScript 规则解析。  

## 组件与复用  

react中用的非常多的就是组件化，这应该也是web未来发展的方向，这种复用非常方便的方式也方便了开发。  

    ReactDOM.render(
      <div>
        <HelloMessage name="John" />
        <HelloMessage name="Frank" />
        <HelloMessage name="Didier" />
      </div>,
      document.getElementById('container')
    );

很简单的，我们就复用了组件。

## state与props  

这是所有组件都具有的，也是非常重要的两个属性。  

state是组件本身的状态，一旦组件被设置了新的state就会触发render重新渲染。一般包含的是是否显示，是否可用等等组件本身的状态，都是与外部环境无关的内容。

props是由外部传递进来的参数，一般数据都是以参数的身份被传递进来，在flux模型中外部数据都是维护在store中的，所以作为参数传递是必不可少的。  

一定要区分好state与props负责的范围，维护适当的数据。我在刚开始接触的时候以为把所有的数据都放在store里分层最彻底，把许多诸如visible之类的简单数据都放到了store中，反而在后期操作起来更繁琐。

    var Demo = React.createClass({
      getInitialState: function() {
        return {visible: false};
      },
      handleClick: function(event) {
        this.setState({visible: !this.state.visible});
      },
      render: function() {
        return (
          <p onClick={this.handleClick}>
             {this.state.visible?
               '隐藏'
               ：‘显示’}
          </p>
        );
      }
    });

    ReactDOM.render(
      <Demo />,
      document.getElementById('example')
    );

每次点击之后都会更改状态，重新渲染。

## 生命周期

一个组件在它创建、显示和接受新的状态、参数时，会按照顺序调用一系列方法，就是生命周期方法。了解了这些对于做交互维护UI非常有帮助。  

这部分我没什么好说的，官网上的说明已经非常棒了[官方文档中文版](http://reactjs.cn/react/docs/component-specs.html)

  参考链接：[React 入门实例教程](http://www.ruanyifeng.com/blog/2015/03/react.html) by 阮一峰  
  我在学习的伊始，收到了很多启发。
