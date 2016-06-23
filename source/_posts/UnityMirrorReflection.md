---
title: Unity Mirror Reflection 镜子反射
date: 2016-06-23 11:00:16
tags: [unity]
categories: [Unity,Shader]
---

#Reflection 镜子反射
在unity3.5的时候就看见官方那个换装的demo工程，那时候没留意这个包
还这么多功能性的东西：换装，镜子，AssetBudle等。
今天看到突然看到Unity5的TexGen，原来看不懂的Unity4的TexGen突然明白
了一点，里面说到水晶球效果。记起来一个镜子反射的效果，查了一下，
就发现了两篇
>http://wiki.unity3d.com/index.php/MirrorReflection4  
>http://wiki.unity3d.com/index.php/MirrorReflection2

<!--more-->
找到官方的例子看看，果真不错：

![][2]

# 研究一下
  主要的两个脚本MirrorReflection.cs，MirrorReflection.shader。

  * 先复制一个相机
  * 由镜子为参考面，镜像
  * 复制相机的裁剪面，以镜子屏幕参考，设置斜的裁剪面。
  * 渲染一个RT
  * 传送Camera的矩阵给shader。（在Scene与Game中的相机矩阵是不同的,
  Scene使用的是视野相机）
  * 在shader中映射view

# 使用
把 MirrorReflection.cs 拖到一个plane上。设置plane的mat的shader为MirrorReflection.shader
即可。

![][1]

下载：[Mirror Project][3]
[1]: UnityMirrorReflection/0.png
[2]: UnityMirrorReflection/mirror.gif
[3]: http://git.oschina.net/wuzhi/Blog/tree/master/project4/Mirror