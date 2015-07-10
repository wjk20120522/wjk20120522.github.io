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
四大组件：Activity, Service, Content Provider, BroadcastReceiver.
* Activity主要是用来显示UI界面的，用户可以看见并与之交互的
* Service主要是在后台，处理些耗时之类的任务
* Content Provider主要是提供应用程序间数据的共享
* BroadcastReceiver接受来自系统的指定类型的广播，从而进行相应的处理

###### Activity
1. 生命周期： onCreate-> onStart-> onResume-> onPause-> onStop-> onDestroy. Fragment也有类似的生命周期。
2. 四种启动模式：
	standard: 默认模式，在这个模式下默认创建一个新的实例。
	singleTop: 可以有多个实例，但是不允许有两个相邻相同Activity。创建与栈顶相同Activity时不会创建新的实例，而会调用其onNewIntent方法
	singleTask: 只有一个实例。在同一个应用程序中启动时若Activity不存在，则会在当前task创建一个新的实例，若存在，则会把task中其上activity删除掉并调用onNewIntent方法；若在别的应用程序中启动它，则会创建一个task，并在该task中启动这个Activity，并且这个Activity允许别的Activity与其共在一个task中。

	singleInstance: 只有一个实例，并且这个实例独自运行在一个task中，不允许有别的Activity存在。

























