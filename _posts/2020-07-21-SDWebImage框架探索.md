---
layout:     post
title:      SDWebImage框架探索
subtitle:   SDWebImage框架探索
date:       2020-07-21
author:     KG丿夏沫
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - iOS
    - SDWebImage
    - 面试题
---

# SDWebImage框架探索

> 基于目前最新版本5.8.0版本进行使用以及解析分析其原理，优秀的三方库有多很多知识点值得学习，比如说架构思想、设计模式、继承方式、扩展方式等等一系类的东西。

## 一、大概框架介绍

>SDWebImage->5.8.0版本只有两个文件夹，```Core```核心类所在文件夹以及```Private```一些私人扩展类所在文件夹。先介绍下私人扩展类文件夹。

1、```Private```文件夹中类的大概介绍：

>```SDDeviceHelper```类是对设备内存的读取类，提供两个方法```+ (NSUInteger)totalMemory```获取当前系统剩余可用内存，```+ (NSUInteger)freeMemory```获取当前内存可释放内存也就是当前打开APP所占用的内存。在类的.m文件中使用到了一个```NSProcessInfo```类，这个类的属性基本上都是只读类型的，他是用来描述当前进程信息的类。

>```SDWeakProxy```类是一个继承于虚基类```NSProxy```的虚类，主要就是为了防止循环引用问题，在设置target的时候使用虚基类绑定，在虚基类内部使用weak声明了对象，然后持有对象，这样就不会引起循环引用。提供了一个类方法和一个init初始化方法，最后都是为了将传入的target绑定到weak声明的对象上。

>```SDHexString```类是对UIColor类的扩展，只提供了一个只读属性，来获取当前UIColor类型的颜色的hex字符串值。

>```SDRoundedCorners```类是在macOS开发需要的分类，对NSBezierPath类创建分类，实现画圆角，但是基于这个类我们可以使用相同的思想，实现iOS开发需要的分类，创建一个UIBezierPath类的分类，比如定义为```SDRoundedCorners```，然后方法里面参数需要进行修改，因为Mac开发和iOS开发框架不太一样。实际代码如下：

```
#import <UIKit/UIKit.h>

NS_ASSUME_NONNULL_BEGIN

@interface UIBezierPath (SDRoundedCorners)

+ (nonnull instancetype)sd_bezierPathWithRoundedRect:(CGRect)rect byRoundingCorners:(UIRectCorner)corners cornerRadius:(CGFloat)cornerRadius;

@end

NS_ASSUME_NONNULL_END


#import "UIBezierPath+SDRoundedCorners.h"

@implementation UIBezierPath (SDRoundedCorners)

+ (nonnull instancetype)sd_bezierPathWithRoundedRect:(CGRect)rect byRoundingCorners:(UIRectCorner)corners cornerRadius:(CGFloat)cornerRadius{
    UIBezierPath *bezierPath = [UIBezierPath bezierPath];
    
    //获取react允许的圆角最大半径
    CGFloat maxConrner = MIN(CGRectGetWidth(rect), CGRectGetHeight(rect))/2;
    
    CGFloat topLeftRadius = MIN(maxConrner, (corners & UIRectCornerTopLeft) ? cornerRadius : 0);
    
    CGFloat topRightRadius = MIN(maxConrner, (corners & UIRectCornerTopRight) ? cornerRadius : 0);
    
    CGFloat bottomLeftRadius = MIN(maxConrner, (corners & UIRectCornerBottomLeft) ? cornerRadius : 0);
    
    CGFloat bottomRightRadius = MIN(maxConrner, (corners & UIRectCornerBottomRight) ? cornerRadius : 0);
    
    [bezierPath moveToPoint:CGPointMake(CGRectGetMinX(rect), CGRectGetMinY(rect)+topLeftRadius)];
    
    //左上角
    [bezierPath addQuadCurveToPoint:CGPointMake(CGRectGetMinX(rect)+topLeftRadius, CGRectGetMinY(rect)) controlPoint:CGPointMake(CGRectGetMinX(rect), CGRectGetMinY(rect))];
    
    
    [bezierPath addLineToPoint:CGPointMake(CGRectGetMaxX(rect)-topRightRadius, CGRectGetMinY(rect))];
    
    //右上角
    [bezierPath addQuadCurveToPoint:CGPointMake(CGRectGetMaxX(rect), CGRectGetMinY(rect)+topRightRadius) controlPoint:CGPointMake(CGRectGetMaxX(rect), CGRectGetMinY(rect))];
    
    
    [bezierPath addLineToPoint:CGPointMake(CGRectGetMaxX(rect), CGRectGetMaxY(rect)-bottomRightRadius)];
    
    //右下角
    [bezierPath addQuadCurveToPoint:CGPointMake(CGRectGetMaxX(rect)-bottomRightRadius, CGRectGetMaxY(rect)) controlPoint:CGPointMake(CGRectGetMaxX(rect), CGRectGetMaxY(rect))];
    
    
    [bezierPath addLineToPoint:CGPointMake(CGRectGetMinX(rect)+bottomLeftRadius, CGRectGetMaxY(rect))];
    
    //左下角
    [bezierPath addQuadCurveToPoint:CGPointMake(CGRectGetMinX(rect), CGRectGetMaxY(rect)-bottomLeftRadius) controlPoint:CGPointMake(CGRectGetMinX(rect) , CGRectGetMaxY(rect))];
    
    [bezierPath addLineToPoint:CGPointMake(CGRectGetMinX(rect), CGRectGetMinY(rect)+topLeftRadius)];
    
    [bezierPath closePath];
    
    return bezierPath;
}

@end
```

