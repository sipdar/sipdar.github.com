---
layout: post
title: Core Animation (一)
---

#Core Animation (一)

一直很羡慕 github 上很多人写的炫酷的动画跟UI的特效。有时候自己想搞一个动画的时候就去 github 搜一下。作为一个iOS 开发人员 我决定从头学习 Core animation。

##Layer & View
Core animation 是一个误导人的名字，大家都觉得看这个名字，这个库应该是主要用来实现动画效果的。实际上，动画只是很小的一部分，更贴切的名字应该叫做 Layer kit。大部分都是关于图层的。屏幕上显示的不同图形图像 其实就是不同的Layer。 Layer 有自己的父图层 也有自己的子图层，它们构成了一个树形的层次结构。这个树形结构构成了UIKit的底层，和屏幕上我们所见的一切。
我们都用过 UIView ，view 就是屏幕上显示文本 图片或者视频的一个矩形区域，并且它可以相应我们的各种手势操作，每个View 可以包含多个view从而构成一种层级结构。父View 管理子View的位置。

![view layer](http://m1.img.libdd.com/farm4/2013/0823/11/731FE6D6A68C3C1013ED9C66E49567D2102577ED96875_652_387.JPEG)

在iOS中 所有的View 都继承自UIView，UIView 处理触摸事件，并且支持基于 **Core Graphics**的显示，仿射变换，例如旋转跟缩放，以及简单的滑动或者翻转动画。但是UIView 并不是处理所有的这些细节，渲染 布局和动画其实是 **CALayer** 来做的。

##CALayer
CALayer其实跟UIView 非常相似，他是一个矩形的方块，在一个层级结构中，包含图片文字或者背景颜色，也是由父类来管理显示的位置。CALayer 有专门的方法和属性来实现动画和几何变换。和UIView 唯一不同的一点就是 Layer 不处理和用户的交互逻辑。
CALayer并不是响应连的一部分，所以无法响应用户的交互，但是它提供了方法来检查用户点击的位置是否在Layer中。

##平行的层级结构
每个UIView 都有一个CALayer 对象，UIView可以创建或者删除这个CALayer。CALayer 的层级结构就像是UIView结构的一个镜像。事实上是图层负责显示UI跟动画以及其他你在屏幕上所见到的东西。UIView 只是简单地封装了一下，处理IOS特有的touch事件，并把底层的Core Animation 接口封装成高级接口。
iOS 之所以有UIView 和 CALayer 两套平行的层级结构，是为了更好的解耦代码，将UI和用户的交互逻辑分离开来。因为iOS的多点触控的处理比起Mac OS的键盘鼠标来要复杂的多。事实上如果你的UI布局使用 Core Animation 的框架来写，是可以在iOS和Mac OS  上通用的

##图层的作用
CALayer 提供了 UIView没有提供的一些属性，例如

* 阴影，矩形的圆角，边框的颜色
* 3D矩阵变换
* 非矩形的边框
* 透明的遮罩
* 多步，非线性的动画。
