title: Android屏幕视频录制
date: 2015-09-15 09:56:04
tags: Android
---

动力：在Android真机调试的时候，总是需要将USB线连接到电脑，有时候这样很不方便。于是便尝试在Google了下看看是否不需要USB线可以调试真机。

刚开始找到的便是比较出名的AirDroid.
AirDroid是一款运行在安卓手机上的免费软件，它能让你直接在浏览器中管理你的手机，传图传歌装软件发信息神马都行！你不需在电脑端安装软件，也不需找数据线，你需要的就是WiFi+浏览器而已。先将 AirDroid 安装到手机中，确保电脑和手机在同一个无线局域网内，那么你就从此可以跟数据线/PC端管理软件说拜拜了！
[项目网址](https://www.airdroid.com/zh-cn/)


偶然间发现了可以通过adb命令来在局域网调试真机。[博客](http://stormzhang.com/android/2014/08/27/adb-over-wifi/)
小米4测试可行。
现在就可以通过adb命令在wifi下调试你的应用了。
比如手机截图：
adb shell screencap -p | perl -pe 's/\x0D\x0A/\x0A/g' > screen.png
也可以录制手机屏幕的视频：
adb shell screenrecord /sdcard/movie.mp4    （随时按control-c终止）

看到上面，你是不是觉得可以做些小坏事^_^




