---
layout: post
title: Transparency Layers
---
**Transparency Layer** 是由多个对象组合在一起拼成的图形，它在图形绘制的过程中会被当作一个对象来处理。 **Transparency Layer** 最常见的用处就是在我们想对一组图形添加效果的时候。比如像下面这样对三个叠加在一起的三个圆形添加阴影。

![image](http://sipdar.github.io/image/2014-03-17/circlesWithShadow.png)

如果我们添加阴影的时候没用把这三个圆形绘制在 **Transparency Layer** 上的话 那么阴影则将会添加在每个圆形周围上。

![image](http://sipdar.github.io/image/2014-03-17/circlesInnerShadow.png)

使用 **Transparency Layer** 的时候，我们首先要在绘制 **Transparency Layer** 开始的地方调用
**CGContextBeginTransparencyLayer** 它需要两个参数，一个就是我们绘制图形的**graphics context** 另外一个是个**CFDictionary**对象，通过第二个参数我们可以控制一些绘制图形的属性，比如 阴影的开关 图形混合的类型 和图形的alpha值。一般的情况下都是传**NULL**。接下来我们就可以执行我们绘图的代码了。 画完之后在	**UIGraphicsEndImageContext** 或者 **UIGraphicsPopContext** 之前我们需要调用 **CGContextEndTransparencyLayer**来结束 **Transparency Layer**的绘制。此外，我们还可以使用 **CGContextBeginTransparencyLayerWithRect** 来制定区域来绘制 **Transparency Layer**

我们看下面这个例子代码

	UIGraphicsBeginImageContextWithOptions(CGSizeMake(320, 320),NO ,[UIScreen mainScreen].scale);
	CGContextRef myContext = UIGraphicsGetCurrentContext();
	CGFloat wd = 250;
	CGFloat ht = 250;
	CGSize myShadowOffset = CGSizeMake (5, -5);
		CGContextSetShadow (myContext, myShadowOffset, 3);
	CGContextBeginTransparencyLayer (myContext, NULL);
	CGContextSetRGBFillColor (myContext, 0, 1, 0, 1);	CGContextFillRect (myContext, CGRectMake (wd/3+ 20,ht/2 ,wd/4,ht/4));	CGContextSetRGBFillColor (myContext, 0, 0, 1, 1);
	CGContextFillRect (myContext, CGRectMake (wd/3-20,ht/2-50,wd/4,ht/4));	CGContextSetRGBFillColor (myContext, 1, 0, 0, 1);	CGContextFillRect (myContext, CGRectMake (wd/3,ht/2-20,wd/4,ht/4));	CGContextEndTransparencyLayer (myContext);

	UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
	self.imageView.image = image;	UIGraphicsEndImageContext();

![image](http://sipdar.github.io/image/2014-03-17/a.png)

三个矩形被当作一个整体添加了阴影，如果注释掉 

	CGContextBeginTransparencyLayer (myContext, NULL);
和

	CGContextEndTransparencyLayer (myContext);

那么阴影就会单独的添加在每个图形上。

![image](http://sipdar.github.io/image/2014-03-17/b.png)