>这样就实现了在iOS环境下的根据指定的CGReat对UIView的子类进行画圆角，并且可以实现只对单个角进行画圆角。

>```SDAsyncBlockOperation```类以及```SDImageCachesManagerOperation```类都是```NSOperation```的子类，区别在于前者是通过Block实现代码块回调，并且创建简单，和系统提供的```NSBlockOperation```的用法类似，只是在内部多添加了KVO对当前线程是否正在执行以及是否执行完成进行监听。后者有一个只读属性```pendingCount```用来查看当前线程正在执行的任务数量，可以通过```- (void)beginWithTotalCount:(NSUInteger)totalCount```方法进行设置，```- (void)completeOne```方法是减少一个执行的任务数，```- (void)done```方法是表示所有任务都已执行完成，所有状态进行重置，这个类只是用于操作管理，但是不用于队列操作管理。

>```SDDisplayLink```类和定时器有点类似，但是在iOS环境下创建的是```CADisplayLink```对象，```CADisplayLink```也是和NSTimer一样可以添加到NSRunLoop中执行任务，但是不同于NSTimer的一点是，NSTimer可以设置循环间隔，但是```CADisplayLink```不能设置循环间隔，而且```CADisplayLink```循环间隔和屏幕刷新频率是一致的，也就是1s走60次。

>```SDFileAttributeHelper```文件扩展类，实现了指定路径下读取文件夹内所有文件列表、指定路径下指定文件是否存在检测、指定路径下对指定文件进行数据写入、指定路径下对指定文件进行读取以及指定路径下对指定文件进行删除操作。

>```SDImageAssetManager```类和```SDWebImageCompat```类是对图片资源的一些拷贝操作或者读取写入操作。

## 二、SDWebImage核心介绍

>平时开发的时候基本上都是使用```- (void)sd_setImageWithURL:(nullable NSURL *)url```方法进行网络图片异步加载，那么就从这个方法入手，然后开始解读作者的架构思想。

>```UIImageView+WebCache```类是UIImageView类的分类，提供了多种异步加载网络图片的方法，所有方法最后都是调用UIView的扩展类```UIView+WebCache```类里面的```- (void)sd_internalSetImageWithURL:(nullable NSURL *)url placeholderImage:(nullable UIImage *)placeholder options:(SDWebImageOptions)options context:(nullable SDWebImageContext *)context setImageBlock:(nullable SDSetImageBlock)setImageBlock progress:(nullable SDImageLoaderProgressBlock)progressBlock completed:(nullable SDInternalCompletionBlock)completedBlock```这个方法，然后我们一起探索这个方法的内部实现。

>在方法实现的内部先进行判断上下文```SDWebImageContext```是否存在，而这个```SDWebImageContext```是一个NSDictionary，其内部是通过```SDWebImageContextOption```为key，以id类型对象为value进行存储。```SDWebImageContextOption```是一个枚举类型，有以下可选类型：

