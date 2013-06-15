---
layout: post
title: What's new in iOS 7.0
---

#What's new in iOS 7.0
iOS 7.0应该是这五年来最大的更新。包括重新设计的UI，全新的创建2d 2.5d游戏的动画系统，多任务增强，p2p联接等等。

##UIKit Dynamics
 通过实现 [UIDynamicItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIDynamicItem_Protocol/Reference/Reference.html#//apple_ref/occ/intf/UIDynamicItem)   protocol 可以是UIView 实现一些动态的效果。
 
* **UIAttachmentBehavior**  两个对象之间的动态效果
* **UICollisionBehavior**  碰撞效果
* **UIGravityBehavior**  重力矢量的动态效果
* **UIPushBehavior** 指定其动态项目连续或瞬时力矢量
* **UISnapBehavior** 指定某个对某个点的连接效果。

这些动态效果必须添加到 [UIDynamicAnimator](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIDynamicAnimator_Class/Reference/Reference.html#//apple_ref/doc/uid/TP40013153) 的对象上。animator 提供了执行这些动态效果时的关系。每个动态的对象可以有过个效果，所有的这些效果必须在同一个animator对象下执行动画效果。

##Text Kit
是一个全功能的，高层次的框架,包含了需要处理文字精排版的应用所需要的所有功能。可以实现在文档中通过拖动动态的排版，比起以前的UIKit中基于文本的控件实现相同的功能需要更少的代码。
Text Kit 包含新的类 以及对已有类进行了扩展。

 * [NSAttributedString](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/NSAttributedString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/cl/NSAttributedString) 支持新的属性的扩展.
 * [NSLayoutManager](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/NSLayoutManager_Class_TextKit/Reference/Reference.html#//apple_ref/occ/cl/NSLayoutManager) 管理字体和排版
 * [NSTextContainer](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/NSTextContainer_Class_TextKit/Reference/Reference.html#//apple_ref/occ/cl/NSTextContainer) 定义文字排版的区域
 * [NSTextStorage](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/NSTextStorage_Class_TextKit/Reference/Reference.html#//apple_ref/occ/cl/NSTextStorage) 定义管理文字内用的基本接口
 更多关于Text Kit的信息 参看[Text Programming Guide for iOS.](https://developer.apple.com/library/prerelease/ios/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009542)
 
##Multitasking Enhancements
iOS 7添加了新的后台执行的模式

* 对于经常需要更新内容的应用现在可以通过注册到系统实现定期唤醒，然后在后台下载。要在app 的 **Info.plist** 中注册**UIBackgroundModes** key 和**fetch**的值。并且要通过** setMinimumBackgroundFetchInterval: **设置下载操作的最小间隔时间。然后在app delegate中实现** application:performFetchWithCompletionHandler:** 来获取下载的数据。

* 现在应用可以使用 **APNS** 通知用户有新的内容了，并且可以使用这个通知来开启后台下载的操作。支持这种模式 需要在 **Info.Plist** 中添加**UIBackgroundModes**的键 和 **remote-notification**的值 然后在app delegate中实现** application:performFetchWithCompletionHandler:** 来获取下载的数据。
这两种模式都可以在合适的时间把应用从挂起状态启动起来进入后台下载的状态。在**fetch**的模式下，系统使用现有的信息来决定唤醒app的最佳时间。例如，当网络状况非常好或者设备在使用中。**remote-notification**的模式下，只有当新的消息到达的时候才会唤醒。
在后台获取下载的内用 要使用**NSURLSession**类 ，**NSURLSession** 比 **NSURLConnection**在使用上更简单些，下载任务还是基于** NSURLRequest**

更多的新的后台下载的特性参见[App States and Multitasking](https://developer.apple.com/library/prerelease/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/ManagingYourApplicationsFlow/ManagingYourApplicationsFlow.html#//apple_ref/doc/uid/TP40007072-CH4) [in iOS App Programming Guide](https://developer.apple.com/library/prerelease/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40007072)
##Maps
**Map Kit**对于使用基本地图信息的应用来说有很多改进跟新特性。应用使用地图展示本地信息时可以充分的利用3D地图，包括在程序中动态的控制地图的视角。
 
 * Overlays现在可以在同一地图上设置不同的级别， 可以展示多层相关的数据。
 * 可以在题图上的指定某个位置使用[**MKMapCamera **](https://developer.apple.com/library/prerelease/ios/documentation/MapKit/Reference/MKMapCamera_class/Reference/Reference.html#//apple_ref/occ/cl/MKMapCamera) 来增强3D地图的显示。
 * [**MKDirections**](https://developer.apple.com/library/prerelease/ios/documentation/MapKit/Reference/MKDirections_class/Reference/Reference.html#//apple_ref/occ/cl/MKDirections) 可以从苹果获得基于方向的导航信息。 
 * [**MKGeodesicPolyline**](https://developer.apple.com/library/prerelease/ios/documentation/MapKit/Reference/MKGeodesicPolyline_class/Reference/Reference.html#//apple_ref/occ/cl/MKGeodesicPolyline) 可以创建一个沿着地面弧度的基于线的层。
 * [**MKMapSnapshotter**](https://developer.apple.com/library/prerelease/ios/documentation/MapKit/Reference/MKMapSnapshotter_class/Reference/Reference.html#//apple_ref/occ/cl/MKMapSnapshotter) 可以对地图截图。
 * 可以使用[**MKTileOverlay**](https://developer.apple.com/library/prerelease/ios/documentation/MapKit/Reference/MKTileOverlay_class/Reference/Reference.html#//apple_ref/occ/cl/MKTileOverlay) 和 [**MKTileOverlayRenderer**](https://developer.apple.com/library/prerelease/ios/documentation/MapKit/Reference/MKTileOverlayRenderer_class/Reference/Reference.html#//apple_ref/occ/cl/MKTileOverlayRenderer)来替换地图加载中的默认瓷砖效果。
 * 加在地图上面的层的现在都基于[**MKOverlayRenderer**](https://developer.apple.com/library/prerelease/ios/documentation/MapKit/Reference/MKOverlayRenderer_class/Reference/Reference.html#//apple_ref/occ/cl/MKOverlayRenderer) 它代替了view 提供了更好的更简单的实现方式
 
 更多关于地图的内容参见[Map Kit Framework Reference](https://developer.apple.com/library/prerelease/ios/documentation/MapKit/Reference/MapKit_Framework_Reference/_index.html#//apple_ref/doc/uid/TP40008210)

## AirDrop
AirDrop使用户可以分享 照片 文档 链接 和其他的数据类型。[UIActivityViewController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIActivityViewController_Class/Reference/Reference.html#//apple_ref/occ/cl/UIActivityViewController)现在默认实现了AirDrop。

## Inter-App Audio
**AudioUnit.framework** 支持**Inter-App Audio**该API将允许app之间相互分享音频流，也让苹果iOS设备更容易地创建音乐。

更多的信息参考 [Audio Toolbox Framework Reference](https://developer.apple.com/library/prerelease/ios/documentation/MusicAudio/Reference/CAAudioTooboxRef/_index.html#//apple_ref/doc/uid/TP40002089)

## Peer-to-Peer Connectivity
**MultipeerConnectivity.framework** 支持发现附近的设备并且可以不通过互联网就可以连接到这些附近的设备，设备间可以无缝的分享数据。
应用可以直接集成[MCPeerPickerViewController](https://developer.apple.com/library/prerelease/ios/documentation/MultipeerConnectivity/Reference/MCPeerPickerViewControllerRef/Reference/Reference.html#//apple_ref/occ/cl/MCPeerPickerViewController)类来展示用户附近可选的设备，可以通过[MCNearbyServiceBrowser](https://developer.apple.com/library/prerelease/ios/documentation/MultipeerConnectivity/Reference/MCNearbyServiceBrowserClassRef/Reference/Reference.html#//apple_ref/occ/cl/MCNearbyServiceBrowser)在程序中发现管理附近的设备。
更多信息参见[Multipeer Connectivity Framework Reference](https://developer.apple.com/library/prerelease/ios/documentation/MultipeerConnectivity/Reference/MultipeerConnectivityFramework/_index.html#//apple_ref/doc/uid/TP40013328)
##Games

####Sprite Kit Framework   
类似cocos2d 的 游戏框架。
####Game Controller Framework
对游戏手柄之类的硬件外设支持。
####Game Center Improvements

##New Frameworks
* Game Controller framework
* Sprite Kit framework
* Multipeer Connectivity framework 
* JavaScript Core framework (JavaScriptCore.framework) 

	提供了用 OBjc封装的标准JS对象使用此框架来评估JavaScript代码和解析JSON数据. 	
* Media Accessibility framework (MediaAccessibility.framework) 管理多媒体文件的隐藏式字幕
* Safari Services framework (SafariServices.framework) 支持应用内添加URL到safari reading list  	

##Objective-C

objc 增强了对模块的支持， 提高了编译速度和索引速度。用xcode 5 新建的工程默认都是打开了模块支持，老的工程要通过修改工程的配置 **Enable Modules setting**

## Deprecated APIs

* UUID	 使用 identifier 代替
* **Audio Toolbox framework** 中的 **AudioSession** API 废弃了，要使用 **AV Foundation framework**
中的**AVAudioSession** 代替
* 在iOS 7中获取 mac 地址 系统会返回 02:00:00:00:00:00 建议使用 **UIDevice** **identifierForVendor** 代替
 

##Other
*  状态栏，导航栏和应用UI不再有界限区分
*  所有的导航栏跟toolbar 上的按钮都变成了文字 
* Barcode Scanning 条码扫描
* iBeacons 通过蓝牙4.0获取位置数据
* 60-fps Video Capture：iOS 7 允许应用以60帧每秒录制视频
* 集成了腾讯微博
* MessageUI里添加了附件按钮
* Store Kit在内购方面采用了新的订单系统，这将可以实现对订单的本机验
* iOS7的模拟器支持iCloud调试 推送调试了。
* iOS6开始就支持多种字体了。一直没发现 [字体列表](http://support.apple.com/kb/HT5484)
* 应用单独设置VPN
* UIView有了tintColor属性：可以使用一个tint color，并对view和它的subview有影响。








