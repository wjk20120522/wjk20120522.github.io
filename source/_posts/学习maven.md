title: 学习maven
date: 2015-07-25 13:26:00
tags: Android
---

maven是项目自动化构建与依赖管理工具

#### maven的安装和配置
具体的安装步骤就不细说了，可以参考[官方文档](https://maven.apache.org/install.html)。
安装后的目录结构为：

* bin		包含mvn运行的脚本，用来配置Java命令
* boot 	只有一个plexus-classworlds-*.jar的文件，它是一个类加载器框架，提供了丰富的语法已方便配置，maven使用该框架加载自己的类库。
* conf 	包含重要的文件settings.xml.可以全局定制maven的行为。
* lib		包含maven运行时需要的Java类库。
* LICENSE.txt		maven使用的软件许可证 Apache Licence Version 2.0
* NOTICE.txt
* README.txt

#### maven使用入门
就像Make的Makefile,Ant的build.xml一样，Maven项目的核心是pom.xml。POM(Project Object Model,项目对象模型)定义了项目的基本信息，用于描述项目如何构建，声明项目依赖等等。
1. 编写POM
POM文件中重要的三个元素: groupId, artifactId, version, 这三个元素定义了一个项目基本的坐标。
* groupId定义项目属于哪个组，一般与所在组织或公司关联
* 定义当前maven项目在组中唯一的ID
* 项目版本，SNAPSHOT说明还处于开发中，是不稳定版本

maven最主要的命令：mvn clean compile, mvn clean test, mvn clean package, mvn clean install.
test之前是会先执行compile的，执行package之前是会先执行test的，而类似地，install之前会执行package。

#### 坐标和依赖
maven坐标为各种构建引入了秩序，任何一个构建都必须明确定义自己的坐标，而一组maven坐标是通过一些元素定义的，如groupId, artifactId, version, packaging, classifier. 

#### 多个模块的依赖配置
1. 消除多模块依赖配置重复
比如A，B模块都依赖了log4j,则需要在A，B的pom里面引入依赖如：
```
<dependency>
	<groupId>log4j</groupId>
	<artifactid>log4j</artifactid>
	<version>4.8.2</version>
</dependency>
```
A,B在各自模块引入log4j依赖，可能会出现版本不一致等问题。maven提供了解决办法，使用继承机制以及dependencyManagement元素就能解决这个问题。**dependencyManagement只会影响现有依赖的配置，但不会引入依赖**。这样子模块可以按需引入依赖。

