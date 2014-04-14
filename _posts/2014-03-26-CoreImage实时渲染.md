---
layout: post
title: Core Image 实时渲染
---
我们在使用 **AVFoundation** 显示视频的时候，也可以使用 Core Image 进行事实渲染。

	#import <UIKit/UIKit.h>
	#import <GLKit/GLKit.h>
	#import <AVFoundation/AVFoundation.h>
	#import <CoreImage/CoreImage.h>
	@interface ViewController : GLKViewController <AVCaptureVideoDataOutputSampleBufferDelegate> {
		AVCaptureSession *session;
		CIContext *coreImageContext;
	}
	@property (strong, nonatomic) EAGLContext *context;
	@end

	@implementation ViewController
	@synthesize context = _context;
	- (void)viewDidLoad{
		[super viewDidLoad];
		self.context = [[EAGLContext alloc] initWithAPI:kEAGLRenderingAPIOpenGLES2];
		GLKView *view = (GLKView *)self.view;
		view.frame = CGRectMake(0, 0, 640, 1136);
		view.context = self.context;
		view.drawableDepthFormat = GLKViewDrawableDepthFormat24;
		coreImageContext = [CIContext contextWithEAGLContext:self.context];

		NSError * error;
		session = [[AVCaptureSession alloc] init];
		[session beginConfiguration];
		[session setSessionPreset:AVCaptureSessionPreset640x480];
		AVCaptureDevice * videoDevice = [AVCaptureDevice defaultDeviceWithMediaType:AVMediaTypeVideo];
		AVCaptureDeviceInput *input = [AVCaptureDeviceInput deviceInputWithDevice:videoDevice error:&error];
		[session addInput:input];

		AVCaptureVideoDataOutput * dataOutput = [[AVCaptureVideoDataOutput alloc] init];
		[dataOutput setAlwaysDiscardsLateVideoFrames:YES]; 
		[dataOutput setVideoSettings:[NSDictionary dictionaryWithObject:[NSNumber numberWithInt:kCVPixelFormatType_32BGRA] forKey:(id)kCVPixelBufferPixelFormatTypeKey]]; 	
		[dataOutput setSampleBufferDelegate:self queue:dispatch_get_main_queue()];

		[session addOutput:dataOutput];
		[session commitConfiguration];
		NSArray *array = [[session.outputs objectAtIndex:0] connections];
		for (AVCaptureConnection *connection in array){
			connection.videoOrientation = AVCaptureVideoOrientationPortrait;
		}
		[session startRunning];
	}

	-(void)captureOutput:(AVCaptureOutput *)captureOutput didOutputSampleBuffer:(CMSampleBufferRef)sampleBuffer fromConnection:(AVCaptureConnection *)connection {
		CVPixelBufferRef pixelBuffer = (CVPixelBufferRef)CMSampleBufferGetImageBuffer(sampleBuffer);
		CIImage *image = [CIImage imageWithCVPixelBuffer:pixelBuffer];
		CGRect rect = [image extent];
		rect.origin.y = 200;
		rect.size.width  =  640;
		rect.size.height  =  (640.0/480.0)*640;
		CIFilter *filter =[CIFilter filterWithName:@"CISepiaTone"];
		[filter setValue:image forKey:kCIInputImageKey];
		[filter setValue:@0.8 forKey:kCIInputIntensityKey];
		image =	filter.outputImage;
		[coreImageContext drawImage:image inRect:rect fromRect:[image extent] ];
	}
	@end
