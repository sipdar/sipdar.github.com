---
layout: post
title: Blend Modes
---
#Blend Modes
我们在使用**Quartz 2D**画图的时候，经常遇到图形叠加的情况。在多个图形重叠的时候有时候我们想重叠的部分透明阿，或者重叠的部分颜色混合在一起阿。这时候就要用到 **Quartz 2D** 的混合模式了
**Blend Modes**。通过 **Blend Modes** 我们可以把几个图片组合起来绘制到已经有图形的
 **graphic context**上。
##How to use Blend Modes 
1. 首先先画背景。
2. 使用 **CGContextSetBlendMode** 设置 **Blend Modes** 
3. 在画我们想和背景图形合成的图片 用 **CGContextDrawImage** 绘制。其实不只是图片。接下来绘制的无论是图片 矩形 文字都会使用  **Blend Modes** 和背景组合在一起。

下面我们把左边的图片跟右边的条纹背景用不同的 **Blend Modes** 混合在一起看看都有什么效果
![image](http://sipdar.github.io/image/2014-03-18/1.png)

###	Normal Blend Mode

	UIImage *foregroundImage = [UIImage imageNamed:@"abc.jpg"];
	UIGraphicsBeginImageContextWithOptions(CGSizeMake(320, 320),NO ,[UIScreen mainScreen].scale);
	CGContextRef context = UIGraphicsGetCurrentContext();
	CGFloat y = 0;
	CGFloat height = 50 ;
	for (int i = 0; i<5; i++) {
		y  += height ;
		CGRect rect = CGRectMake(0, y, 320, height);
		UIColor *color = [self colorWihtIndex:i];
		CGContextSetFillColorWithColor(context, color.CGColor);
		CGContextFillRect(context, rect);
		CGContextSaveGState(context);
	}

	CGContextScaleCTM(context, 1.0f, -1.0f);
	CGContextTranslateCTM(context, 0.0f, -320);

	CGContextSetBlendMode(context, kCGBlendModeNormal);
	CGContextDrawImage(context, CGRectMake(0, 50, 320, 240), foregroundImage.CGImage);
	UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
	self.imageView.image = image;	UIGraphicsEndImageContext();

	-(UIColor *)colorWihtIndex:(NSInteger) index{
		if (index == 0)
			return [[UIColor yellowColor] colorWithAlphaComponent:0.8];
		else if (index== 1)
			return [[UIColor purpleColor] colorWithAlphaComponent:0.8];
		else if (index== 2)
			return [[UIColor redColor]colorWithAlphaComponent:0.8];
		else if (index== 3)
			return [[UIColor greenColor] colorWithAlphaComponent:0.8];
		else if (index== 4)
			return [[UIColor blueColor] colorWithAlphaComponent:0.8];
		return [UIColor purpleColor];
	}
![image](http://sipdar.github.io/image/2014-03-18/normal.png)

### Multiply Blend Mode

	CGContextSetBlendMode(context, kCGBlendModeMultiply);
![image](http://sipdar.github.io/image/2014-03-18/multiply.png)

### Screen Blend Mode
	CGContextSetBlendMode(context, kCGBlendModeScreen);
	
![image](http://sipdar.github.io/image/2014-03-18/Screen.png)

## Overlay Blend Mode
	CGContextSetBlendMode(context, kCGBlendModeOverlay);
	
![image](http://sipdar.github.io/image/2014-03-18/Overlay.png)

##Lighten Blend Mode
	CGContextSetBlendMode(context, kCGBlendModeLighten);
	
![image](http://sipdar.github.io/image/2014-03-18/Lighten.png)

##Color Dodge Blend Mode
	CGContextSetBlendMode(context, kCGBlendModeColorDodge);
	
![image](http://sipdar.github.io/image/2014-03-18/Overlay.png)

##Color Burn Blend Mode
	CGContextSetBlendMode(context, kCGBlendModeColorBurn);
	
![image](http://sipdar.github.io/image/2014-03-18/ColorBurn.png)

##Soft Light Blend Mode
	CGContextSetBlendMode(context, kCGBlendModeSoftLight);
	
![image](http://sipdar.github.io/image/2014-03-18/SoftLight.png)

##Hard Light Blend Mode
	CGContextSetBlendMode(context, kCGBlendModeHardLight);
	
![image](http://sipdar.github.io/image/2014-03-18/HardLight.png)

## Difference Blend Mode
	CGContextSetBlendMode(context, kCGBlendModeDifference);
	
![image](http://sipdar.github.io/image/2014-03-18/Difference.png)
## Exclusion Blend Mode
	CGContextSetBlendMode(context, kCGBlendModeExclusion);
	
![image](http://sipdar.github.io/image/2014-03-18/Exclusion.png)

## Hue Blend Mode
	CGContextSetBlendMode(context, kCGBlendModeHue);
	
![image](http://sipdar.github.io/image/2014-03-18/Hue.png)

## Saturation Blend Mode
	CGContextSetBlendMode(context, kCGBlendModeSaturation);
	
![image](http://sipdar.github.io/image/2014-03-18/Saturation.png)

## Color Blend Mode
	CGContextSetBlendMode(context, kCGBlendModeColor);
	
![image](http://sipdar.github.io/image/2014-03-18/Color.png)

##Luminosity Blend Mode

	CGContextSetBlendMode(context, kCGBlendModeLuminosity);
	
![image](http://sipdar.github.io/image/2014-03-18/Luminosity.png)

