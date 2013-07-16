---
layout: post
title: What's new in cocoatouch
---


##Multitasking

###background fetching 

####New delegate method on UIApplication is called
   	- (void)application:(UIApplication *)application     performFetchWithCompletionHandler:(void (^)(UIBackgroundFetchResult     result))completionHandler; 
 Call the completion handler when fetch is complete

####Tuning the fetch interval
 	- (void)setMinimumBackgroundFetchInterval:(NSTimeInterval)minInterval;
####Constants
	 const NSTimeInterval UIApplicationBackgroundFetchIntervalMinimum     const NSTimeInterval UIApplicationBackgroundFetchIntervalNever
     
###Remote notifications
####New delegate method on UIApplication is called     - (void)application:(UIApplication *)application     didReceiveRemoteNotification:(NSDictionary *)userInfo           fetchCompletionHandler:(void (^)(UIBackgroundFetchResult                                                result))completionHandler;Call the completion handler when fetch is complete
###Fetch results
You call the completion handler
     void (^)(UIBackgroundFetchResult result))completionHandler;    
     typedef NS_ENUM(NSUInteger, UIBackgroundFetchResult) {         UIBackgroundFetchResultNewData,         UIBackgroundFetchResultNoData,         UIBackgroundFetchResultFailed		}
		
###Background transfers
####NSURLSession* Replacement API for NSURLConnection * Data, upload, download tasks* Sessions have identifiers####New delegate method on UIApplication is called        - (void)application:(UIApplication *)application     	 handleEventsForBackgroundURLSession:(NSString *)identifier	     completionHandler:(void (^)())completionHandler; 
Call the completion handler when you’re done handling callbacks
##Images and Views
###Image Rendering Modes
####Creating an image with a rendering mode	- (UIImage *)imageWithRenderingMode:(UIImageRenderingMode)renderingMode; Pass the mode
     typedef NS_ENUM(NSInteger, UIImageRenderingMode) {         UIImageRenderingModeAutomatic,         UIImageRenderingModeAlwaysOriginal,         UIImageRenderingModeAlwaysTemplate	}
### Tint
####New UIView property
	@property (nonatomic, retain)UIColor *tintColor;

####Dimming adjustment behavior     @property (nonatomic) UIViewTintAdjustmentMode tintAdjustmentMode;
#### Finding out about changes
	-(void) tintColorDidChange;
###View Animation
#### No animations
	+(void)perforWithoutAnimation:(void(^)(void))actionsWithOutAnimation;
#### Keyframes

	+ (void)animateKeyframesWithDuration:(NSTimeInterval)duration                               delay:(NSTimeInterval)delay                             options:(UIViewKeyframeAnimationOptions)options                          animations:(void (^)(void))animations                          completion:(void (^)(BOOL finished))completion;
                          	+ (void)addKeyframeWithRelativeStartTime:(double)frameStartTime                        relativeDuration:(double)frameDuration                              animations:(void (^)(void))animations;
#### Keyframe animation options	 typedef NS_OPTIONS(NSUInteger, UIViewKeyframeAnimationOptions) {	     UIViewKeyframeAnimationOptionLayoutSubviews = UIViewAnimationOptionLayoutSubviews,	     UIViewKeyframeAnimationOptionAllowUserInteraction = UIViewAnimationOptionAllowUserInteraction,	     UIViewKeyframeAnimationOptionBeginFromCurrentState = UIViewAnimationOptionBeginFromCurrentState,	     UIViewKeyframeAnimationOptionRepeat = UIViewAnimationOptionRepeat,	     UIViewKeyframeAnimationOptionAutoreverse = UIViewAnimationOptionAutoreverse,	     UIViewKeyframeAnimationOptionOverrideInheritedDuration =  UIViewAnimationOptionOverrideInheritedDuration,	     UIViewKeyframeAnimationOptionCalculationModeLinear = 0 << 9,	     UIViewKeyframeAnimationOptionCalculationModeDiscrete = 1 << 9,	     UIViewKeyframeAnimationOptionCalculationModePaced = 2 << 9,	     UIViewKeyframeAnimationOptionCalculationModeCubic = 3 << 9,	     UIViewKeyframeAnimationOptionCalculationModeCubicPaced = 4 << 9	}#### Motion Effects
 * Applies relative values to keypaths of a target view * Affected by device "pose" or position * Affects animatable properties only
