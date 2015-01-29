---
layout: post
title: Bitmap Image
---
#Bitmap Image
位图图像 **Bitmap Image** 由像素组成，每一个像素都相当于图像中的一个点，JPEG,TIFF,PNG 类型的文件都是位图图像。在位图图像中每个像素都被分配一个特定位置和颜色值。在处理位图图像时，我们编辑的是像素而不是对象或形状，也就是说，编辑的是每一个点。位图图像被限制成正方形，但是我们通过填充透明像素来使图片呈现其他的形状包括旋转裁切等效果。

**Quartz** 支持很多流行的类型的图片格式，在iOS上包括 JPEG, GIF, PNG, TIF, ICO, GMP, XBM, and CUR。其他的图片类型 我们可以通过将格式信息传递给**Quartz**保证图片被正确的解析。

通常情况下我们在程序中大多使用 UIImagePNGRepresentation() 来将图片转换成二进制数据。但是这样的话实际上在转换的过程中被压缩了。那么我们可以通过 **Quartz** 来获得原始的图片数据。
首先我们要先把图片绘制到**context**上 然后使用 **CGBitmapContextGetData（）**获取二进制数据。	
{% highlight objc linenos %}
-(NSData *)bytesFromRGBImage:(UIImage *)sourceImage {
	if (!sourceImage) {
		return nil;
	}

	CGColorSpaceRef colorSpace = CGColorSpaceCreateDeviceRGB();//创建设备的colorSpace

	if (colorSpace == NULL) {
		return nil;
	}

	int width = sourceImage.size.width;//获取的像素点的个数
	int height = sourceImage.size.height;//获取的像素点的个数
	//8 是每一像素点的大小 8个bit(0-255)的空间
	// width * 4 每一行的像素点占用的字节数，每个像素点的ARGB四个通道各占8个bit(0-255)的空间

	CGContextRef context = CGBitmapContextCreate(NULL,width, height, 8, width *4, colorSpace, (CGBitmapInfo)kCGImageAlphaPremultipliedFirst);

	CGColorSpaceRelease(colorSpace);
	if (context == NULL) {
		return nil;
	}

	CGRect rect = (CGRect){.size = sourceImage.size};
	CGContextDrawImage(context, rect, sourceImage.CGImage);
	NSData *data = [NSData dataWithBytes:CGBitmapContextGetData(context) length:width*height*4];
	CGContextRelease(context);
	return data;
}

{% endhighlight %}

同样我们还可以通过二进制数据获得图片对象
{% highlight objc linenos %}
-(UIImage *)imageFromBytes:(NSData *)data size:(CGSize)targetSize {
	int width = targetSize.width;
	int height = targetSize.height;
	if (data.length < width *height *4) {
		return nil;
	}

	CGColorSpaceRef colorSpace = CGColorSpaceCreateDeviceRGB();
	if (colorSpace == NULL) {
		return nil;
	}
	Byte *bytes = (Byte *)data.bytes;
	CGContextRef context = CGBitmapContextCreate(bytes, width, height, 8, width*4, colorSpace, (CGBitmapInfo) kCGImageAlphaPremultipliedFirst);
	CGColorSpaceRelease(colorSpace);
	if (context == NULL) {
		return nil;
	}

	CGImageRef imageRef = CGBitmapContextCreateImage(context);
	UIImage *image = [UIImage imageWithCGImage:imageRef];
	CGContextRelease(context);
	return image;
}
{% endhighlight %}


#####Alpha通道
Alpha通道增加像素的透明度信息。

#####色彩深度
色彩深度又叫色彩位数，即位图中要用多少个二进制位来表示每个点的颜色，是分辨率的一个重要指标。常用有1位（单色），2位（4色，CGA），4位（16色，VGA），8位（256色），16位（增强色），24位和32位（真彩色）等。色深16位以上的位图还可以根据其中分别表示RGB三原色或CMYK四原色（有的还包括Alpha通道）的位数进一步分类.
#####图像遮罩(image mask)
**image mask** 也是由一組像素组成的特殊的位图，能指定图片的绘制区域，常见就通过**image mask**来绘制各种各样的形状，在iOS实际开发中用到的最多的可能就是圆角矩形了。 说白了就是就是我们通过**image mask**想让图片怎么显示，就怎么显示。



