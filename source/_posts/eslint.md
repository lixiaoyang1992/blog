---
title: eslint
date: 2016-11-02 13:32:24
tags: javascript
category : javascript
---

eslint是一个是一个QA工具，用来避免低级错误和统一代码的风格，项目源码 [eslint/eslint](https://github.com/eslint/eslint/)。
<!-- more -->
在项目中使用非常简单，支持jsx语法。

    npm i -g eslint
    eslint --init

然后在它给出的选项中选择，为了方便的话，可以直接选择流行的配置：Google、Airbnb、Standard中选择。当然也可以自定义详细完整的配置设置在[官网](http://eslint.org/docs/user-guide/command-line-interface)能够找到。

然后就可以检查代码了。

    eslint src

一下子输出了超多的error和warning信息，原来自己平时不注意的代码习惯其实非常的差，分号、空格、tab等等都太不严谨了。

总之，这是一款非常实用的工具，尤其是语法的检查、多人协作统一代码风格非常有用。这两天做测试学习了很多有用的工具。