#### UIInterpolatingMotionEffect
Initialization
     - (instancetype)initWithKeyPath:(NSString *)keyPath                                type:(UIInterpolatingMotionEffectType)type;Types
     typedef NS_ENUM(NSInteger, UIInterpolatingMotionEffectType) {         UIInterpolatingMotionEffectTypeTiltAlongHorizontalAxis,         UIInterpolatingMotionEffectTypeTiltAlongVerticalAxis	};
Managing values
	 @property (retain, nonatomic) id minimumRelativeValue;     @property (retain, nonatomic) id maximumRelativeValue;

Abstract superclass

	 - (NSDictionary *)keyPathsAndRelativeValuesForViewerOffset:                                                      (UIOffset)viewerOffset;	typedef struct UIOffset {         CGFloat horizontal, vertical;	} UIOffset;
	
Adding and removing     - (void)addMotionEffect:(UIMotionEffect *)effect;     - (void)removeMotionEffect:(UIMotionEffect *)effect;What’s already there?
     @property (copy, nonatomic) NSArray *motionEffects;##Collection Views
### Transitions  between layouts
	- (void)setCollectionViewLayout:(UICollectionViewLayout *)layout	                       animated:(BOOL)animated	                     completion:(void (^)(BOOL finished))completion;### UIConllectionViewTransitionLayout
#### Initializing
     - (id)initWithCurrentLayout:(UICollectionViewLayout *)currentLayout                      nextLayout:(UICollectionViewLayout *)newLayout;#### Managing values
     - (void)updateValue:(CGFloat)value forAnimatedKey:(NSString *)key;     - (CGFloat)valueForAnimatedKey:(NSString *)key;#### Progress
     @property (assign, nonatomic) CGFloat transitionProgress;
## View Controllers 

###Layout 
####wantsFullScreenLayout   deprecated in iOS 7

####Extended edges
	typedef NS_OPTIONS(NSUInteger, UIExtendedEdge) {     UIExtendedEdgeNone = 0,     UIExtendedEdgeTop  = 1 << 0,     UIExtendedEdgeLeft = 1 << 1,     UIExtendedEdgeBottom = 1 << 2,     UIExtendedEdgeRight  = 1 << 3,     UIExtendedEdgeAll  = UIExtendedEdgeTop |                          UIExtendedEdgeLeft |                          UIExtendedEdgeBottom |                          UIExtendedEdgeRight	};
		 @property(nonatomic,assign) UIExtendedEdge edgesForExtendedLayout;	 @property(nonatomic,assign) BOOL extendedLayoutIncludesOpaqueBars;	 @property(nonatomic,assign) BOOL automaticallyAdjustsScrollViewInsets;
	#### Content size
	   @property (nonatomic ) CGSize peferredContentSize;
#### Status bar apearance
new behavior for the status bar

new status bar style 

	 typedef NS_ENUM(NSInteger, UIStatusBarStyle) {         UIStatusBarStyleDefault         UIStatusBarStyleLightContent     NS_ENUM_AVAILABLE_IOS(7_0),         UIStatusBarStyleBlackTranslucent,         UIStatusBarStyleBlackOpaque	 }
	 
### Custom Transitions
####     UIViewControllerTransitioningDelegate

	 @property (nonatomic,retain) id <UIViewControllerTransitioningDelegate>     transitioningDelegate;
     
     @protocol UIViewControllerTransitioningDelegate <NSObject>	 @optional	 - (id <UIViewControllerAnimatedTransitioning>)	     animationControllerForPresentedController:(UIViewController *)presented	                          presentingController:(UIViewController *)presenting	                              sourceController:(UIViewController *)source;	 - (id <UIViewControllerAnimatedTransitioning>)	                animationControllerForDismissedController:(UIViewController *)dismissed;	 - (id <UIViewControllerInteractiveTransitioning>)interactionControllerForPresentation:	                                   (id <UIViewControllerAnimatedTransitioning>)animator;	 - (id <UIViewControllerInteractiveTransitioning>)interactionControllerForDismissal:	                                   (id <UIViewControllerAnimatedTransitioning>)animator;	@end