```
SDWebImageContextSetImageOperationKey       //用作视图类别的操作键，用于存储图像加载操作，比如说UIButton在不同状态下加载不同图片
SDWebImageContextCustomManager              //使用一个实例来识别当前控制器manager
SDWebImageContextImageCache                 //用于在图像加载管道期间覆盖图像管理员的缓存
SDWebImageContextImageLoader                //用于在图像加载管道中覆盖图像管理员的加载器
SDWebImageContextImageCoder                 //用于在图像加载过程中覆盖图像解码(包括渐进)和编码的默认图像codre
SDWebImageContextImageTransformer           //用于图像加载完成后进行图像变换，并将变换后的图像存储到缓存中
SDWebImageContextImageScaleFactor           //指定图像缩放因子
SDWebImageContextImagePreserveAspectRatio   //指示在生成缩略图图像(或矢量格式的位图图像)时是否保持原始的纵横比
SDWebImageContextImageThumbnailPixelSize    //指示是否生成缩略图
SDWebImageContextQueryCacheType             //指定要查询的缓存源的SDImageCacheType原始值
SDWebImageContextStoreCacheType             //指定刚刚下载图像并将存储到缓存中的存储缓存类型
SDWebImageContextOriginalQueryCacheType     //指定要查询的缓存源的SDImageCacheType原始值,但控制查询缓存类型为原始图像时，使用图像转换器功能
SDWebImageContextOriginalStoreCacheType     //指定刚刚下载图像并将存储到缓存中的存储缓存类型,但是当你使用图像转换器特性时，控制原始图像的存储缓存类型
SDWebImageContextAnimatedImageClass         //一个类对象，它的实例是一个' UIImage/NSImage '子类，并采用' SDAnimatedImage '协议,用来提高动画图像渲染性能
SDWebImageContextDownloadRequestModifier    //用来修改下载URL和选项的原始的请求
SDWebImageContextDownloadResponseModifier   //来修改图像下载响应
SDWebImageContextDownloadDecryptor          //解密图像下载数据
SDWebImageContextCacheKeyFilter             //用于将URL转换为缓存键。当管理器需要缓存键来使用图像缓存时使用
SDWebImageContextCacheSerializer            //用于将已解码的图像、源下载的数据转换为实际数据。它用于管理器存储映像到磁盘缓存
```

>从```context```中获取配置选项，因为是一个字典了类型，为了防止读取时被修改，所以先进行了一次cpoy操作，如果是不可变的不影响，如果是可变的直接拷贝一份不可变的进行操作，然后使用```SDWebImageContextSetImageOperationKey```来读取视图类别操作键，如果说没有给出，那么先创建一个```SDWebImageMutableContext```可变上下文对象，```SDWebImageMutableContext```是一个可变字典，其内部是通过```SDWebImageContextOption```为key，以id类型对象为value进行存储.然后使用类名映射为字符串获取到类名字符串值进行赋值.

>知识点：类名映射，即：通过类名获取包含类名称的字符串

>以```SDWebImageContextSetImageOperationKey```为key，以映射后的类名字符串为value，然后对可变对象进行一次copy操作变成不可变对象。然后调用UIView的分类WebCache类的属性的setter方法进行赋值。

>知识点：利用runtime对分类的属性添加setter和getter方法。

>然后调用```- (void)sd_cancelImageLoadOperationWithKey:(nullable NSString *)key```方法，取消当前提供的key的下载任务。

>知识点：方法内部使用```@synchronized (self) {}```互斥锁进行加锁保护操作

>然后调用UIView分类的WeCache类的sd_imageURL属性的setter方法赋值，然后对图片加载策略```SDWebImageOptions```进行判断，这是一个枚举，有以下可选类型：

```
SDWebImageRetryFailed                       //禁止URL请求失败后加入黑名单
SDWebImageLowPriority                       //默认情况下图片下载是在UI交互时，这个属性是禁止此功能，导致图片下载可能在UIScrollview滑动减速时
SDWebImageProgressiveLoad                   //支持图像在下载过程中渐进式显示，默认情况下是下载完成后显示
SDWebImageRefreshCached                     //即使图像已经被缓存了，但是还是在需要的时候请求URL进行获取
SDWebImageContinueInBackground              //在APP进入后台时，下载图片
SDWebImageHandleCookies                     //通过设置处理存储在NSHTTPCookieStore中的cookie
SDWebImageAllowInvalidSSLCertificates       //允许使用不受信任的SSL证书
SDWebImageHighPriority                      //默认情况下按照排队顺序下载，这个属性可以让下载任务排到前面
SDWebImageDelayPlaceholder                  //默认情况下，提供的占位图在图像下载加载完成前一直显示，这个属性会让占位图延迟到图像下载加载完成后显示
SDWebImageTransformAnimatedImage            //不管什么情况下，都使用图像变换
SDWebImageAvoidAutoSetImage                 //在图像下载完成后不是立马显示，而是等待对图像的操作完成后再显示
SDWebImageScaleDownLargeImages              //将图像缩小到与限制的大小一致进行解码
SDWebImageQueryMemoryData                   //默认情况下如果缓存中存在对象，不会去查找映射对象，但是这个属性会强制去查找映射对象，而且是异步的
SDWebImageQueryMemoryDataSync               //可以配置上面SDWebImageQueryMemoryData来实现同步查询
SDWebImageQueryDiskDataSync                 //
```