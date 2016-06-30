---
title: typescript react Hello World
date: 2016-06-28 09:00:16
tags: [ TypeScript , react ]
category : javascript
---

[官方教程](https://www.typescriptlang.org/docs/handbook/react-&-webpack.html)  
[本文代码地址](https://github.com/lixiaoyang1992/React-Webpack-typescript)

总体来说就是加了一些配置，具体的使用代码还是一样的。

### 项目结构  

    mkdir proj
    cd proj
    mkdir src
    cd src
    mkdir components
    cd ..
    mkdir dist

### 初始化

    npm init

一路回车

#### 安装依赖

    npm install -g typescript typings webpack
    npm install --save react react-dom
    npm install --save-dev ts-loader source-map-loader
    npm link typescript
    typings install --global --save dt~react
    typings install --global --save dt~react-dom

#### typescript配置文件tsconfig.json

    {
        "compilerOptions": {
            "outDir": "./dist/",
            "sourceMap": true,
            "noImplicitAny": true,
            "module": "commonjs",
            "target": "es5",
            "jsx": "react"
        },
        "files": [
            "./typings/index.d.ts",
            "./src/components/Hello.tsx",
            "./src/index.tsx"
        ]
    }

#### react代码

    import * as React from "react";

    export interface HelloProps { compiler: string; framework: string; }

    export class Hello extends React.Component<HelloProps, {}> {
        render() {
            return <h1>Hello from {this.props.compiler} and {this.props.framework}!</h1>;
        }
    }


    import * as React from "react";
    import * as ReactDOM from "react-dom";

    import { Hello } from "./components/Hello";

    ReactDOM.render(
        <Hello compiler="TypeScript" framework="React" />,
        document.getElementById("example")
    );

#### index.html

    <!DOCTYPE html>
    <html>
        <head>
            <meta charset="UTF-8" />
            <title>Hello React!</title>
        </head>
        <body>
            <div id="example"></div>

            <!-- Dependencies -->
            <script src="./node_modules/react/dist/react.js"></script>
            <script src="./node_modules/react-dom/dist/react-dom.js"></script>

            <!-- Main -->
            <script src="./dist/bundle.js"></script>
        </body>
    </html>

#### webpack配置文件webpack.config.js

    module.exports = {
        entry: "./src/index.tsx",
        output: {
            filename: "./dist/bundle.js",
        },

        // Enable sourcemaps for debugging webpack's output.
        devtool: "source-map",

        resolve: {
            // Add '.ts' and '.tsx' as resolvable extensions.
            extensions: ["", ".webpack.js", ".web.js", ".ts", ".tsx", ".js"]
        },

        module: {
            loaders: [
                // All files with a '.ts' or '.tsx' extension will be handled by 'ts-loader'.
                { test: /\.tsx?$/, loader: "ts-loader" }
            ],

            preLoaders: [
                // All output '.js' files will have any sourcemaps re-processed by 'source-map-loader'.
                { test: /\.js$/, loader: "source-map-loader" }
            ]
        },

        // When importing a module whose path matches one of the following, just
        // assume a corresponding global variable exists and use that instead.
        // This is important because it allows us to avoid bundling all of our
        // dependencies, which allows browsers to cache those libraries between builds.
        externals: {
            "react": "React",
            "react-dom": "ReactDOM"
        },
    };

#### 运行

只需执行  

    webpack

然后在浏览器中打开index.html