####UIViewControllerAnimatedTransitioning	 @protocol UIViewControllerAnimatedTransitioning <NSObject>	 - (NSTimeInterval)transitionDuration:	               (id <UIViewControllerContextTransitioning>)transitionContext;	 - (void)animateTransition:	               (id <UIViewControllerContextTransitioning>)transitionContext;	 @optional	 - (void)animationEnded:(BOOL) transitionCompleted;	@end


####UIViewControllerInteractiveTransitioning	 @protocol UIViewControllerInteractiveTransitioning <NSObject>	 - (void)startInteractiveTransition:	               (id <UIViewControllerContextTransitioning>)transitionContext;	 @optional	 - (CGFloat)completionSpeed;	 - (UIViewAnimationCurve)completionCurve;	@end####UIViewControllerContextTransitioning	 @protocol UIViewControllerContextTransitioning <NSObject>	 - (UIView *)containerView;	 - (BOOL)isAnimated;	 - (BOOL)isInteractive;	 - (BOOL)transitionWasCancelled;	 - (UIModalPresentationStyle)presentationStyle;	 - (void)updateInteractiveTransition:(CGFloat)percentComplete;	 - (void)finishInteractiveTransition;	 - (void)cancelInteractiveTransition;	 - (void)completeTransition:(BOOL)didComplete;	 - (UIViewController *)viewControllerForKey:(NSString *)key;	 - (CGRect)initialFrameForViewController:(UIViewController *)vc;	 - (CGRect)finalFrameForViewController:(UIViewController *)vc;	 @end##State Restoration
###Ignoring Snapshots
Called from methods invoked by state restoration:
     - (void)ignoreSnapshotOnNextApplicationLaunch;###Other ObjectsNon-view and non-view controller objects can now participate     + (void)registerObjectForStateRestoration:(id<UIStateRestoring>)object             restorationIdentifier:(NSString *)restorationIdentifier;
###Bluetooth State Restoration

####Launch options keys     
     UIKIT_EXTERN NSString *const     UIApplicationLaunchOptionsBluetoothCentralsKey;     UIKIT_EXTERN NSString *const     UIApplicationLaunchOptionsBluetoothPeripheralsKey;             ##AirDrop
 * Adopt UIActivityItemSourceProtocol* Update application’s Info.plist to create, register, and export UTI for custom document formats* New Documents/Inbox directory 
	 * Check this on app activations	 * ...even when you didn’t get an 

			 - application:openURL:sourceApplication:annotation: call
		 * May be launched multiple times in quick succession 
	* Might want to queue work up##Dynamics
###UIDynamicAnimator	 @interface UIDynamicAnimator: NSObject	 - (instancetype)initWithReferenceView:(UIView*)view;	 - (void)addBehavior:(UIDynamicBehavior *)behavior;	 - (void)removeBehavior:(UIDynamicBehavior *)behavior;	 - (void)removeAllBehaviors;
	 	 @property (nonatomic, readonly) UIView* referenceView;	 @property (nonatomic, readonly, copy) NSArray* behaviors;	 
	 - (NSArray*)itemsInRect:(CGRect)rect;	 @property (nonatomic, readonly, getter = isRunning) BOOL running;	 - (NSTimeInterval)elapsedTime;	 
	 @property (nonatomic, assign) id <UIDynamicAnimatorDelegate> delegate;	 @end
	 
	 
###UIDynamicBehavior 
	 @interface UIDynamicBehavior : NSObject	 - (void)addChildBehavior:(UIDynamicBehavior *)behavior;	 - (void)removeChildBehavior:(UIDynamicBehavior *)behavior;	 @property (nonatomic, readonly, copy) NSArray* childBehaviors;	 @property (nonatomic,copy) void (^action)(void);	 @end	 
