---
title: CodePush集成指南
date: 2017-02-06 11:34:12
tags: react-native
category : javascript
---
本文以 React Native 初始项目AwesomeProject为例，总结应用部署/动态更新方面的内容。
<!-- more -->
# Install the CodePush CLI
    npm install -g code-push-cli
# Create a CodePush account

    code-push register
在弹出的网页里面注册，可以使用GitHub或者Microsoft账号  

**PS.相关命令**  

* `code-push login` 登陆  
* `code-push loout` 注销  
* `code-push access-key ls` 列出登陆的token  
* `code-push access-key rm <accessKye>` 删除某个 access-key  

# Register your app with the service

为了让CodePush服务器知道你的app，我们需要向它注册app： 在终端输入code-push app add <appName>即可完成注册。例如：

    code-push app add MyApp

**PS.相关命令**   

* `code-push app add` 在账号里面添加一个新的app  
* `code-push app remove` 或者 rm 在账号里移除一个app  
* `code-push app rename` 重命名一个存在app  
* `code-push app list` 或则 ls 列出账号下面的所有app  
* `code-push app transfer` 把app的所有权转移到另外一个账号

# CodePush-ify your mobile client

为你的移动客户端加上CodePush。

1.安装依赖

    npm install --save react-native-code-push@latest
或

    yarn add react-native-code-push@latest
2.安装插件
在高版本的React Native下，直接在项目根目录执行，就可以完成所有配置了，无需手动配置。

    react-native link react-native-code-push

之后你可以用Xcode或android studio进去看一下，所有的配置几乎都完成了。

修改android/app/build.gradle 的 versionName 为 "1.0.0"

    android{
        defaultConfig{
            versionName "1.0.0"
        }
    }

3.插件使用
用codePush高阶组件包一下你的根组件。

    import codePush from "react-native-code-push";

    class MyApp extends Component {
    }

    MyApp = codePush(MyApp);

为了更快的发现更新，可以加一些配置

    let codePushOptions = { checkFrequency: codePush.CheckFrequency.ON_APP_RESUME };

    class MyApp extends Component {
    }

    MyApp = codePush(codePushOptions)(MyApp);

# Release an app update

    code-push release-react <appName> <platform>

    code-push release-react MyApp ios
    code-push release-react MyApp android

之后就可以在你的手机上看到更新之后的变化了。

# Live long and prosper!  

祝编码愉快！  


参考：
1.[CodePush](https://microsoft.github.io/code-push/index.html#getting_started)
2.[React Native应用部署/热更新-CodePush最新集成总结](https://github.com/crazycodeboy/RNStudyNotes/blob/master/React%20Native%E5%BA%94%E7%94%A8%E9%83%A8%E7%BD%B2%E3%80%81%E7%83%AD%E6%9B%B4%E6%96%B0-CodePush%E6%9C%80%E6%96%B0%E9%9B%86%E6%88%90%E6%80%BB%E7%BB%93/Readme.md)
