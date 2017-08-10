---
title: node.js学习笔记
date: 2017-04-11 23:33:15
tags: nodejs
category : javascript
---

# Hello World

node.js执行起来非常简单

    console.log("Hello World");

保存这个文件，然后执行就可以了

    node helloworld.js

<!-- more -->
# HTTP服务器

node的一个重要模块 http

    var http = require("http");

    http.createServer(function(request, response) {
      response.writeHead(200, {"Content-Type": "text/plain"});
      response.write("Hello World");
      response.end();
    }).listen(8888);

执行，就可以在 http://localhost:8888 访问到页面了

    node server.js

再稍微封装一下

    1.server.js
    var http = require("http");

    function start() {
      function onRequest(request, response) {
        console.log("Request received.");
        response.writeHead(200, {"Content-Type": "text/plain"});
        response.write("Hello World");
        response.end();
      }

      http.createServer(onRequest).listen(8888);
      console.log("Server has started.");
    }

    exports.start = start;

    2.index.js
    var server = require("./server");

    server.start();

# 路由

这要依赖node的另一个模块url  
修改server.js

    var http = require("http");
    var url = require("url");

    function start() {
      function onRequest(request, response) {
        var pathname = url.parse(request.url).pathname;
        console.log("Request for " + pathname + " received.");
        response.writeHead(200, {"Content-Type": "text/plain"});
        response.write("Hello World");
        response.end();
      }

      http.createServer(onRequest).listen(8888);
      console.log("Server has started.");
    }

    exports.start = start;

这时服务器会对 http://localhost:8888/start 和 http://localhost:8888/end 输出不同内容了。
