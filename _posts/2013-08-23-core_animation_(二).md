---
layout: post
title: Core Animation (二)  The Backing Image
---


#Core Animation (二) 

## The contents Image
CALayer 有个属性叫做 **contents** 是个**id**类型mac平台 core animation 的产物，用这个属性我们可以非常简单的使一个CALayer 显示一张image。
	
	CALayer *layer = [CALayer layer];
	layer.frame = CGRectMake(50, 50, 220, 220);
	[self.view.layer addSublayer:layer];

	UIImage *image = [UIImage imageNamed:@"testImage.png"];
	layer.contents = (__bridge id)image.CGImage;


![image](http://m2.img.libdd.com/farm4/2013/0823/15/0AB56D6360A02199E06D0ED0964B8A46628D602B64D5A_320_480.PNG)

##contentsGravity
额 宝马车被砸扁了。这有点不正常，怎么办呢，我的图片是个长方形，但是我的layer是个正方形。
当我们使用UIImageView显示图片的时候遇到这种情况下，我们可以使用
	
	view.contentMode = UIViewContentModeScaleAspectFit;

layer 也有类似的属性，它叫做 **contentsGravity** 跟UIView的contentMode enum 定义不一样，**contentsGravity**是**NSString**类型。

	 NSString * const kCAGravityCenter
	 NSString * const kCAGravityTop
	 NSString * const kCAGravityBottom
	 NSString * const kCAGravityLeft
	 NSString * const kCAGravityRight
	 NSString * const kCAGravityTopLeft
	 NSString * const kCAGravityTopRight
	 NSString * const kCAGravityBottomLeft
	 NSString * const kCAGravityBottomRight
	 NSString * const kCAGravityResize
	 NSString * const kCAGravityResizeAspect
	 NSString * const kCAGravityResizeAspectFill
	 
我们可以使用 **kCAGravityResizeAspect** 等同于
 **UIViewContentModeScaleAspectFit** 我们可以是图片适应 layer的大小 而不会生变形。汽车就不会被拍瘪了。
	
	layer.contentsGravity = kCAGravityResizeAspect;
![image](http://m2.img.libdd.com/farm4/2013/0823/15/E73BE61AD8A39DBCB263AD10238C1C8286303EC8A9942_320_480.PNG)

##contentsScale
**contentsScale**定义了两个像素点之间的距离的比例。是个float类型 默认值为1.0；
contentScale的用在我的汽车图片的时候并没有效果，因为 **contents**的图片已经被 **contentsGravity** 进行过缩放处理了，已适应，layer的bounds。

如果我们想简单的缩放layer 的 contents 图片，我们可以使用 **transform**或者 **affinetransform** .

contentsScale 到底是用来干嘛的呢？他其实是用来支持高分屏的。它用来查看当image绘制的时候应该绘制的大小。前提是图片没有被 **contentsGravity**压缩过，UIView 有个类似的很少用的属性 **contentScaleFactor**。

当**contentsScale**设为 1.0 时，在屏幕上绘制的每个点一个像素，2.0 就是每个点 2个像素。这就是Apple 对Retina的解决方案。

kCAGravityResizeAspect会压缩我们的图片。当我们换成 kCAGravityCenter 看看会发生什么

![image](http://m1.img.libdd.com/farm5/2013/0823/15/8A958479617C50A39DDC729ED3700C56E62F98F27FDC3_320_480.PNG)

额 只能看见车头的一点点，并且整张图片被像素化了。当我们给contents 设置CGImage 对象的时候，缩放机制就失去作用，我们现在可以使用 **contentsScale**来修正我们的image 显示了

	self.layerView.layer.contentsScale = image.scale;


![image](http://m2.img.libdd.com/farm4/2013/0823/15/351C15DD6BF6119F1BC1E73ABA073282EC32F96105F78_320_480.PNG)

当我们在程序中使用 layer显示图片的时候。我们要记着，设置layer的**contentsScale**符合屏幕的scale；否则我们的图片会像素画在高分屏上。

	layer.contentsScale = [UIScreen mainScreen].scale;

##masksToBounds		
我们的汽车图片现在显示的正确的大小了。但是我们不想显示超出layer 大小的部分。 UIView 有个叫属性叫做 clipsToBounds 用来决定是否显示超出自身大小的部分。CALayer 也有个类似的属性叫做 **masksToBounds** 当我们设置成 YES的时候，就是这样。

![image](http://m2.img.libdd.com/farm4/2013/0823/16/DE921EFD2ADAFB25D4181D37D078F48A007118E36E9E4_320_480.PNG)

##contentsRect
**contentsRect**是个CALayer的属性 可以指定layer显示image的某个区域。和 bounds frame 不一样的是 **contentsRect** 的度量单位并不是 points，而是坐标。坐标的范围是 （0 ~ 1.0）而且取得是相对值。

默认的**contentsRect**的值 是 {0,0,1,1}如果我们设置contentsRect为{0，0，0.5，0，5}
则layer显示的image如下
![image](http://m1.img.libdd.com/farm5/2013/0823/16/664DD0B4DA6B108AEC962505FDBA9FE49EEE6FBC2B9AE_513_338.PNG)


##contentsCenter
最后一个和内容相关的属性就是 **contentsCenter**。看名字大家觉得这个东西可能是个中心点之类的。但是实际上他是一个 **CGRect**  ，它指定图片的拉伸区域。默认情况下， contentsCenter 是{0，0，1，1}，当layer 改变大小的时候，image将会均匀的拉伸。当我们增加起始的位置，减小size的时候，我们就围着图片产生了一个边界。下面这个图显示了 **contentsCenter**的值为 {0.25，0.25，0.5，0.5}的时候缩放的行为 是什么样儿的。

![image](http://m2.img.libdd.com/farm4/2013/0823/16/2E140BC9F6DC79A0A3DFABEA83C40F9B3FF7A74C9602C_601_247.PNG)

这个跟UIImage的**resizableImageWithCapInsets**是一样的效果


##Custom Drawing
设置 layer 的contents 并不是唯一的显示Image 的方法， UIView的子类实现了  **-drawRect:** 方法 可以自定义UI。drawRect 方法会给View创建一个新的 背景image 对象， ，所以如果我们不需要这个image的话，就不要实现空的drawRect 方法。会浪费内存和CPU资源。

CAlayer 还有个 optional delegate 实现了 **CALayerDelegate**的协议。当CALayer 需要特殊信息的时候可以通过delegate 获得。

	- (void)displayLayer:(CALayer *)layer;
	- (void)drawLayer:(CALayer *)layer inContext:(CGContextRef)ctx;
	- (void)layoutSublayersOfLayer:(CALayer *)layer;
	- (id<CAAction>)actionForLayer:(CALayer *)layer forKey:(NSString *)event;

当layer 重绘之前会依次调用这几个方法。

	- (void)viewDidLoad
	{
	    [super viewDidLoad];
		CALayer *layer = [[CALayer alloc] init];
		layer.frame = CGRectMake(50, 50, 100, 100);
		layer.masksToBounds = YES;
		layer.delegate = self;
		layer.backgroundColor = [UIColor blueColor].CGColor;
		[self.view.layer addSublayer:layer];
		[layer display];
		
	}
	
	-(void)drawLayer:(CALayer *)layer inContext:(CGContextRef)ctx{
		CGContextSetLineWidth(ctx, 10.0f);
		CGContextSetStrokeColorWithColor(ctx, [UIColor redColor].CGColor);
		CGContextStrokeEllipseInRect(ctx, layer.bounds);
	}
	
![image](http://m3.img.libdd.com/farm5/2013/0823/17/66D57F267854F694235FDC96BD2884EA5F9F5573E317F_320_480.PNG)


我们主动的调用了 layer的 display方法，来强制它刷新，跟UIView不一样的是 CALayer 当它显示在屏幕上之后，它不会自动的重绘。需要程序员自己小心的决定他的重绘时机。

我们画的圆圈儿超出layer 大小的部分呗剪切掉了没有显示，尽管我们没有使用**masksToBounds**属性。这是因为我们绘制的时候调用的 **CALayerDelegate** CALayer 创建的绘制区域 conext的大小是由layer的大小决定的。所以没有可能在超出的区域绘制。

除非我们创建了单独的layer 否则尽量不要使用delegate,尽量不要实现delegate的协议方法，这是因为UIView 会创建一个他自己的背景层，自动的设置自己是layer的delegate，并且实现了 -displayLayer：方法。这就是问题的所在，当我们使用 View的背景层的时候，不需要实现 -displayLayer:方法 或者drawLayer:inContext:方法来绘制层的图片，我们只需要实现 drawRect：方法， UIView 会帮我们处理一切，包括layer需要重绘的时候 自动调用 display。





	