###Supported behaviors* UIAttachmentBehavior * UICollisionBehavior* UIGravityBehavior* UIPushBehavior* UISnapBehavior* UIDynamicItemBehavior##Text
###dynamic text size
	 @property(nonatomic,readonly) NSString *preferredContentSizeCategory;
	
		 NSString *const UIContentSizeCategoryExtraSmall;		 NSString *const UIContentSizeCategorySmall;		 NSString *const UIContentSizeCategoryMedium;		 NSString *const UIContentSizeCategoryLarge;		 NSString *const UIContentSizeCategoryExtraLarge;		 NSString *const UIContentSizeCategoryExtraExtraLarge;		 NSString *const UIContentSizeCategoryExtraExtraExtraLarge;	 
	 NSString *const UIContentSizeCategoryDidChangeNotification;	 NSString *const UIContentSizeCategoryNewValueKey;
	 
###UIFontFont scaling based on content size category     + (UIFont *)preferredFontForTextStyle:(NSString *)style;     NSString *const UIFontTextStyleHeadline1;     NSString *const UIFontTextStyleHeadline2;     NSString *const UIFontTextStyleBody;     NSString *const UIFontTextStyleSubheadline1;     NSString *const UIFontTextStyleSubheadline2;     NSString *const UIFontTextStyleFootnote;     NSString *const UIFontTextStyleCaption1;     NSString *const UIFontTextStyleCaption2;
##Text Kit     * Objective-C API* Inspired by the Cocoa text system from OS X * Wraps Core Text
###UITextView and NSTextContainer	 - (instancetype)initWithFrame:(CGRect)frame	                 textContainer:(NSTextContainer *)textContainer;	 @property (nonatomic,readonly) NSTextContainer *textContainer;	 @property(nonatomic,readonly) NSLayoutManager *layoutManager;	 @property(nonatomic,readonly,retain) NSTextStorage *textStorage;
	 
	 @interface NSTextContainer : NSObject <NSCoding,		 NSTextLayoutOrientationProvider>		 - (id)initWithSize:(CGSize)size;		 @property(assign, NS_NONATOMIC_IOSONLY) NSLayoutManager *layoutManager;		 @property(NS_NONATOMIC_IOSONLY) CGSize size;		 @property(copy, NS_NONATOMIC_IOSONLY) NSArray *exclusionPaths;		 @property(NS_NONATOMIC_IOSONLY) NSLineBreakMode lineBreakMode;		 @property(NS_NONATOMIC_IOSONLY) CGFloat lineFragmentPadding;		 - (CGRect)lineFragmentRectForProposedRect:(CGRect)proposedRect		           atIndex:(NSUInteger)characterIndex     @end
     
###NSLayoutManager global options	 @interface NSLayoutManager : NSObject <NSCoding>	 ...		 @property(NS_NONATOMIC_IOSONLY) BOOL showsInvisibleCharacters;		 @property(NS_NONATOMIC_IOSONLY) BOOL showsControlCharacters;		 @property(NS_NONATOMIC_IOSONLY) CGFloat hyphenationFactor;		 @property(NS_NONATOMIC_IOSONLY) BOOL usesFontLeading;		 @property(NS_NONATOMIC_IOSONLY) BOOL allowsNonContiguousLayout;		 @property(readonly, NS_NONATOMIC_IOSONLY) BOOL hasNonContiguousLayout;	 ...	 @end
### NSLayoutManager features• Invalidation     - (void)invalidateLayoutForCharacterRange:(NSRange)charRange             actualCharacterRange:(NSRangePointer)actualCharRange;• Glyphs and glyph properties     @property(readonly, NS_NONATOMIC_IOSONLY) NSUInteger numberOfGlyphs;     - (CGGlyph)glyphAtIndex:(NSUInteger)glyphIndex                isValidIndex:(BOOL *)isValidIndex;     - (NSUInteger)getGlyphsInRange:(NSRange)glyphRange                             glyphs:(CGGlyph *)glyphBuffer                         properties:(NSGlyphProperty *)props                   characterIndexes:(NSUInteger *)charIndexBuffer                         bidiLevels:(unsigned char *)bidiLevelBuffer;