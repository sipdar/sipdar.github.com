#Core Image
**Core Image**是一个很强大的图像处理框架，最早2004在**Mac**平台上发布，2011年随着**iOS5**iOS 平台也可以使用**Core Image**了，只是**iOS**上不如**Mac**下面的滤镜丰富。大概九十几种滤镜。
通过**Core Image**我们可以方便的修改图像的饱和度亮度和对比度之类的。他把大量的技术细节隐藏起来了，对于开发者来说，我们不用去了解高深的图像处理算法，还是非常易用的。

##处理图像
**Core image**有三个类来支持图像处理：

1. **CIFilter** 每个**CIFilter**都相当于一个滤镜。
2. **CIImage** 这个类存储图片数据，我们可以通过 **UIImage** 或者 图片文件，构造出来。
3. **CIContext** 图像处理都是在一个**CIContext**中完成的，通过它**Core Image**可以绘制一个**CIFilter**产生的结果。我们使用的时候可以指定**CIContext**基于CPU还是GPU。

下面是一个**Core Image**使用的简单例子
	
	self.logoImageView.image = [UIImage imageNamed:@"baby.jpg"];
	CIContext *context = [CIContext contextWithOptions:nil];
	CIImage *image = [CIImage imageWithCGImage:[UIImage imageNamed:@"baby.jpg"].CGImage];
	CIFilter *filter =[CIFilter filterWithName:@"CISepiaTone"];
	[filter setValue:image forKey:kCIInputImageKey];
	[filter setValue:@0.8 forKey:kCIInputIntensityKey];
	CIImage *result = [filter valueForKey:kCIOutputImageKey];
	CGRect extent = [result extent];	CGImageRef cgImage = [context createCGImage:result fromRect:extent];
	UIImage *resultImage = [UIImage imageWithCGImage:cgImage];
	self.progressView.image = resultImage;


