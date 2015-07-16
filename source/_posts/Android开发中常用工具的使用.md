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


#### android studio
Android Studio是Google开发d基于IDEA的IDE，所以理所当然继承它的很多功能，比如代码补全等非常nice的feature。我自己常用的一些快捷键如下：
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
对于那些刚从Eclipse转到Android Studio的程序员来说，必须要弄清楚两个概念，Project和Module。
(1)在Android Studio中，Project真实含义是工作空间，Module为一个具体的项目。
(2)在Eclipse中，我们可以同时对多个Project进行编辑，这些Project在同一个workspace中；在AS中，我们可以同时对多个Module进行编辑，这些Module在同一个Project中。
故Eclipse的Project相当于AS中的Module，Eclipse中的Workspace相当于AS中的Project。

















