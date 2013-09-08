---
layout: post
title: Core Animation (三)  Layout
---


#Core Animation (三)

##布局
**UIView** 有三个跟布局有关的属性，**frame** **bounds** **center**。 **CALayer** 也有三个类似的属性，**frame** **bounds** **position** 。view 使用 center ，layer 使用position。实际上他们是一样的。

![image](http://m2.img.libdd.com/farm4/2013/0908/15/2875A64D846B3A739E72CBE40653C6C84E8E392A3B4B6_540_309.JPEG)

**frame** 跟**bounds**的区别就是参照系不一样。 frame是以superView 做参照系。 而bounds 是以自己做参照系。上面这幅图就是个很好的例子。**layer** 跟**view**是一样的。

![image](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/Art/rotated_view.jpg)

frame 并不是个确定的值。它会根据 bounds center 以及做动画的时候的transform 动态的改变。例如上面这个图片旋转了45度 frame就变大了。这时。frame的size就跟bounds的size 不一样了。
![image](http://m2.img.libdd.com/farm4/2013/0908/15/6B69C122D6E1A980BA9FBE74E20E63D144DA6172230CC_600_400.PNG)

旋转之后的frame 变大了。

##锚点（anchorPoint）

图层的 **anchorPoint** 属性指定了一个基于图层 **bounds** 的符合位置坐标系的位置。锚点指定了 **bounds** 相对于 **position** 的值,同时也作为变换时候的支点。锚点使用单元空间坐标系表示,**(0.0,0.0)**点接近图层 的原点,而**(1.0,1.0)**是原点的对角点。改变图层的父图层的变换属性将会影响到 **anchorPoint** 的方向,具体变化取决于父图层坐标系的 Y 轴。

当你设置图层的 **frame** 属性的时候,**position** 会根据锚点相应的改变,而当你设置图层的 **position** 属性的时候,**bounds**会根据锚点做相应的改变。锚点的默认值是**(0.5,0.5)**.

下面几幅图片展示了锚点的位置对于 **position**的影响。
![image](http://m3.img.libdd.com/farm4/2013/0908/15/C9DA0E164914D457005A246C534030BD82A87510B80BC_657_726.PNG)
![image](http://m1.img.libdd.com/farm4/2013/0908/15/7F64FC799F69C56600175ACAF10941B6BF93D381B4E36_657_639.PNG)
