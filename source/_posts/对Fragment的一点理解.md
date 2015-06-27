title: 对Fragment的一点理解
date: 2014-01-13 16:23:04
tags: Android
---

[官方文档](developer.android.com/training/basics/fragments)上的内容：
* 创建一个Fragment
* 创建灵活UI
* 与其它Fragment交互

######创建一个Fragment
[Note]若应用需要运行在3.0版本一下，需要导入Support Library.
主要创建步骤：
(1)创建一个继承Fragment的类，就像是Activity.
(2)在XML中将Fragment添加在activity.
(3)在activity中加载这个布局.
这里,我们需要了解下Fragment的生命周期。如下图所示：
![Fragment_LifeCycle](http://7u2ido.com1.z0.glb.clouddn.com/Fragment_LifeCycle.png)
###### 创建灵活UI
如果APP需要指出不同屏幕之间的适配，则需要重用fragment来优化用户体验。如在手机设备上一次只有一个fragment而Tablet上则可以放两个fragment.如下图：
![Tablet VS phone](http://7u2ido.com1.z0.glb.clouddn.com/fragments-screen-mock.png)
FragmentManager提供了Activity运行时的Fragment的添加、删除、替换等操作。即通过FragmentManager对象获得一个FragmentTransaction对象，通过该对象的API进行各种操作。
值得注意的一点是：如果想在按Back按键后返回上一个碎片，则需要在commit之前调用addToBackStack()。
###### 与其他Fragment交互
两个Fragment之间不应直接通信，最好用他们所在的Activity作为沟通的桥梁。
Fragment与Activity通信：
* FragmentManager可以通过findFragmentById()获取对应Fragment
* Fragment可以通过调用getActivity()得知相关联的Activity

######参考文献
* http://developer.android.com/training/basics/fragments/