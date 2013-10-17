---
layout: post
title: Core Animation (四) Visual Effects
---


#Visual Effects (四)

##圆角
**IOS** **UI** 的一个显著特点就是无处不在的圆角矩形，从主屏的图标，alert，文本输入框。我们也可以在程序中创建圆角矩形，而不需要Photoshop。

**CAlayer** 有个 **cornerRadius** 属性可以控制**Layer** 直角的弯曲度。它是一个默认值为零的浮点值。默认的情况下，圆角的效果只会影**Layer**的背景颜色，并不会影响你设置的背景图片，或者加在**Layer**上面的**SubLayer**，**masksToBounds**设置为 **YES** 可以实现裁切**SubLayer**的功能。代码如下

	UIView *greenView = [[UIView alloc] initWithFrame:CGRectMake(100,100 , 160, 160)];
	greenView.backgroundColor = [UIColor greenColor];
	UIView *redView = [[UIView alloc] initWithFrame:CGRectMake(-20,-20, 60, 60)];
	redView.backgroundColor = [UIColor redColor];
	[greenView addSubview:redView];
	greenView.layer.cornerRadius = 20;
	[self.view addSubview:greenView];
	
![image](http://m3.img.libdd.com/farm4/2013/1016/17/8273E20D2020401960A3FC8DF2CB97692811EACA9BF1C_594_856.PNG)

当设置了

		greenView.layer.masksToBounds = YES;

效果就是这样的，红色的**layer**的右上角也被剪切了。
![image](http://m1.img.libdd.com/farm4/2013/1016/17/7987618F46E5F587701ED6868A2995412FE51E3F677D6_594_856.PNG)

##Layer Borders

**CALayer**另外一个非常有用的属性就是 **borderWidth** 和 **borderColor** 这两个属性组合起来控制**Layer**的边框。 **borderColor** 的类型不是**UIColor** 是 **CGColorRef**。
边框是是绘制在**Layer**的**bounds**里面并且覆盖在其他所有的 **SubLayer** 的上面。我们还用这个**greenView** 来做实验。

			greenView.layer.borderWidth = 5.0f;
			
			
![image](http://m3.img.libdd.com/farm5/2013/1016/17/6F400D5569BE107A9658A55280CADA0362FE7053F3661_825_540.PNG)			
##Drop Shadows
对于IOS的设计来说，另外一个特征就是阴影。当我们给**Layer** 的 **shadowOpacity** 属性设置了一个大于零的值，阴影就会出现在 **Layer** 的后面。 **shadowOpacity** 是一个 大于0.0(透明) 小于1.0（全黑） 的浮点数。此外，我们还可以通过 **shadowColor**, **shadowOffset**, **shadowRadius**来调整 阴影的效果。

	UIView *viewA = [[UIView alloc] initWithFrame:CGRectMake(100, 80, 150, 150)];
	UIView *viewB = [[UIView alloc] initWithFrame:CGRectMake(300, 80, 150, 150)];
	viewA.backgroundColor = [UIColor greenColor];
	viewB.backgroundColor = [UIColor redColor];
	
	viewA.layer.shadowOpacity = 1.0;
	viewB.layer.shadowOpacity = 0.5;
	
	viewA.layer.shadowOffset = CGSizeMake(0, -15);
	viewB.layer.shadowOffset = CGSizeMake(5, 5);
	
	viewA.layer.shadowRadius = 30;
	viewB.layer.shadowRadius = 1;
	
	viewA.layer.shadowColor = [UIColor redColor].CGColor;
	viewB.layer.shadowColor = [UIColor greenColor].CGColor;

	
	[self.view addSubview:viewA];
	[self.view addSubview:viewB];

![image](http://m1.img.libdd.com/farm5/2013/1016/18/D6A310C8074AEF33D9D4628567E575D7949F2E3D8A83B_596_372.PNG)

##Shadow Clipping
和**Layer**的边框不一样，阴影会依照**Layer**包含的内容的确切的形状来显示。还有一个问题就是当使用 **masksToBounds** 的时候，阴影的显示也会有问题。
	
	UIView *redViewA = [[UIView alloc] initWithFrame:CGRectMake(-20,-20, 70, 70)];
	redViewA.backgroundColor = [UIColor redColor];
	UIView *redViewB = [[UIView alloc] initWithFrame:CGRectMake(-20,-20, 70, 70)];
	redViewB.backgroundColor = [UIColor redColor];
	
	
	UIView *viewA = [[UIView alloc] initWithFrame:CGRectMake(100, 80, 150, 150)];
	UIView *viewB = [[UIView alloc] initWithFrame:CGRectMake(300, 80, 150, 150)];
	viewB.layer.masksToBounds = YES;
	
	[viewA addSubview:redViewA];
	[viewB addSubview:redViewB];

	viewB.backgroundColor = [UIColor greenColor];
	viewA.backgroundColor = [UIColor greenColor];
	viewA.layer.shadowOffset = CGSizeMake(0, 10);
	viewB.layer.shadowOffset = CGSizeMake(0, 10);
	viewA.layer.shadowOpacity = 0.7;
	viewB.layer.shadowOpacity = 0.7;

![image](http://m1.img.libdd.com/farm4/2013/1016/18/5493C185AEEA6C73FE1DC43B1070CABE667EAC57F2857_682_456.PNG)

viewB因为使用了 **layer.masksToBounds = YES;** 导致阴影消失了。我们怎么解决这个问题呢？
给**viewB**一个同样大小的**superView** 叫这个**superView** 添加阴影。下面的代码是这个问题的解决方法。

	UIView *redViewA = [[UIView alloc] initWithFrame:CGRectMake(-20,-20, 70, 70)];
	UIView *redViewB = [[UIView alloc] initWithFrame:CGRectMake(-20,-20, 70, 70)];
	redViewA.backgroundColor = [UIColor redColor];
	redViewB.backgroundColor = [UIColor redColor];
	
	
	UIView *viewA = [[UIView alloc] initWithFrame:CGRectMake(100, 80, 150, 150)];
	UIView *viewB = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 150, 150)];
	viewB.layer.masksToBounds = YES;
	[viewA addSubview:redViewA];
	[viewB addSubview:redViewB];

	viewB.backgroundColor = [UIColor greenColor];
	viewA.backgroundColor = [UIColor greenColor];
	viewA.layer.shadowOffset = CGSizeMake(0, 10);
	viewB.layer.shadowOffset = CGSizeMake(0, 10);
	viewA.layer.shadowOpacity = 0.7;
	viewB.layer.shadowOpacity = 0.7;

	
	UIView *shadowViewB = [[UIView alloc] initWithFrame:CGRectMake(300, 80, 150, 150)];
	shadowViewB.layer.shadowOffset = CGSizeMake(0, 10);
	shadowViewB.layer.shadowOpacity = 0.7f;
	[shadowViewB addSubview:viewB];

	[self.view addSubview:viewA];
	[self.view addSubview:shadowViewB];
	
![image](http://m2.img.libdd.com/farm5/2013/1016/21/057ECD4FB33D83B407A9388A7DE0E858EA9D801553EBF_682_456.PNG)
这样一来 ViewB也有阴影啦。


##ShadowPath	
有时候我们给 **Layer** 添加的阴影并不总是正方形，如果我们想要其他形状的阴影怎么办呢。这时候 **shadowPath** 就派上用场了。阴影的实时计算是非常消耗手机性能的，尤其是当 **Layer** 包含大量的 带 **alpha** 遮罩 **sublayer** 的时候。
**ShadowPath** 是一个 **CGPathRef** （CGPath 的指针）。**CGPath** 是一个**Core Graphics** 的用来表示矢量形状的对象。我们可以用它来定义**shadow**的形状。

	UIView *viewA = [[UIView alloc] initWithFrame:CGRectMake(100, 80, 100, 100)];
	UIView *viewB = [[UIView alloc] initWithFrame:CGRectMake(300, 80, 100, 100)];

	viewB.backgroundColor = [UIColor greenColor];
	viewA.backgroundColor = [UIColor greenColor];
	viewA.layer.shadowOpacity = 0.5;
	viewB.layer.shadowOpacity = 0.5;


	CGMutablePathRef squarePath = CGPathCreateMutable();
	CGPathAddRect(squarePath, NULL, CGRectMake(-25, -25, 150, 150));
	viewA.layer.shadowPath = squarePath;
	CGPathRelease(squarePath);
	
	
	CGMutablePathRef circlePath = CGPathCreateMutable();
	CGPathAddEllipseInRect(circlePath, NULL,CGRectMake(-25, -25, 150, 150));
	viewB.layer.shadowPath = circlePath;
	CGPathRelease(circlePath);

	[self.view addSubview:viewA];
	[self.view addSubview:viewB];

![image](http://m1.img.libdd.com/farm5/2013/1016/22/AF4BC1A1DDB42D92DADD9D9F65D83633FD4F77B65038C_682_456.PNG)

当我们绘制一些简单的矩形或者圆形时可以直接使用**CGPath**。但是当需要一些复杂的形状的时候，比如圆角矩形 或者 五角星之类的 ，我们可以使用 **UIBezierPath** ，它是 **UIKit** 提供的 objc 封装的**CGPath** 要简单的多。 













			
