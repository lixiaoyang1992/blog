---
title: TypeScript Hello World
date: 2016-06-28 08:43:28
tags: TypeScript
category : javascript
---

[Quick start](https://www.typescriptlang.org/docs/tutorial.html)  

#### 全局安装typescript  

    npm install -g typescript


#### 新建一个indxe.html文件，  

    <!DOCTYPE html>
    <html>
      <head><title> TypeScript Hello World </title></head>
      <body>
        <script src='index.js'></script>
      </body>
    </html>

#### 新建一个index.ts文件，

    class Greeter {
      constructor(public greeting: string) { }
      greet() {
          return "<h1>" + this.greeting + "</h1>";
      }
    };

    var greeter = new Greeter("Hello, world!");

    document.body.innerHTML = greeter.greet();

#### 新建一个tsconfig.json文件，  

    {
        "compilerOptions": {
            "sourceMap":  true
        }
    }

#### 运行一下

    tsc --sourcemap index.ts

在浏览打开index.html 就完成hello world了！
