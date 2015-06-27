title: Ubuntu14.04 LTS 64下载并编译Android源码
date: 2015-01-26 20:22:30
tags: Android
---

记录下在Ubuntu14.04 LTS 64位系统下，下载并编译Android4.4中遇到的问题，方便自己以后查阅和给别人提供一些帮助。

######下载Android源码
(1)在下载之前，查看自己系统是否[符合要求](source.android.com/source/building.html)。
其中值得注意的是：


* JDK 7编译最新Android源码,**JDK 6**编译 Gingerbread至KitKat,JDK 5编译Cupcake至Froyo.
* 编译Android2.3.x或以上系统需要** 64位系统 **
* Python 2.6-2.7


(2)安装各种必须[软件](http://source.android.com/source/initializing.html#setting-up-a-linux-build-environment)

    $ sudo apt-get install bison g++-multilib git gperf libxml2-utils

(3)下载Android源码到具体目录

* [安装repo](http://source.android.com/source/downloading.html)<br>
  ```
   $ mkdir ~/bin
   $ PATH=~/bin:$PATH
   $ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
   $ chmod a+x ~/bin/repo
  ```
* 下载对应版本源码，我下载的是4.4的，故命令为：<br>
 ```
 repo init -u https://android.googlesource.com/platform/manifest -b android-4.4_r1.2
 repo sync 
```

这里，我遇到的一个问题是： repo init 时总是将.repo放在/home/your host name/目录下，看了下repo相关的内容发现，repo是封装了git功能的脚本，而我在对应目录的上层目录中已经有了一个.repo，故不再会在对应目录创建.repo.
其实.repo于.git类似。

######编译Android源码
* 初始化
```
$ source build/envsetup.sh
```
* 选择合适的Target,如
```
$ lunch aosp_x86-eng
```
* 编译代码
```
$ make -j4(这里的4可以根据自己系统的cpu调整，一般是核数*2)
```

这里，我遇到的问题是： 编译开始报各种错误，原因是安装必须软件时，有的软件没有安装完全造成的。在安装g++-multilib
时，总是出现依赖不满足的问题。使用-f 仍然没有用，后来将官方的源换成了国内的源后解决。