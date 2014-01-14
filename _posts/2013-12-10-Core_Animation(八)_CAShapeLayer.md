---
layout: post
title: Core Animation - CAShapeLayer
---

### CAShapeLayer
我们在上面曾经用过**CGPath**代替图片来绘制阴影，同样的我们可以使用**CGPath**来绘制各种我们想要的图形，用来代替图片使用。

**CAShapeLayer**是 **CALayer**的子类，在绘制的时候它使用的是矢量图形，而不是 **Bitmap Image**，因此效率非常高。在使用的时候 只要我们定义好颜色 线条的宽度，然后用**CGPath**来定义出形状，剩下的 **CAShapeLayer**会自动渲染。当然我们也可以使用 **Core Graphics**在**CALayer**中绘制我们想要的内容，但是有使用 **CAShapeLayer**有它的好处，

* 首先它速度非常快，**CAShaplayer**使用的是硬件加速，比起用 **Core Graphics**画一个图来会快很多
* 节省内存 它不象**CALayer**那样会创建背景图片，所以无论它有多大，都不会太消耗内存
* 不会剪切超出自己**bounds**的部分， 但是使用**CALayer**的时候我们就会被**CGPath**剪切掉超出的部分
* 没有像素画，在做缩放变幻的时候，不会出现像素化的情况。

我们看个小栗子 怎么使用 **CASapeLayer**

	UIBezierPath *path = [[UIBezierPath alloc] init]; [path moveToPoint:CGPointMake(175, 100)];
	[path addArcWithCenter:CGPointMake(150, 100) radius:25 startAngle:0 endAngle:2*M_PI clockwise:YES];	[path moveToPoint:CGPointMake(150, 125)];	[path addLineToPoint:CGPointMake(150, 175)]; 	[path addLineToPoint:CGPointMake(125, 225)];	[path moveToPoint:CGPointMake(150, 175)]; 
	[path addLineToPoint:CGPointMake(175, 225)];
	[path moveToPoint:CGPointMake(100, 150)];
	[path addLineToPoint:CGPointMake(200, 150)];	CAShapeLayer *shapeLayer = [CAShapeLayer layer]; 	shapeLayer.strokeColor = 	[UIColor redColor].CGColor; 	shapeLayer.fillColor = [UIColor clearColor].CGColor; 	shapeLayer.lineWidth = 5;	shapeLayer.lineJoin = kCALineJoinRound; 	shapeLayer.lineCap = kCALineCapRound; 		shapeLayer.path = path.CGPath;	[self.containerView.layer addSublayer:shapeLayer];


![image](http://sipdar.github.io/image/shaplayer1.png)**lineWidth** 就是线的宽度	
**lineCap** 线头的效果
**lineJoin** 不同的线相交的地方的效果
##圆角
我们曾经使用过 **CALayer** 的 **cornerRadius**属性来绘制圆角矩形，我们使用 **CAShaplayer**也可以实现这个效果，但是**CAShaplayer**更好的地方在于它可以定义每个角实现不同的效果。


	CGRect rect = CGRectMake(50, 50, 150, 150);
	UIView *testView = [[UIView alloc] initWithFrame:rect];
	testView.backgroundColor = [UIColor redColor];
	[self.view addSubview:testView];

	CGSize radii = CGSizeMake(20, 20);
	UIRectCorner corners = UIRectCornerTopRight |
	UIRectCornerBottomRight | UIRectCornerBottomLeft;
	//create path
	UIBezierPath *path = [UIBezierPath bezierPathWithRoundedRect:testView.bounds byRoundingCorners:corners cornerRadii:radii];
	CAShapeLayer *shapeLayer = [CAShapeLayer layer];
	shapeLayer.strokeColor = [UIColor greenColor].CGColor;
	shapeLayer.fillColor=[UIColor redColor].CGColor;
	shapeLayer.lineWidth = 1;
	shapeLayer.lineJoin = kCALineJoinRound;
	shapeLayer.lineCap = kCALineCapRound;
	shapeLayer.path = path.CGPath;
	testView.layer.mask =shapeLayer;
![image](http://sipdar.github.io/image/shaplayer2.png)
