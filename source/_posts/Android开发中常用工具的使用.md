title: Android开发中常用工具的使用
date: 2015-07-13 10:28:04
tags: Android
---

本文主要总结下自己在实习期间使用的一些工具，具体在使用过程碰到的一些问题以及自己的解决办法。
#### git以及sourceTree
git的操作基本以前也学过，基本可以参考[git使用简易指南](http://www.bootcss.com/p/git-guide/)
以前在命令行下用的最多的几条命令就是：
```
git clone
git add *
git commit -m "message"
git push 
git checkout
```
我觉得要想了解Git的工作原理，我们需要知道工作区(Working dir)，缓存区(Index)和Head之间的联系与区别。
由于我们使用了sourceTree图形工具来简便Git操作，好多命令都已经封装好了，只要知道基本的用法在GUI上操作也比较方便。
#### maven, gradle
现在Android Studio采用了gradle脚本进行构建，Gradle结合了Ant和maven的优点。但是，工作环境中仍然有使用maven进行远程打包。所以，便在空闲时间学习了相关的知识内容。

maven是项目自动化构建与依赖管理工具

###### maven的安装和配置
具体的安装步骤就不细说了，可以参考[官方文档](https://maven.apache.org/install.html)。
安装后的目录结构为：
```
bin	包含mvn运行的脚本，用来配置Java命令
boot 只有一个plexus-classworlds-*.jar的文件，它是一个类加载器框架，提供了丰富的语法已方便配置，maven使用该框架加载自己的类库。
conf 包含重要的文件settings.xml.可以全局定制maven的行为。
lib	包含maven运行时需要的Java类库。
LICENSE.txt	maven使用的软件许可证 Apache Licence Version 2.0
NOTICE.txt
README.txt
```
###### maven使用入门
就像Make的Makefile,Ant的build.xml一样，Maven项目的核心是pom.xml。POM(Project Object Model,项目对象模型)定义了项目的基本信息，用于描述项目如何构建，声明项目依赖等等。
1. 编写POM
POM文件中重要的三个元素: groupId, artifactId, version, 这三个元素定义了一个项目基本的坐标。
* groupId定义项目属于哪个组，一般与所在组织或公司关联
* 定义当前maven项目在组中唯一的ID
* 项目版本，SNAPSHOT说明还处于开发中，是不稳定版本

maven最主要的命令：mvn clean compile, mvn clean test, mvn clean package, mvn clean install.
test之前是会先执行compile的，执行package之前是会先执行test的，而类似地，install之前会执行package。

###### 坐标和依赖
maven坐标为各种构建引入了秩序，任何一个构建都必须明确定义自己的坐标，而一组maven坐标是通过一些元素定义的，如groupId, artifactId, version, packaging, classifier. 

#### android studio
* Android Studio是Google开发d基于IDEA的IDE，所以理所当然继承它的很多功能，比如代码补全，超强但重构功能和超强的代码分析等非常nice的feature。它使用Gradle管理依赖项，方便第三方库的引用和升级。
* 对于那些刚从Eclipse转到Android Studio的程序员来说，必须要弄清楚两个概念，Project和Module。
(1)在Android Studio中，Project真实含义是工作空间，Module为一个具体的项目。
(2)在Eclipse中，我们可以同时对多个Project进行编辑，这些Project在同一个workspace中；在AS中，我们可以同时对多个Module进行编辑，这些Module在同一个Project中。
故Eclipse的Project相当于AS中的Module，Eclipse中的Workspace相当于AS中的Project。

* 我自己常用的一些快捷键如下：(Windows)
```
ctrl + N 			快速打开某个类 
shift 双击			快速打开某个文件
ctrl ＋ Q			查看某个API的说明文档
```
对了，可以开启自动import功能。
提供Android Studio编译速度的几个小技巧：
* 在项目根目录下的gradle.properties里面加入下面配置：
```
org.gradle.daemon=true
org.gradle.parallel=true
org.gradle.jvmargs=-Xmx1024m
```
其中jvmargs根据各自机器可以调整。
* 打开增量编译。在项目主Module下的build.gradle中加入：
```
dexOptions {
	incremental true
}
```