![image](http://sipdar.github.io/image/2014/03/25/1.png)

##内置滤镜
**Core Image**提供了大量内置的滤镜，供你的应用做图像处理。不同iOS版本提供的滤镜是不同的，所以Core Image 提供了方法，让你可以查询系统提供了哪些滤镜。我们可以查找指定的分类，也可以通过**nil**残说获得所有的滤镜名称。

		NSArray *filterList = [CIFilter filterNamesInCategories:nil];

###滤镜的类别列表
#### 根据效果分类

Effect type  | Indicates  
------------ | -------------  
kCICategoryDistortionEffect | Distortion effects, such as bump, twirl, hole   
kCICategoryGeometryAdjustment | Geometry adjustment, such as affine transform, crop, perspective transform 
kCICategoryCompositeOperation | Compositing, such as source over, minimum, source atop, color dodge blend mode
kCICategoryHalftoneEffect | Halftone effects, such as screen, line screen, hatchedkCICategoryColorAdjustment | Color adjustment, such as gamma adjust, white point adjust, exposurekCICategoryColorEffect | Color effect, such as hue adjust, posterizekCICategoryTransition | Transitions between images, such as dissolve, disintegrate with mask, swipekCICategoryTileEffect | Tile effect, such as parallelogram, trianglekCICategoryGenerator | Image generator, such as stripes, constant color, checkerboardkCICategoryGradient | Gradient, such as axial, radial, GaussiankCICategoryStylize | Stylize, such as pixellate, crystallizekCICategorySharpen | Sharpen, luminancekCICategoryBlur | Blur, such as Gaussian, zoom, motion

####根据用法分类

USE  | Indicates  
------------ | -------------  
kCICategoryStillImage | Can be used for still imageskCICategoryVideo | Can be used for videokCICategoryInterlaced | Can be used for interlaced imageskCICategoryNonSquarePixels | Can be used for nonsquare pixelskCICategoryHighDynamicRange | Can be used for high-dynamic range pixels

###滤镜的属性
很多滤镜都有多个输入参数。并且每个输入的参数有可能都是不同的数据类型。那么我们可以通过下面这个方法获得指定滤镜的属性。
	
	CIFilter *myFilter = [CIFilter filterWithName:@"<# Filter Name Here #>"];	NSDictionary *myFilterAttributes = [myFilter attributes];
滤镜属性是以**key-Value**的形式保存的。**Core Image**使用**KVC**，你可以通过**KVC**获得或更改属性的值。

滤镜的属性类型。

Data Type  | Object  | Description  
------------ | -------------  | -------------  
Strings | NSString | Used for such things as display namesFloating-point values | NSNumber | Scalar values such as intensity levels and radiiVectors | CIVector | Specify positions, areas, and color values; can have 2, 3, or 4 elements, each of which is a floating-point numberColors | CIColor | Contain color values and a color space in which to interpret the valuesImages | CIImage | Lightweight objects that specify image “recipes”Transforms | NSData on iOS NSAffineTransformon OS X | An affine transformation to apply to an image

##创建一个Core Image Context
要渲染图像，我们需要创建一个**Core Image Context**,并且用这个**Context**来渲染输出图像。我们在创建**Core Image Context**可以通过初始化方法来指定 **Context**的渲染方式，**GPU** 还是**CPU**。

Context  | Renderer  | Supported platform  
------------ | -------------  | -------------  
contextWithOptions: | CPU or GPU | iOScontextWithCGContext: options: NSGraphicsContext | CPU or GPU | OS XcontextWithEAGLContext:<br> contextWithEAGLContext: options: | GPU | iOScontextWithCGLContext: pixelFormat:options: <br>contextWithCGLContext: pixelFormat: colorSpace:options: |GPU | OS X 

当我们的应用不需要实时渲染的时候我们用下面的方法创建 **Context**

	CIContext *context = [CIContext contextWithOptions:nil];
这个方法可以使用CPU或者GPU进行渲染。要定义使用哪个，需要设置一个**dictionary**，增加键kCIContextUserSoftwareRenderer，并设置相应的布尔值。CPU渲染要比GPU要慢。但是对于GPU渲染，在渲染结果被拷贝回CPU内存并转化为UIImage之前，不会被显示出来。

如果我们需要实时渲染，我们就要用到 GPU了。

	EAGLContext *myEAGLContext = [[EAGLContext alloc] initWithAPI:kEAGLRenderingAPIOpenGLES2];
	NSDictionary *options = @{ kCIContextWorkingColorSpace : [NSNull null] };	CIContext *myContext = [CIContext contextWithEAGLContext:myEAGLContext options:options];
##创建一个CIImage对象**Core Image filters** 处理**Core Image**images.**Core Image** 封装了image 对象，在渲染结果之前，**Core Image** 实际上不产生一个像素。下面是**Core Image**创建的方法：

Image source  | Methods  | Platform  
------------ | -------------  | -------------  
URL | imageWithContentsOfURL:<br>imageWithContentsOfURL: options: | iOS, OS XQuartz 2D image (CGImageRef) | imageWithCGImage:<br>imageWithCGImage: options: | iOS, OS XBitmap data | imageWithBitmapData: bytesPerRow:size:format:colorSpace: <br> imageWithImageProvider: size:format:colorSpace:options: | iOS, OS XEncoded data (an image in memory)|imageWithData: imageWithData: options: | iOS, OS XCIImageProvider | imageWithImageProvider: size:format:colorSpace:options: | iOS, OS XOpenGL texture |imageWithTexture: size:flipped: colorSpace:<br>imageWithTexture: size:flipped: options: | iOS, OS XCore Video pixel buffer |imageWithCVPixelBuffer:<br>imageWithCVPixelBuffer:options: | iOSCore Video image buffer |imageWithCVImageBuffer:<br>imageWithCVImageBuffer:options: | OS XIOSurface | imageWithIOSurface:<br>imageWithIOSurface: options: | OS X Quartz 2D layer (CGLayerRef) | imageWithCGLayer:<br>imageWithCGLayer: options: | OS XNSCIImageRep |initWithBitmapImageRep: | OS X
##Core Image 处理流程
![image](http://sipdar.github.io/image/2014/03/25/2.png)
当有多个滤镜的时候，**Core Image**会决定处理顺序，使效率达到最高。
## 渲染处理完成的图像
渲染使用 **GPU** 还是 **CPU** 取决于我们生成 **Context**的时候指定的是哪个。
Method   | Use
------------ | ------------- 
drawImage:inRect: fromRect: | Renders a region of an image to a rectangle in the context destination.<br>On iOS, this method renders only to a CIContext object that is created with contextWithEAGLContext:.<br>On OS X, the dimensions of the destination rectangle are in points if the CIContext object is created with a CGContextRef. The dimensions are in points if the CIContext object is created with a CGLContext object.createCGImage: fromRect:<br>createCGImage:fromRect:format:colorSpace: | Create a Quartz 2D image from a region of a CIImage object. (iOS and OS X)render:toBitmap:rowBytes:bounds:format:colorSpace: | Renders a CIImage object into a bitmap. (iOS and OS X)createCGLayerWithSize:info: | Creates a CGLayer object renders the CIImage object into the layer. (OS X only)render: toCVPixelBuffer:<br>render:toCVPixelBuffer:bounds:colorSpace: | Renders the CIImage object into a Core Video pixel buffer. (iOS only)render:toIOSurface:bounds:colorSpace: | Renders the CIImage object into an IOSurface object. (OS X only)

##多线程
**CIContext**和**CIImage**都是不可更改的。所以它们是线程安全的。可以在多个线程中使用同一个**Context**来渲染**CIImage**对象。**CIFilter**因为有写操作，所以它不是线程安全的，我们要给每个线程创建独立的**CIFilter**对象。
## 滤镜链
我们可以组合使用多个滤镜，来达到更好的滤镜效果。滤镜链的意思就是我们可以把前一个滤镜的输出结果，当作后面一个滤镜的输入参数。
我们看下面三个滤镜组合的效果

	self.logoImageView.image = [UIImage imageNamed:@"baby.jpg"];
	CIContext *context = [CIContext contextWithOptions:nil];
	CIImage *image = [CIImage imageWithCGImage:[UIImage imageNamed:@"baby.jpg"].CGImage];
	CIFilter *filter =[CIFilter filterWithName:@"CISepiaTone"];
	[filter setValue:image forKey:kCIInputImageKey];
	[filter setValue:@0.8 forKey:kCIInputIntensityKey];
	CIImage *result = [filter valueForKey:kCIOutputImageKey];
	CIFilter *gloom = [CIFilter filterWithName:@"CIGloom"];	[gloom setDefaults];	[gloom setValue: result forKey: kCIInputImageKey];	[gloom setValue: @25.0f forKey: kCIInputRadiusKey];	[gloom setValue: @0.75f forKey: kCIInputIntensityKey];	result = [gloom valueForKey: kCIOutputImageKey];

	CIFilter *bumpDistortion = [CIFilter filterWithName:@"CIBumpDistortion"];
	[bumpDistortion setDefaults];
	[bumpDistortion setValue: result forKey: kCIInputImageKey];	[bumpDistortion setValue: [CIVector vectorWithX:350 Y:250] forKey: kCIInputCenterKey];
	[bumpDistortion setValue: @150.0f forKey: kCIInputRadiusKey];
	[bumpDistortion setValue: @3.0f forKey: kCIInputScaleKey];
	result = [bumpDistortion valueForKey: kCIOutputImageKey];

	CGRect extent = [result extent];	CGImageRef cgImage = [context createCGImage:result fromRect:extent];
	UIImage *resultImage = [UIImage imageWithCGImage:cgImage];
	self.progressView.image = resultImage;
![image](http://sipdar.github.io/image/2014/03/25/3.png)

##Transition
使用**Core Image**实现图片之间的切换效果


	@interface TransitionView : GLKView
	@end

	@interface TransitionView ()<GLKViewDelegate>
	{
		NSTimeInterval  base;
		CGRect imageRect;
	}
	@property (nonatomic, strong) CIImage *image1;
	@property (nonatomic, strong) CIImage *image2;
	@property (nonatomic, strong) CIVector *extent;
	@property (nonatomic, strong) CIFilter *transition;
	@property (nonatomic, strong) CIContext *myContext;
	@end

	@implementation TransitionView
	- (void)awakeFromNib {
		self.transition = [CIFilter filterWithName: @"CIDissolveTransition"];
		UIImage *uiImage1    = [UIImage imageNamed:@"sample1.jpg"];
		UIImage *uiImage2    = [UIImage imageNamed:@"sample2.jpg"];
		self.image1    = [CIImage imageWithCGImage:uiImage1.CGImage];
		self.image2    = [CIImage imageWithCGImage:uiImage2.CGImage];
		imageRect = CGRectMake(0, 0, uiImage1.size.width, uiImage1.size.height);
		base = [NSDate timeIntervalSinceReferenceDate];
		[NSTimer scheduledTimerWithTimeInterval:1.0/30.0 target:self selector:@selector(onTimer:) userInfo:nil repeats:YES];
		self.delegate = self;
		self.context = [[EAGLContext alloc] initWithAPI:kEAGLRenderingAPIOpenGLES2];
		self.myContext = [CIContext contextWithEAGLContext:self.context];
	}

	- (CIImage *)imageForTransitionAtTime:(float)time{
		if (fmodf(time, 2.0) < 1.0f){
			[self.transition setValue:self.image1 forKey:@"inputImage"];
			[self.transition setValue:self.image2 forKey:@"inputTargetImage"];
		}else{
			[self.transition setValue:self.image2 forKey:@"inputImage"];
			[self.transition setValue:self.image1 forKey:@"inputTargetImage"];
		}
		CGFloat transitionTime = 0.5 * (1 - cos(fmodf(time, 1.0f) * M_PI));
		[self.transition setValue:@(transitionTime) forKey:@"inputTime"];

		CIImage *transitionImage = [self.transition valueForKey:@"outputImage"];
		return transitionImage;
	}

	- (void)glkView:(GLKView *)view drawInRect:(CGRect)rect {
		dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
		dispatch_async(queue, ^{
			float t = 0.4 * ([NSDate timeIntervalSinceReferenceDate] - base);
			CIImage *image = [self imageForTransitionAtTime:t];
			dispatch_async(dispatch_get_main_queue(), ^{
				[self.myContext drawImage:image inRect:imageRect fromRect:imageRect];
			});
		});
	}

	- (void)onTimer:(NSTimer *)timer {
		[self setNeedsDisplay];
	}

	@end
![image](http://sipdar.github.io/image/2014/03/25/4.gif)
