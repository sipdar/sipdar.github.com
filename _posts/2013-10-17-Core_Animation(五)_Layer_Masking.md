---
layout: post
title: Core Animation (五)Layer Masking
---


#Layer Masking (五)
## Layer Masking 
当使用 **masksToBounds** 的时候，它会沿着layer的边框进行裁切超出的部分。包括使用 **cornerRadius**也是这样，裁出一个圆角矩形来。但是有时候我们想绘制的并不是一个矩形，或者一个圆角矩形，比如我现在想以五角星的形状来显示一个image对象，在比如我想在滚动一个显示文本的View的时候可以叫他优雅的在屏幕边缘淡出，而不是笔直的切割显示区域的边缘。
有两种方法可以实现，一种就是用 带 **alpha** 透明的**PNG**图片盖在要显示的区域上，当我们在程序中要动态的裁切图片显示区域的时候这个方法可能就不实用了。

**CAlayer** 有个**mask**属性， **mask**本身就是一个**CAlayer**，它和其他任何的**Layer**没什么不同。使用**mask layer**的时候，跟其他的**sublayer**没什么区别，不同的是它并像一个普通的**subLayer**那样显示在**superLayer**上 ，它决定了**superLayer**的可显示区域。
**mask Layer**的color 属性就可以忽略了。**mask**会把**superLayer**裁切,只显示符合 **Mask** 大小的部分.



	UIView *viewA = [[UIView alloc] initWithFrame:CGRectMake(100, 80, 100, 100)];
	UIView *viewB = [[UIView alloc] initWithFrame:CGRectMake(350, 80, 100, 100)];
	UIImage *image = [UIImage imageNamed:@"kangaroo.png"];
	UIImageView *imageView = [[UIImageView alloc] initWithImage:image];
	imageView.frame = CGRectMake(250, 100 , CGRectGetWidth(imageView.frame), 
											 CGRectGetHeight(imageView.frame));
	viewB.backgroundColor = [UIColor greenColor];
	viewA.backgroundColor = [UIColor greenColor];
	
	CALayer *layer = [CALayer layer];

	layer.frame = CGRectMake(10, 20, image.size.width, image.size.height);
	layer.contents = (id)image.CGImage;
	viewB.layer.mask = layer;

	[self.view addSubview:imageView];
	[self.view addSubview:viewA];
	[self.view addSubview:viewB];



![image](http://m3.img.libdd.com/farm5/2013/1018/23/0EEEEA8A23052F8A25B94C45104B54AD29117463794C2_880_532.PNG)
