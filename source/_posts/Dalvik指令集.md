title: "Dalvik指令集"
date: 2014-05-10 22:44:47
tags:  Android Security
description: 
---

主要介绍Dalvik指令集的一些基础知识，以后别人以及自己以后查阅.
先介绍下Dalvik字节码的类型、方法与字段表示方法.
###### 类型
Dalvik字节码只包含两种类型，基本类型与引用类型.

|语法    |   含义    |
|:--------:|:------------:|
|      V     |         void      |
|      Z     |    boolean   |
|     B      |      byte         |
|      S     |      short         |
|      C     |        char       |
|       I    |          int     |
|       J    |            long   |
|       F   |           float    |
|       D   |             double  |
|       L    |           Java类类型    |
|       [    |            数组类型   |

###### 方法
方法的格式如下:
Lpackage/name/ObjectName;->MethodName(III)Z
其中,Lpackage/name/ObjectName是对应类的类型，与方法名MethodName用;相隔.括号中为函数参数，最后的Z为函数返回类型.

##### 字段
与方法类似,格式如下:
Lpackage/name/ObjectName;->FieldName:Ljava/lang/String;

#### Dalvik指令集
下面介绍具体类型的指令。在介绍之前，先熟悉下指令特点

-   参数从目标（destination）到源（source）的方式
-   64位常规类型字节码添加 -wide后缀
- 根据字节码布局与选项不同，一些字节码添加了字节码后缀以消除歧义。这些后缀通过在字节码主名称后添加斜杠“／”来分割开。

###### 空指令
无实际操作，一般用于指令对齐

###### 数据操作指令
move vA, vB
move/from16 vAA, VBBBB
move/16  VAAAA,VBBBB
move-wide VA,VB
move-object VA,VB
move-result VAA
move-result-wide VAA
move-result-object VAA
move-exception VAA

###### 返回指令



