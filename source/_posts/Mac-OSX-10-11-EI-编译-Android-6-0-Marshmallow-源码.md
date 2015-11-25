title: Mac OSX 10.11 EI 编译 Android 6.0 Marshmallow 源码
date: 2015-11-25 10:29:10
tags:
---

想在Android Studio上看Android源码，于是便尝试着编译源码导入AS中，遇到了些问题，记录与此。

###### Mac平台设置
由于Mac的文件系统默认是Case insensitive的，即不区分大小写。(可尝试在同一个目录下创建字母相同的，大小写不同的文件，看看是否可以创建两个文件)在编译Android源码的时候会出现问题。故需要新创建一个case-sensitive的disk image.具体命令为：
```
hdiutil create -type SPARSE -fs 'Case-sensitive Journaled HFS+' -size 55g ~/放在某个目录下的文件.dmg
```
**注意**，官方文档上命令是创建40g的磁盘镜像文件，但是实际在编译时即使删除.repo文件夹空间扔不够用。

<!-- more -->

###### 下载源码
按照[官方教程](https://source.android.com/source/downloading.html)下载源码。
由于某种大众皆知的原因，我们可以去国内的镜像网站去下取。这里推荐[中国科技大学的镜像](https://lug.ustc.edu.cn/wiki/mirrors/help/aosp)下载，速度比较快。

###### 编译源码
Mac OSX 的EI Capitan，新于代码的配置文件，因此要修改配置文件。它位于build/core/combo/mac_version.mk，其中有一个 mac_sdk_versions_supported 变量，修改为我目前的版本10.11。

参考官方指南[Building the System](https://source.android.com/source/building.html)编译系统。


具体安装细节可参考[Ubuntu14.04 LTS 64下载并编译Android源码](http://wjk20120522.github.io/2015/01/26/Ubuntu14.04%20LTS%2064%E4%B8%8B%E8%BD%BD%E5%B9%B6%E7%BC%96%E8%AF%91Android%E6%BA%90%E7%A0%81/)

###### 代码导入Android Studio
进入Android源码主目录然后执行下面命令
```
mmm development/tools/idegen/
sh ./development/tools/idegen/idegen.sh
```
执行完成后，打开AS，新建项目，选择Android源码根目录，导入即可。
如果提示case-sensitive相关的warning可以在/Applications/Android\ Studio.app/Contents/bin/idea.properties文件中添加一行：
```
idea.case.sensitive.fs=true
```
重启ok


###### 参考文献
[Android 6.0 源代码编译实践](http://www.iteye.com/news/31146)



