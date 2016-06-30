---
title: typescript angular2 Hello World
date: 2016-06-30 23:25:55
tags: [ TypeScript , angular2 ]
category : javascript
---

[官方教程](https://angular.io/docs/ts/latest/quickstart.html)  
[本文代码地址](https://github.com/lixiaoyang1992/typescript_angular2）

### 创建与配置配置项目

#### 创建目录

    mkdir angular2-quickstart
    cd    angular2-quickstart

#### 创建配置文件

package.json

    {
      "name": "angular2-quickstart",
      "version": "1.0.0",
      "scripts": {
        "start": "tsc && concurrently \"npm run tsc:w\" \"npm run lite\" ",
        "lite": "lite-server",
        "postinstall": "typings install",
        "tsc": "tsc",
        "tsc:w": "tsc -w",
        "typings": "typings"
      },
      "license": "ISC",
      "dependencies": {
        "@angular/common":  "2.0.0-rc.3",
        "@angular/compiler":  "2.0.0-rc.3",
        "@angular/core":  "2.0.0-rc.3",
        "@angular/forms": "0.1.1",
        "@angular/http":  "2.0.0-rc.3",
        "@angular/platform-browser":  "2.0.0-rc.3",
        "@angular/platform-browser-dynamic":  "2.0.0-rc.3",
        "@angular/router":  "3.0.0-alpha.8",
        "@angular/router-deprecated":  "2.0.0-rc.2",
        "@angular/upgrade":  "2.0.0-rc.3",
        "systemjs": "0.19.27",
        "core-js": "^2.4.0",
        "reflect-metadata": "^0.1.3",
        "rxjs": "5.0.0-beta.6",
        "zone.js": "^0.6.12",
        "angular2-in-memory-web-api": "0.0.12",
        "bootstrap": "^3.3.6"
      },
      "devDependencies": {
        "concurrently": "^2.0.0",
        "lite-server": "^2.2.0",
        "typescript": "^1.8.10",
        "typings":"^1.0.4"
      }
    }

tsconfig.json

    {
      "compilerOptions": {
        "target": "es5",
        "module": "commonjs",
        "moduleResolution": "node",
        "sourceMap": true,
        "emitDecoratorMetadata": true,
        "experimentalDecorators": true,
        "removeComments": false,
        "noImplicitAny": false
      }
    }

typings.json

    {
      "globalDependencies": {
        "core-js": "registry:dt/core-js#0.0.0+20160602141332",
        "jasmine": "registry:dt/jasmine#2.2.0+20160621224255",
        "node": "registry:dt/node#6.0.0+20160621231320"
      }
    }

systemjs.config.js


    (function(global) {
      // map tells the System loader where to look for things
      var map = {
        'app':                        'app', // 'dist',
        '@angular':                   'node_modules/@angular',
        'angular2-in-memory-web-api': 'node_modules/angular2-in-memory-web-api',
        'rxjs':                       'node_modules/rxjs'
      };
      // packages tells the System loader how to load when no filename and/or no extension
      var packages = {
        'app':                        { main: 'main.js',  defaultExtension: 'js' },
        'rxjs':                       { defaultExtension: 'js' },
        'angular2-in-memory-web-api': { main: 'index.js', defaultExtension: 'js' },
      };
      var ngPackageNames = [
        'common',
        'compiler',
        'core',
        'forms',
        'http',
        'platform-browser',
        'platform-browser-dynamic',
        'router',
        'router-deprecated',
        'upgrade',
      ];
      // Individual files (~300 requests):
      function packIndex(pkgName) {
        packages['@angular/'+pkgName] = { main: 'index.js', defaultExtension: 'js' };
      }
      // Bundled (~40 requests):
      function packUmd(pkgName) {
        packages['@angular/'+pkgName] = { main: '/bundles/' + pkgName + '.umd.js', defaultExtension: 'js' };
      }
      // Most environments should use UMD; some (Karma) need the individual index files
      var setPackageConfig = System.packageWithIndex ? packIndex : packUmd;
      // Add package entries for angular packages
      ngPackageNames.forEach(setPackageConfig);
      var config = {
        map: map,
        packages: packages
      };
      System.config(config);
    })(this);

安装依赖

    npm install

### 写一个组件

    mkdir app

app/app.component.ts

    import { Component } from '@angular/core';
      @Component({
      selector: 'my-app',
      template: '<h1>My First Angular 2 App</h1>'
    })
    export class AppComponent { }

### 添加入口

app/main.ts

    import { bootstrap }    from '@angular/platform-browser-dynamic';
    import { AppComponent } from './app.component';
    bootstrap(AppComponent);

### 添加index.html

根目录index.html

    <html>
      <head>
        <title>Angular 2 QuickStart</title>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <link rel="stylesheet" href="styles.css">
        <!-- 1. Load libraries -->
         <!-- Polyfill(s) for older browsers -->
        <script src="node_modules/core-js/client/shim.min.js"></script>
        <script src="node_modules/zone.js/dist/zone.js"></script>
        <script src="node_modules/reflect-metadata/Reflect.js"></script>
        <script src="node_modules/systemjs/dist/system.src.js"></script>
        <!-- 2. Configure SystemJS -->
        <script src="systemjs.config.js"></script>
        <script>
          System.import('app').catch(function(err){ console.error(err); });
        </script>
      </head>
      <!-- 3. Display the application -->
      <body>
        <my-app>Loading...</my-app>
      </body>
    </html>

### 运行

只需执行  

    npm start

然后在浏览器中打开index.html
