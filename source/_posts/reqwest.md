---
title: reqwest源码分析
date: 2016-09-06 16:21:03
tags: javascript
category : javascript
---

reqwest是一个非常轻量级的ajax库，项目源码 [ded/reqwest](https://github.com/ded/reqwest)。  
<!-- more -->
常用的使用方法：

    reqwest('path/to/html', function (resp) {
      qwery('#content').html(resp)
    })

    reqwest({
        url: 'path/to/html'
      , method: 'post'
      , data: { foo: 'bar', baz: 100 }
      , success: function (resp) {
          qwery('#content').html(resp)
        }
    })

看一下他是如何实现的，代码不多，600多行。直接打开reqwest.js，是一段形如这样的代码：

    !function( ){ }( )
或许你见过另一种写法：
	(function( ){ })( )

function( ){ }是一个匿名函数，前面加!或+是告诉浏览器默认执行这个函数，参数是最后的()里面的，因为!+()在运算符最高的。  

	!function (name, context, definition) {
	  if (typeof module != 'undefined' && module.exports) module.exports = definition()
	  else if (typeof define == 'function' && define.amd) define(definition)
	  else context[name] = definition()
	}('reqwest', this, function () {} )

模块的导出不多说了。我们在代码中导入后得到的是第三个参数执行后的结果。  

    var context = this

    if ('window' in context) {
      var doc = document
        , byTag = 'getElementsByTagName'
        , head = doc[byTag]('head')[0]
    } else {
      var XHR2
      try {
        XHR2 = require('xhr2')
      } catch (ex) {
        throw new Error('Peer dependency `xhr2` required! Please npm install xhr2')
      }
    }

这里获取的html的head或xhr2（XMLHttpRequest Level 2）用于下面的跨域。  

接下来就是大量的变量和函数的定义了，先跳过直接看return。

    return reqwest

再找reqwest

    function reqwest(o, fn) {
      return new Reqwest(o, fn)
    }

再找Reqwest

    function Reqwest(o, fn) {
      this.o = o
      this.fn = fn

      init.apply(this, arguments)
    }

再找init

    function init(o, fn) {

      this.url = typeof o == 'string' ? o : o['url']
      this.timeout = null

      // whether request has been fulfilled for purpose
      // of tracking the Promises
      this._fulfilled = false
      // success handlers
      this._successHandler = function(){}
      this._fulfillmentHandlers = []
      // error handlers
      this._errorHandlers = []
      // complete (both success and fail) handlers
      this._completeHandlers = []
      this._erred = false
      this._responseArgs = {}

      var self = this

      fn = fn || function () {}

      if (o['timeout']) {
        this.timeout = setTimeout(function () {
          timedOut()
        }, o['timeout'])
      }

      if (o['success']) {
        this._successHandler = function () {
          o['success'].apply(o, arguments)
        }
      }

      if (o['error']) {
        this._errorHandlers.push(function () {
          o['error'].apply(o, arguments)
        })
      }

      if (o['complete']) {
        this._completeHandlers.push(function () {
          o['complete'].apply(o, arguments)
        })
      }

      function complete (resp) {
        o['timeout'] && clearTimeout(self.timeout)
        self.timeout = null
        while (self._completeHandlers.length > 0) {
          self._completeHandlers.shift()(resp)
        }
      }

      function success (resp) {
        var type = o['type'] || resp && setType(resp.getResponseHeader('Content-Type')) // resp can be undefined in IE
        resp = (type !== 'jsonp') ? self.request : resp
        // use global data filter on response text
        var filteredResponse = globalSetupOptions.dataFilter(resp.responseText, type)
          , r = filteredResponse
        try {
          resp.responseText = r
        } catch (e) {
          // can't assign this in IE<=8, just ignore
        }
        if (r) {
          switch (type) {
            case 'json':
              try {
                resp = context.JSON ? context.JSON.parse(r) : eval('(' + r + ')')
              } catch (err) {
                return error(resp, 'Could not parse JSON in response', err)
              }
              break
            case 'js':
              resp = eval(r)
              break
            case 'html':
              resp = r
              break
            case 'xml':
              resp = resp.responseXML
              && resp.responseXML.parseError // IE trololo
              && resp.responseXML.parseError.errorCode
              && resp.responseXML.parseError.reason
                ? null
                : resp.responseXML
              break
          }
        }

        self._responseArgs.resp = resp
        self._fulfilled = true
        fn(resp)
        self._successHandler(resp)
        while (self._fulfillmentHandlers.length > 0) {
          resp = self._fulfillmentHandlers.shift()(resp)
        }

        complete(resp)
      }

      function timedOut() {
        self._timedOut = true
        self.request.abort()
      }

      function error(resp, msg, t) {
        resp = self.request
        self._responseArgs.resp = resp
        self._responseArgs.msg = msg
        self._responseArgs.t = t
        self._erred = true
        while (self._errorHandlers.length > 0) {
          self._errorHandlers.shift()(resp, msg, t)
        }
        complete(resp)
      }

      this.request = getRequest.call(this, success, error)
    }

主要是根据构造的参数来赋值，包含url，还有完成、成功、失败等回调函数。



Happy Ajaxing!
