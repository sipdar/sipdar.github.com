---
layout: post
title: Core Animation (七)3DTransform
---


#3D Transform
**CGAffineTransform** 属于 **Core Graphics Framework** ,**Core Graphic**是一个绘制2D图形的库。**CGAffineTransform** 只提供 2D 的旋转缩放移动等。我们要想实现 3D矩阵下的旋转缩放就要用到**CATransform3D**。在2D动画中，**point**属性只有 X Y 值，在3D坐标中，多了一个 Z 值。它代表的是当前的Layer距离用户视角的远近，正是这个Z值 使我们可以在3D坐标系中旋转缩放移动。

**CATransform3D** 跟 **CGAffineTransform** 一样也是个矩阵，是一个 4x4 的矩阵结构定义如下。
	
	struct CATransform3D
	{
	  CGFloat m11, m12, m13, m14;
	  CGFloat m21, m22, m23, m24;
	  CGFloat m31, m32, m33, m34;
	  CGFloat m41, m42, m43, m44;
	};
	typedef struct CATransform3D CATransform3D;
	
3D转换的计算如下。

![image](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/Art/transform_basic_math_2x.png)


下面是几种常用的3D变换矩阵 跟 **CGAffineTransform** 矩阵非常相似


![image](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/Art/transform_manipulations_2x.png)


**Core Graphic** 提供了一些列的函数使我们可以方便简单的实现 3D变换。

	CATransform3DMakeRotation(angle,x,y,z);
	CATransform3DMakeScale(sx,sy,sz);
	CATransform3DMakeTranslation(tx,ty,tz);

我们看下面这个例子

	UIImage *image = [UIImage imageNamed:@"DragonMedium"];
	UIImageView *imageView = [[UIImageView alloc] initWithImage:image];
	imageView.backgroundColor = [UIColor greenColor];
	imageView.frame = CGRectMake(0, 0 , CGRectGetWidth(imageView.frame),
								 CGRectGetHeight(imageView.frame));
	imageView.center = self.view.center;
	
	CATransform3D transform = CATransform3DMakeRotation(M_PI_4, 0, 1, 0);
	imageView.layer.transform = transform;
	[self.view addSubview:imageView];

![image](http://m1.img.libdd.com/farm5/2013/1020/22/00594CE26CC3572725BEF8911D9A93EBE1B6259EAEAD7_594_856.PNG)

好像只是简单的压缩了宽度。并没有出现我们预期的3D 沿 Y轴旋转的效果，这是为什么呢。我们知道，要想在2D平面表现3D效果，无论是拍照，还是绘画都要用到一个叫做透视法的东西，当我们距离一个物体越远的时候，我们看它就越小，在**CATransform3D**有一个值是控制透视效果的，就是 **m34** .默认的情况下 **m34**的值是零，意味着我们是在元素的角度看到UI，我们把上面的代码稍加改造。

	CATransform3D transform =	CATransform3DIdentity;
	transform.m34 = -1.0 / 500.0;
	transform = CATransform3DRotate(transform,M_PI_4, 0, 1, 0);
	imageView.layer.transform = transform;

![image](http://m3.img.libdd.com/farm5/2013/1020/22/D23F55284B47C8C51F62F170CAA35113AA8262A12DC96_594_856.PNG)
这次看上去就对了。我们将**m34** 的值设为 **-1.0/d** .d 表示的就是虚拟的摄像头跟屏幕的距离，因为摄像头并不是真的存在，这个值通常是靠感觉来调整的，一般来说 500 到 1000 都是不错的值。 可以多尝试几次。