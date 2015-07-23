title: Android中的单位和尺寸
date: 2013-12-29 14:22:32
tags: Android
---

由于Android手机有着各种尺寸和屏幕分辩率，在开发UI界面的时候，px和pt尺寸单位已经不能满足需要了。为了能够让界面在不同的手机上观感保持一致，Android引入了一套新的单位dp和sp.

-------------------

### px和pt
#### px
即pixel，像素的意思。屏幕上可以显示的最小元素单元。比如相同大小的两个手机，屏幕分辨率不同，则用px会使UI在两个手机上显示不同大小。
#### pt
即Point，磅数的意思。72 pt = 1 inch。一般作为字体的单位使用。

在PC上，这两个单位可以很好的使用。但是，手机由于存在各种**分辩率**的问题，会导致UI显示极度不一致。

-------------------
### dp和sp
#### dp
即Density-independent Pixels，密度无关像素的意思。Android[开发者文档中](http://developer.android.com/guide/topics/resources/more-resources.html#Dimension)规定，这些单位与160dp相关。比如160px的屏幕，1dp = 1px. 而320px的屏幕，1dp = 2px。
Android应用一般提供了几个通用目录如drawable-ldpi, drawable-mdpi, drawable-hdpi等供存放不同分辨率的图片。
一般有如下的性质：
ldpi 	120dp 	
mdpi 	160dp
hdpi 	240dp
xhdpi	320dp
xxhdpi 	480dp
xxxhdpi 640dp


#### sp
即Scale-independent Pixels，缩放无关的像素的意思。与sp类似，不过主要是针对字体的大小。

### 使用法则
sp用作指定**文字**大小的单位，其余用dp.

-------------------
参考文献
1. http://developer.android.com/guide/topics/resources/more-resources.html#Dimension
2. http://stackoverflow.com/questions/2025282/difference-between-px-dp-dip-and-sp-in-android