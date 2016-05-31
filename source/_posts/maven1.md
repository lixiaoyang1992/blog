---
title: Apache Maven 入门
date: 2016-05-26 09:09:38
tags: Maven
category : Java
---

dedicated to you.  

## 下载  

Maven 官网的下载链接是：[Maven下载](http://maven.apache.org/download.cgi)  
下载，解压到你喜欢的目录

## 配置环境变量  

Maven 官网的配置指导是：[Maven配置](http://maven.apache.org/install.html)

系统变量：MAVEN_HOME = D:\Dev\apache-maven  （你解压的目录）  
系统变量：path = %MAVEN_HOME%\bin  

on mac

export MAVEN_HOME=/Users/lixiaoyang/Documents/apache-maven;  
export PATH=$PATH:$MAVEN_HOME/bin;

然后试一下  

    $ mvn -v

如果有结果就安装成功了

## Hello World  

执行命令  

    mvn archetype:generate -DgroupId=cn.lixiaoyang.helloworld -DartifactId=helloworld -Dpackage=cn.lixiaoyang.helloworld -Dversion=1.0-SNAPSHOT

会下载一些东西，最后会问你一些问题，一路回车就行了。  

然后我们可以尝试构建了

    cd helloworld
    mvn package
    java -cp target/helloworld-1.0-SNAPSHOT.jar cn.lixiaoyang.helloworld.App

控制台输出 Hello World! 代表成功了。

## POM (Project Object Model)  

所谓万物皆对象，一个工程当然也是对象。

    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>

      <groupId>cn.lixiaoyang.helloworld</groupId>
      <artifactId>helloworld</artifactId>
      <version>1.0-SNAPSHOT</version>
      <packaging>jar</packaging>

      <name>helloworld</name>
      <url>http://maven.apache.org</url>

      <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
      </properties>

      <dependencies>
        <dependency>
          <groupId>junit</groupId>
          <artifactId>junit</artifactId>
          <version>3.8.1</version>
          <scope>test</scope>
        </dependency>
      </dependencies>
    </project>


在 POM 中，groupId, artifactId, packaging, version 叫作 maven 坐标，它能唯一的确定一个项目。有了 maven 坐标，我们就可以用它来指定我们的项目所依赖的其他项目，插件，或者父项目。一般 maven 坐标写成如下的格式：

    groupId:artifactId:packaging:version

像我们的例子就会写成：

    cn.lixiaoyang.helloworld: helloworld: jar: 1.0-SNAPSHOT

我们的 helloworld 示例很简单，但是大项目一般会分成几个子项目。在这种情况下，每个子项目就会有自己的 POM 文件，然后它们会有一个共同的父项目。这样只要构建父项目就能够构建所有的子项目了。子项目的 POM 会继承父项目的 POM。

## Maven 生命周期  

我们用的第二个命令是：mvn package。这里的 package 是一个maven的生命周期阶段 (lifecycle phase )。生命周期指项目的构建过程，它包含了一系列的有序的阶段 (phase)，而一个阶段就是构建过程中的一个步骤。  

那么生命周期阶段和上面说的插件目标之间是什么关系呢？插件目标可以绑定到生命周期阶段上。一个生命周期阶段可以绑定多个插件目标。当 maven 在构建过程中逐步的通过每个阶段时，会执行该阶段所有的插件目标。  

maven 能支持不同的生命周期，但是最常用的是默认的Maven生命周期 (default Maven lifecycle )。如果你没有对它进行任何的插件配置或者定制的话，那么上面的命令 mvn package 会依次执行默认生命周期中直到包括 package 阶段前的所有阶段的插件目标：

process-resources 阶段：resources:resources

compile 阶段：compiler:compile

process-classes 阶段：(默认无目标)

process-test-resources 阶段：resources:testResources

test-compile 阶段：compiler:testCompile

test 阶段：surefire:test

prepare-package 阶段：(默认无目标)

package 阶段：jar:jar

## Maven 依赖管理  

之前我们说过，maven 坐标能够确定一个项目。换句话说，我们可以用它来解决依赖关系。在 POM 中，依赖关系是在 dependencies 部分中定义的。在上面的 POM 例子中，我们用 dependencies 定义了对于 junit 的依赖：

Xml 代码

    <dependencies>
      <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>3.8.1</version>
        <scope>test</scope>
      </dependency>
    </dependencies>

那这个例子很简单，但是实际开发中我们会有复杂得多的依赖关系，因为被依赖的 jar 文件会有自己的依赖关系。那么我们是不是需要把那些间接依赖的 jar 文件也都定义在POM中呢？答案是不需要，因为 maven 提供了传递依赖的特性。

所谓传递依赖是指 maven 会检查被依赖的 jar 文件，把它的依赖关系纳入最终解决的依赖关系链中。
