title: Android开发基础知识总结
date: 2014-04-26 10:22:21
tags: Android
---

记录在初学Android开发时遇到的问题以及一些知识点，方便自己查阅.

######adb相关
1. 记得将adb所在目录添加到Path中;
2. adb是一个C/S模式的程序。client和server运行在开发机器上，daemon运行在emulator设备上。
3. adb常用命令: adb pull <remote> <local>, adb push <local> <remote>, adb install -r **.apk

######4大组件相关
