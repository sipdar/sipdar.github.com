---
layout: post
title: Core Animation (六)CGAffineTransform
---


#变换
图层一旦创建,你就可以通过矩阵变换来改变一个图层的几何形状。**CGAffineTransform** ,用于 图层的旋转,缩放,位移，还有 **CATransform3D** 它可以把二维的平面转换到三维矩阵中。
##Affine Transforms
**UIView**  有个属性是 **transform**,**transform** 的类型就是 **CGAffineTransform** 通过它我们可以对**UIView**做一些二维的旋转缩放之类的操作。**CGAffineTransform** 的定义如下

	struct CGAffineTransform {
		  CGFloat a, b, c, d;
		  CGFloat tx, ty;
	};

它其实表示的是一个矩阵   	
			
![image](https://developer.apple.com/library/ios/documentation/GraphicsImaging/Conceptual/drawingwithquartz2d/Art/equation01.gif)

因为最后一列总是是(0,0,1)，所以有用的信息就是前面两列，对一个view进行仿射变化就相当于对view上的每个点做一个乘法，结果就是

![image](https://developer.apple.com/library/ios/documentation/GraphicsImaging/Conceptual/drawingwithquartz2d/Art/equation02.gif)


下面举例说明几个转换运算的数学实现，x y 是原先点的坐标，下面是矩阵转换的计算公式

![image](https://developer.apple.com/library/ios/documentation/GraphicsImaging/Conceptual/drawingwithquartz2d/Art/equation03.gif
)

###恒等矩阵
恒等矩阵就是输入什么坐标，出来什么坐标，没有转换

![image](https://developer.apple.com/library/ios/documentation/GraphicsImaging/Conceptual/drawingwithquartz2d/Art/equation04.gif)

计算的结果如下，计算之前的x y 跟计算之后的 x‘ y’ 是相等的。

![image](https://developer.apple.com/library/ios/documentation/GraphicsImaging/Conceptual/drawingwithquartz2d/Art/equation05.gif)

###平移矩阵

![image](https://developer.apple.com/library/ios/documentation/GraphicsImaging/Conceptual/drawingwithquartz2d/Art/equation06.gif)

通过平移矩阵的计算公式如下

![image](https://developer.apple.com/library/ios/documentation/GraphicsImaging/Conceptual/drawingwithquartz2d/Art/equation07.gif)

###缩放矩阵

![image](https://developer.apple.com/library/ios/documentation/GraphicsImaging/Conceptual/drawingwithquartz2d/Art/equation08.gif)

缩放矩阵的计算公式

![image](https://developer.apple.com/library/ios/documentation/GraphicsImaging/Conceptual/drawingwithquartz2d/Art/equation09.gif)


###旋转矩阵

![image](https://developer.apple.com/library/ios/documentation/GraphicsImaging/Conceptual/drawingwithquartz2d/Art/equation10.gif)

旋转矩阵的计算公式

![image](https://developer.apple.com/library/ios/documentation/GraphicsImaging/Conceptual/drawingwithquartz2d/Art/equation11.gif)


###旋转加平移矩阵

![image](https://developer.apple.com/library/ios/documentation/GraphicsImaging/Conceptual/drawingwithquartz2d/Art/equation12.gif)


下面就是组合起来的计算公式

![image](https://developer.apple.com/library/ios/documentation/GraphicsImaging/Conceptual/drawingwithquartz2d/Art/equation13.gif)


AffineTransform类描述了一种二维仿射变换的功能，它是一种二维坐标到二维坐标之间的线性变换，保持二维图形的“平直性”（即变换后直线还是直线不会打弯，圆弧还是圆弧）和“平行性”（是指保二维图形间的相对位置关系不变，平行线还是平行线，而直线上点的位置顺序不变，另特别注意向量间夹角可能会发生变化。）仿射变换可以通过一系列的原子变换的复合来实现，包括：平移（Translation）、缩放（Scale）、翻转（Flip）、旋转（Rotation）和错切（Shear）


![image](http://m1.img.libdd.com/farm5/2013/1019/20/8B45FAAB1B7FF35E7BBDFB6BF60F0B1CCE48596C04FAE_482_342.PNG)

##创建一个 **CGAffineTransform**

看了上面的的矩阵和计算有点叫人挠头。 不过 **Core Graphics**为了方便开发者，提供了好多内建的函数，可以使我们不必学习复杂的数学知识，就可以实现上面的操作。
下面的方法都可以创建 **CGAffineTransform**

函数| 操作 
------------ | -------------
CGAffineTransformMakeTranslation | 新的平移矩阵要移动到距离远点的位置     
CGAffineTransformTranslate | 应用现有的仿射变换平移操作。   
CGAffineTransformMakeRotation | 构建一个新的旋转矩阵 参数是旋转的弧度。
CGAffineTransformRotate | 应用到现有的仿射变换的旋转操作。  
CGAffineTransformMakeScale | 构建一个新的缩放矩阵指定多少拉伸或收缩坐标x和y值。
CGAffineTransformScale | 应用现有的仿射变换的缩放操作。

我们看下面这个例子，它把LLVM的logo 旋转了 45°：

	UIImage *image = [UIImage imageNamed:@"DragonMedium"];
	UIImageView *imageView = [[UIImageView alloc] initWithImage:image];
	imageView.frame = CGRectMake(0, 0 , CGRectGetWidth(imageView.frame),
										 CGRectGetHeight(imageView.frame));
	imageView.center = self.view.center;
	imageView.transform = CGAffineTransformMakeRotation(M_PI_4);
	[self.view addSubview:imageView];
	
![image](http://m3.img.libdd.com/farm4/2013/1019/21/D78163CE65A5BDE31AFB5899ABBECB2C63DFCF8CA2CCB_594_856.PNG)
我们在旋转ImageView的时候 传给 **CGAffineTransformMakeRotation** 的参数是一个宏定义 **M_PI_4**,
并不是 45°。变换功能 在iOS中使用的是弧度并不是角度。 弧度通常指定使用的数学常数π（圆周率）的倍数。 π弧度等于180度，所以π除以4是相当于45度。
C 的**math**库，方便地提供常数π的公倍数，M_PI_4是常数，表示π除以4。如果你想使用弧度，你可以使用这些宏来转换角度。

	#define M_E         2.71828182845904523536028747135266250   /* e              */
	#define M_LOG2E     1.44269504088896340735992468100189214   /* log2(e)        */
	#define M_LOG10E    0.434294481903251827651128918916605082  /* log10(e)       */
	#define M_LN2       0.693147180559945309417232121458176568  /* loge(2)        */
	#define M_LN10      2.30258509299404568401799145468436421   /* loge(10)       */
	#define M_PI        3.14159265358979323846264338327950288   /* pi             */
	#define M_PI_2      1.57079632679489661923132169163975144   /* pi/2           */
	#define M_PI_4      0.785398163397448309615660845819875721  /* pi/4           */
	#define M_1_PI      0.318309886183790671537767526745028724  /* 1/pi           */
	#define M_2_PI      0.636619772367581343075535053490057448  /* 2/pi           */
	#define M_2_SQRTPI  1.12837916709551257389615890312154517   /* 2/sqrt(pi)     */
	#define M_SQRT2     1.41421356237309504880168872420969808   /* sqrt(2)        */
	#define M_SQRT1_2   0.707106781186547524400844362104849039  /* 1/sqrt(2)      */
	
##组合Transforms
