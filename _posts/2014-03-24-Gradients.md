---
layout: post
title: Gradients
---
**Quartz **提供了两种绘制渐变的数据类型，**CGShadingRef** 和**CGGradientRef** 我们都可以始终这两种方式来绘制线性渐变或者辐射渐变。

![image](http://sipdar.github.io/image/2014/03/24/gradient1.png)

#CGGradientRef

###Painting an axial gradient using a CGGradient object
{% highlight objc linenos %}

UIGraphicsBeginImageContextWithOptions(CGSizeMake(200, 200),NO ,[UIScreen mainScreen].scale);
CGContextRef context = UIGraphicsGetCurrentContext();
size_t locations = 2;
CGFloat location[2] ={0.0,1.0};
CGFloat components[8] = {1.0,0.5,0.4,1.0,
						0.8,0.8,0.3,1.0};
CGColorSpaceRef colorSpace = CGColorSpaceCreateDeviceRGB();
CGGradientRef mygradient  = CGGradientCreateWithColorComponents(colorSpace, components, location, locations);
CGContextDrawLinearGradient (context,mygradient,CGPointMake(0.0, 0.0),CGPointMake(200.0, 200.0), 0);
UIImage *image = UIGraphicsGetImageFromCurrentImageContext();UIGraphicsEndImageContext();

{% endhighlight %}	
![image](http://sipdar.github.io/image/2014/03/24/gradient2.png)

###Painting a radial gradient using a CGGradient object

	//CGContextDrawLinearGradient (context,mygradient,CGPointMake(0.0, 0.0),CGPointMake(200.0, 200.0), 0);
	CGContextDrawRadialGradient (context,mygradient,CGPointMake(10.0, 10.0),10.0f,CGPointMake(150.0, 150.0),50.0f,kCGGradientDrawsBeforeStartLocation & kCGGradientDrawsAfterEndLocation);

![image](http://sipdar.github.io/image/2014/03/24/gradient3.png)

	CGContextDrawRadialGradient (context,mygradient,CGPointMake(100.0, 100.0),0.0f,CGPointMake(100.0, 100.0),100.0f,kCGGradientDrawsBeforeStartLocation & kCGGradientDrawsAfterEndLocation);
![image](http://sipdar.github.io/image/2014/03/24/gradient5.png)

###多个颜色渐变
	
	size_t locations = 3;
	CGFloat location[3] ={0.0,0.5,1.0};
	CGFloat components[12] = {1.0,0.5,0.4,1.0,
								0.5,0.5,0.5,1.0,
								0.8,0.8,0.3,1.0};
								
![image](http://sipdar.github.io/image/2014/03/24/gradient4.png)													
### 用 **Gradient** 实现一个 iOS 6.0风格的高光按钮	
我们继承UIButton 然后在 **- (void)drawRect:**  中 添加渐变效果

	- (id)initWithFrame:(CGRect)frame
	{
		self = [super initWithFrame:frame];
		if (self) {
			// Initialization code

			self.layer.cornerRadius = 8.0f;
			self.layer.masksToBounds = YES;
			self.layer.borderWidth = 1.0f;
			self.layer.borderColor = [UIColor colorWithWhite:0.5f alpha:0.2f].CGColor;
		}
		return self;
	}

	// Only override drawRect: if you perform custom drawing.
	// An empty implementation adversely affects performance during animation.
	- (void)drawRect:(CGRect)rect {
		CGContextRef ref = UIGraphicsGetCurrentContext();
		CGFloat locations[5] = {0.0,0.5,0.5,0.8,1.0};
		CFArrayRef colors = (__bridge CFArrayRef) @[(id)[UIColor colorWithWhite:1.0f alpha:0.6f].CGColor,
								(id)[UIColor colorWithWhite:0.9f alpha:0.3f].CGColor,
								(id)[UIColor colorWithWhite:0.75f alpha:0.2f].CGColor,
								(id)[UIColor colorWithWhite:0.4f alpha:0.3f].CGColor,
								(id)[UIColor colorWithWhite:1.0f alpha:0.2f].CGColor];
		CGColorSpaceRef colorSpc = CGColorSpaceCreateDeviceRGB();
		CGGradientRef gradient = CGGradientCreateWithColors(colorSpc, colors, locations);
		CGContextDrawLinearGradient(ref, gradient, CGPointMake(0.5, 0.0), CGPointMake(0.5, rect.size.height), kCGGradientDrawsAfterEndLocation);
		CGColorSpaceRelease(colorSpc);
		CGGradientRelease(gradient);
	}

		
![image](http://sipdar.github.io/image/2014/03/24/gradient6.png)													

