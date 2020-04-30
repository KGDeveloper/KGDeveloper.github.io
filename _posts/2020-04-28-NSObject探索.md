---
layout:     post
title:      NSObject 探索
subtitle:   NSObject 探索以及面试题
date:       2020-04-28
author:     KG丿夏沫
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - iOS
---

1.NSObject是OC中的最常用的类，所以从探索NSObject类开始，逐步深入学习iOS，了解OC。

为了方便探索，直接上代码，然后一起研究，省略其他无关代码

``` #import <Foundation/Foundation.h>

NS_ASSUME_NONNULL_BEGIN

@interface KGManager : NSObject

@property (nonatomic,copy) NSString *str;
@property (nonatomic,assign) NSInteger count;
@property (nonatomic,assign) BOOL isAnswer;

@end

NS_ASSUME_NONNULL_END

#import <objc/runtime.h>
#import <malloc/malloc.h>

KGLog(@"-----%zu---------------%zu",class_getInstanceSize([KGManager class]),malloc_size((__bridge const void *)[[KGManager alloc] init]));

```

我们先来猜测下，以上log在控制台应该输出什么？这个问题先留着，然后我们先把KGManager这个类里面的所有属性都注释了，我们只获取下，新建的NSObject类在内存中占多少字节，要讨论这个问题，我们先得了解，NSObject的结构，里面包含什么，这个我们利用Clang编译器，将Objective-C语言转换为C++。

打开终端输入```xcrun -sdk iphoneos clang -arch arm64 -rewrite-objc KGManager.m -o KGManager.arm64.cpp```

然后等待编译完成后，打开刚才编译出来的cpp文件，然后全局搜索NSObject_IMPL，找到如下位置<img src="http://chuantu.xyz/t6/731/1588212474x3703728804.png">我们看到里面只有isa指针。指针在64位架构中占用8个字节，那么回到刚才的问题当我注释KGManager类中的所有属性后，运行代码，在控制台会打印多少？根据刚才的查看，按理来说应该是8个字节，现在我们看下
<img src="http://chuantu.xyz/t6/731/1588212705x3661913030.png">
我们可以看到控制台打印的确实是8，但是又有个新问题，后面为啥打印的那个是16呢？那是因为我们通过runtime获取到的是对象实际占用的大小，而通过malloc_size获取到的是系统分配的大小。

再次回到最开始的问题，当我的类拥有这些属性的时候应该是打印多少呢？下面我们打开注释，运行程序看下效果<img src="http://chuantu.xyz/t6/731/1588212786x3661913030.png">
我们可以通过控制台看到打印的是32，系统分配的也是32，那是因为对象本身有一个isa指针占用了8个字节，然后NSString属性占用了8个字节，NSInteger属性占用了8个字节，BOOL属性也是占用了8个字节，所以刚好是32个字节。

那么现在我再提一个问题，如果我注释三个属性中随意一个，那应该打印出来多少呢？还是32吗？我们运行下看看<img src="http://chuantu.xyz/t6/731/1588212840x3703728804.png">
打印24个字节，这个对的，那么为啥后面打印还是32呢？这就是系统分配内存的规则了，始终是16的整数倍。

在结尾我想问，KGManager的isa指向谁？supperclass又是谁？NSObject的isa指向谁？supperclass又是指向谁的？

如有理解不当，请及时联系我改正，以防误导，同时欢迎iOS开发者交流学习，QQ群：960744862