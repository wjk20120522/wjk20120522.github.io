title: Android屏幕适配
date: 2013-12-29 14:22:32
tags: Android
---

#### Android屏幕适配出现原因
支持Android的设备尺寸大小不同，屏幕尺寸大小也不尽相同，即存在屏幕的碎片化。
目前主流的Android设备的屏幕分辨是1280\*720, 800\*480, 854\*480, 1920\*1080, 960\*540, 1184\*720, 480\*320.具体数据来自[友盟指数](http://www.umindex.com/devices/android_resolutions)。我们尽量适配这几种分辨率，就可以在大部分的手机上正常运行了。

<!--more-->

#### 相关概念

屏幕尺寸：屏幕对角线长度，1英寸=2.54厘米。比如小米4屏幕尺寸为5英寸。
屏幕分辨率：横纵方向上的像素点数，单位是px。1px为1个像素点，如小米4的屏幕分辨率为1920\*1080.
屏幕像素密度：每英寸上的像素点数，单位是dpi.即"dot per inch". 我之前一直把它与dip混淆掉==

##### px, dp, dip, dpi, sp

* px像素的意思。

* dip和dp是一个意思，都是Density Independent Pixels的缩写，即密度无关像素，上面我们说过，dpi是屏幕像素密度，假如一英寸里面有160个像素，这个屏幕的像素密度就是160dpi，那么在这种情况下，dp和px如何换算呢？在Android中，规定以160dpi为基准，1dip=1px，如果密度是320dpi，则1dip=2px，以此类推。

* sp，即scale-independent pixels，与dp类似，但是可以根据文字大小首选项进行放缩，是设置字体大小的御用单位。

##### mdpi, hdpi, xdpi, xxdpi
mdpi,hdpi,xdpi,xxdpi用来修饰Android中的drawable文件夹及values文件夹，用来区分不同像素密度下的图片和dimen值。
那么如何区分呢？Google官方指定按照下列标准进行区分：

| 名称        | 像素密度范围   |
| --------    | -----:         |
| mdpi        | 120dpi~160dpi  |  
| hdpi        | 160dpi~240dpi  |
| xhdpi       | 240dpi~320dpi  |
| xxhdpi      | 320dpi~480dpi  |
| xxxhdpi     | 480dpi~640dpi  |


#### 屏幕适配解决方案
1. 使用wrap_content, match_parent, weight
如果使用"wrap_content"和"match_parent"尺寸值而不是硬编码的尺寸，视图就会相应地仅使用自身所需的空间或展开以填满可用空间。此方法可让布局正确适应各种屏幕尺寸和屏幕方向。
2. 使用相对布局，禁用绝对布局
3. 使用限定符
	(1)尺寸限定符。res/layout/main.xml和res/layout-large/main.xml,系统会在较大屏幕(如平板)的设备上选择此布局
	(2)最小宽度限定符。res/layout-sw600dp/main.xml指明最小宽度为600dp的屏幕。(PS:Android版本低于3.2设备不支持此技术，设备无法将sw600dp识别为尺寸限定符，因此仍需使用large限定符)
4. 使用布局别名
5. 使用自动拉伸位图(.9.png)
6. 使用dp和sp

关于第6点，有个细节被忽视了。虽然dp可以去除不同像素密度的问题，是的1dp在不同像素密度上面的显示效果相同，但是由于Android屏幕设备多样性，<font color="red">**并非所有的屏幕的宽度都是相同的dp长度**</font>。

所以，我们尽量使用warp_content和match_parent，少用dp来指定控件的具体长宽，结合权重，大部分情况我们都是可以做到适配的。

------

上面的内容对于日常开发而言其实已经足够了。 目前还是有两种方法尝试更彻底解决屏幕适配问题，下面就讲一下大概原理。

1. 假设手机屏幕的宽度都是320单位长度，我们将每个屏幕宽度总像素平均分成320份，每一份对应具体的像素就可以了。
缺点：使用这个方案必须包含所有的分辨率，如果分辨率缺少，有出错的风险。同时这也会增加软件包大小和维护难度。

2. 上面的方法想法很好，但是不太现实。幸运的是，Google已经为我们考虑了我们的这种担忧。最近Google开发出了一个android-percent-support库，基本可以解决屏幕适配的问题了。这个库提供了两个布局供大家使用：
PercentRelativeLayout、PercentFrameLayout他们继承自RelativeLayout和FrameLayout容器。支持的属性有：
layout_widthPercent、layout_heightPercent、 
layout_marginPercent、layout_marginLeftPercent、 
layout_marginTopPercent、layout_marginRightPercent、 
layout_marginBottomPercent、layout_marginStartPercent、layout_marginEndPercent。

对这个库的具体分析以后给出 ==

-------------------
参考文献
1. http://developer.android.com/guide/topics/resources/more-resources.html#Dimension
2. http://stackoverflow.com/questions/2025282/difference-between-px-dp-dip-and-sp-in-android
3. http://blog.csdn.net/zhaokaiqiang1992/article/details/45419023