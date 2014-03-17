---
layout: post
title: Coordinate Space Transformations
---

##Coordinate Systems
Quartz 2D和UIKit的坐标系是不同的，Quartz 2D 使用的是笛卡尔坐标系 原点在左下角，
![image](http://sipdar.github.io/image/2014-03-16/Quartz2DCoordinate.png)

UIkit坐标系的原点在左上角。

![image](http://sipdar.github.io/image/2014-03-16/native_coordinate_system.jpg)

 这个也就可以理解为什么用Quartz 2D画的图形和UIKit的倒过来的。
 
 但是在IOS 3.2及后续的版本中，当UIKit为你的应用程序创建一个**Graphics Context**时，也对**Context**进行了额外的修改以匹配UIKit的约定。

##What is CTM ?
**CTM** = **Current Transformation Matrix**

Quartz通过使用当前转换矩阵(current transformation matrix， CTM)将一个独立的坐标系统(user space)映射到输出设备的坐标系统(device space)，**CTM** 通过平移(translation)、旋转(rotation)、缩放(scale)操作将点从一个坐标空间映射到另外一个坐标空间。

我们可以通过CTM来决定对象如何被绘制。例如，为了绘制一个旋转了45度的矩形，我们可以在绘制矩形之前旋转**Graphics Context**的坐标系统。Quartz使用旋转过的坐标系统来将矩形绘制到输出设备中。设备显示在屏幕上的矩形，就是旋转过后的矩形。

我们先来看一个图片绘制的例子,首先是是没做矩阵转换的代码。

	UIGraphicsBeginImageContextWithOptions(CGSizeMake(320, 320),NO ,[UIScreen mainScreen].scale);
	// Perform drawing here
	UIBezierPath *path = [UIBezierPath bezierPathWithRect:CGRectMake(50, 50, 200, 200)];
	[[UIColor redColor] setFill];
	[path fill];
	UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
	self.imageView.image = image;
	UIGraphicsEndImageContext();

![image](http://sipdar.github.io/image/2014-03-16/CTM_Original.png)


##CGContextTranslateCTM

	UIGraphicsBeginImageContextWithOptions(CGSizeMake(320, 320),NO ,[UIScreen mainScreen].scale);
	// Perform drawing here
	CGContextRef context = UIGraphicsGetCurrentContext();
	CGContextTranslateCTM(context, 70, 0);
	UIBezierPath *path = [UIBezierPath bezierPathWithRect:CGRectMake(50, 50, 200, 200)];
	[[UIColor redColor] setFill];
	[path fill];
	UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
	self.imageView.image = image;
	UIGraphicsEndImageContext();

![image](http://sipdar.github.io/image/2014-03-16/CTM_Translate.png)
##CGContextScaleCTM

	CGContextScaleCTM(context, 0.5, 1.5);
	
![image](http://sipdar.github.io/image/2014-03-16/CTM_Scale.png)

##CGContextRotateCTM

	CGContextRotateCTM(context, M_2_PI);
	
![image](http://sipdar.github.io/image/2014-03-16/CTM_Rotate.png)

这时候我们发现旋转并没有按照绘制的**Context**中心旋转， 因为旋转是以坐标系的原点为中心来做的，所以我们为了要保持图片的中心位置就要配合 **CGContextTranslateCTM** 一起使用。

	CGContextTranslateCTM(context,self.imageView.center.x,self.imageView.center.y);
	CGContextRotateCTM(context, M_2_PI);
	CGContextTranslateCTM(context, -self.imageView.center.x, -self.imageView.center.y);

![image](http://sipdar.github.io/image/2014-03-16/CTM_Rotate_withCenter.png)

##CGContextConcatCTM
此外我们还可以通过 **CGAffineTransform **来变幻坐标系 比如我之前写过的仿射变换。


	CGContextTranslateCTM(context,self.imageView.center.x,self.imageView.center.y);
	CGAffineTransform transform = CGAffineTransformIdentity;
	transform = CGAffineTransformScale(transform, 0.5, 0.5);
	transform = CGAffineTransformTranslate(transform, 0, 100);
	transform.c = -0.5;
	transform.b = 0;
	CGContextConcatCTM(context, transform);
	CGContextTranslateCTM(context, -self.imageView.center.x, -self.imageView.center.y);

![image](http://sipdar.github.io/image/2014-03-16/CTM_Transform.png)


## CGContextGetCTM
我们可以通过 **CGContextGetCTM** 在程序运行中取回当前**Context**上使用的转换矩阵。
