---
layout:     post
title:      iOS中runtime的基本用法
subtitle:   iOS中runtime的基本用法
date:       2020-07-20
author:     KG丿夏沫
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - iOS
    - runtime
    - 面试题
    - 方法交换
    - 扩展添加属性
    - 给类动态添加方法
---

# iOS中runtime的基本用法

## 一、基本介绍

>runtime在OC中扮演什么角色，它的作用是什么？为什么我们面试的时候经常会问道runtime？它在实际开发中到底用来干嘛？今天我们就一起探索一下runtime。

>runtime根据<a href="https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40008048">苹果官方文档的介绍</a>，从编译时间和链接时间到运行时，Objective-C语言会尽可能多地推迟决策。只要有可能，它就会动态地执行操作。这意味着该语言不仅需要编译器，而且还需要运行时系统来执行编译后的代码。运行时系统充当Objective-C语言的一种操作系统。这就是使语言有效的原因。简而言之意思就是把更多的决策从编译时和链接时推迟到运行时。

>runtime在OC中就是一种黑魔法，可以说只有你想不到的，没有它做不到的，很多东西我们使用普通API实现很复杂的东西，或许runtime很简单就给你实现了，很多正常逻辑来说做不了的事情，它可以帮你完成。所以面试的时候runtime是必问的，而且问的最多的都是关于它的，因为它很神奇，也很吸引人。下面就进入主题了。

## 二、runtime在实际开发中的应用

1、修改类的实例变量

>在iOS开发中，我们在一个类里面声明一个成员变量或者属性，当我们从外部去修改这个属性的时候，我们可以正常访问，并且程序不会报错，但是当我们从外部去访问成员变量的时候，程序就报错了，那么我们怎么取解决这个问题呢？接下来我们一起看一下：

（1）、成员变量前面添加修饰符，使得外部能够正常访问类的成员变量，并且能够赋值。这里需要注意下，只有在.h文件中声明的成员变量，添加修饰符后，外部可以访问，.m文件中无论成员变量还是属性，外部都无法直接调用。如果想要在外部访问.m文件中声明的成员变量以及属性，需要使用runtime去访问赋值以及取值，具体代码如下：

KGPerson类的.h文件

```
#import <Foundation/Foundation.h>

@interface KGPerson : NSObject{
    @public
    int age;
}

@property (nonatomic, copy) NSString *name;

@end
```

KGPerson类的.m文件

```
#import "KGPerson.h"

@interface KGPerson (){
    NSString *nikeName;
}

@property (nonatomic,copy) NSString *iconUrl;

@end

@implementation KGPerson

@end
```

>可以看出我们在.h文件中声明了一个成员变量age，正常来说如果没有添加修饰符```@public```，那么外部访问age就会报错，当我们添加修饰符后，外部可以正常访问我们的成员变量，并且能够正常取值赋值。具体调用我们看下代码和打印结果：

<img src="img/20200720001.png" alt="代码和打印结果">

>从打印结果以及调用方式我们可以看出，属性直接可以通过```.```语法去访问，而成员变量我们需要通过箭头函数```->```去访问。并且我们可以看出，当我们使用```@public```修饰，将age属性暴露给外部的时候，外部是可以正常访问赋值的。然后我们继续看，我们在.m文件中也有一个成员变量和一个属性，我们无法从外部直接通过类对象去访问，那么我们就不能从外部处理了吗？接下来看看我们黑魔法```runtime```的使用，怎么实现从外部访问类的内部属性的，并且赋值取值。

<img src="img/20200720002.png" alt="访问类内部成员变量以及属性">

>可以看出我们不仅能够访问到类内部的成员变量，也能访问到类内部的属性，并且能够取值赋值。接下来一起解读下这些API。```Ivar _Nonnull * _Nullable class_copyIvarList(Class _Nullable cls, unsigned int * _Nullable outCount)```官方解释：返回一个Ivar类型的指针数组，用于描述类声明的实例变量。不包括父类声明的任何实例变量。```const char * _Nullable ivar_getName(Ivar _Nonnull v)```官方解释：返回实例变量的名称，是const char*类型的。```void object_setIvar(id _Nullable obj, Ivar _Nonnull ivar, id _Nullable value)```官方解释：设置对象中实例变量的值，第一个参数是对象类型，也就是我们需要设置的```_person```，第二个参数是一个Ivar类型的，也就是我们当前需要设置的实例变量描述```var```，第三个参数是一个id类型的，也就是我们给实例变量赋的值。```id _Nullable object_getIvar(id _Nullable obj, Ivar _Nonnull ivar)```官方解释：读取对象中实例变量的值，第一个参数是对象类型，也就是我们的```_person```，第二个参数是一个Ivar类型的，也就是我们当前需要读取的实例变量描述```var```。```free(ivar);```这句代码必须写，```class_copyIvarList```返回的数组包含*outCount指针，后跟空终止符。必须使用free()释放数组。在这个基础上还可以扩展很多，就需要自己慢慢去研究了。

>扩展：我们作为一个爱学习，爱进去的程序员来说，这个远远无法满足与我，所以我们再来完善下，首先我们创建一个```NSObject```类的扩展类```KGObject```，然后在扩展类.h里面声明两个方法，并在.m里面实现两个方法。如下：

```
#import <Foundation/Foundation.h>

@interface NSObject (KGObject)

- (void)setClassIvarWithName:(NSString *)name withValue:(id)value;

- (id)getClassIvarWithName:(NSString *)name;

@end


#import "NSObject+KGObject.h"
#import <objc/runtime.h>

@implementation NSObject (KGObject)

- (void)setClassIvarWithName:(NSString *)name withValue:(id)value{
    unsigned int count = 0;
    Ivar *ivar = class_copyIvarList([self class], &count);
    for (int i = 0; i < count; i++) {
        Ivar var = ivar[i];
        const char *varName = ivar_getName(var);
        if ([[NSString stringWithUTF8String:varName] isEqualToString:name]) {
            object_setIvar(self, var, value);
            break;
        }
    }
    free(ivar);
}

- (id)getClassIvarWithName:(NSString *)name{
    
    id value = nil;
    
    unsigned int count = 0;
    Ivar *ivar = class_copyIvarList([self class], &count);
    for (int i = 0; i < count; i++) {
        Ivar var = ivar[i];
        const char *varName = ivar_getName(var);
        if ([[NSString stringWithUTF8String:varName] isEqualToString:name]) {
            value = object_getIvar(self, var);
            break;
        }
    }
    free(ivar);
    
    return value;
}

@end

```

>嗯，基本上看着舒服多了，其他的在这个扩展的基础上自己探索吧。我们继续下一个话题，给类动态添加方法。

2、给类动态添加方法

>还是之前创建的Demo，还是之前那个类```KGPerson```，我们写这样一句代码```[_person performSelector:@selector(run)];```，嘿嘿，系统没有报错，没有警告，那么是不是说我们运行也是OK的呢？当我点击运行，程序奔溃了，而且爆出一个经典错误```-[KGPerson run]: unrecognized selector sent to instance 0x60000040ed60```，哈哈，没有找到方法，没错我没有在```KGPerson```这个类里面声明和实现```run```方法，今天我们研究下runtime的黑魔法就从这开始吧，不要和我说在```KGPerson```中实现```forwardInvocation```方法啥的，这个不是我想要的。我就这么刚，我就要在调用它的这个类给它动态添加一个方法，而且名字就叫```run```，接下来一起看一下。

>runtime有这么一个API```BOOL class_addMethod(Class _Nullable cls, SEL _Nonnull name, IMP _Nonnull imp, const char * _Nullable types)```，(⊙o⊙)…有点长哦，这个方法返回一个状态，表示给类添加方法成功还是失败，第一个参数```Class```类型的，就是我们的_person类，第二个参数是个```SEL```类型的，这个很常见的，是个方法选择器，第三个参数```IMP```就是方法编号，第四个参数```const char *```类型的types，就是用来描述方法的返回值，参数这些，具体的可以看下<a href="https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtTypeEncodings.html#//apple_ref/doc/uid/TP40008048-CH100">官方文档</a>，看下下面示例以及代码运行效果。

<img src="20200720004.png" alt="代码运行效果">

>从运行结果，我们看出方法添加成功了。很简单吧？是的就是这么简单。这个不难就过了，下面继续讲解方法交换。

3、方法交换

>对于这个黑魔法，我很喜欢，比如说大型项目，页面很多的时候，你刚去公司，让你开发新功能，让你在一个比较剩的页面改东西啥的，一堆代码里面扒页面很烦躁，所以你可以写一个方法，把系统API交换下，然后每次进入页面打印一下ViewController的名字，这样找起来轻松很多，所以接下来先来个简单的。

>我们创建一个```KGViewController```继承于```ViewController```，然后在```KGViewController```里面写两个方法，如下：

```
- (void)run{
    NSLog(@"哈哈哈哈");
}

- (void)eat{
    NSLog(@"呵呵呵呵");
}
```

>然后我们在```viewDidLoad```方法里面给它进行方法交换，如下所示：

```
Method m1 = class_getInstanceMethod([self class], @selector(run));
Method m2 = class_getInstanceMethod([self class], @selector(eat));
method_exchangeImplementations(m1,m2);
```

>当我们调用```run```方法的时候，就会走```eat```方法的实现。接下来一起看下这些方法的介绍.```Method _Nullable class_getInstanceMethod(Class _Nullable cls, SEL _Nonnull name)```返回指定类的实例方法，第一个参数就是给一个指定的类，第二个```SEL```很熟悉了，方法选择器，需要我们给定一个检索的方法选择器。```Method _Nullable class_getClassMethod(Class _Nullable cls, SEL _Nonnull name)```返回指定类的类方法，第一个参数就是给一个指定的类，第二个```SEL```很熟悉了，方法选择器，需要我们给定一个检索的方法选择器。这两个方法不能混淆了，要不然方法交换就出问题了。第一个是获取实例方法(-方法)，第二个是获取类方法(+方法)。```void method_exchangeImplementations(Method _Nonnull m1, Method _Nonnull m2)```进行方法交换，打开方法交换API介绍的时候，系统给了一个示例：

```
IMP imp1 = method_getImplementation(m1);
IMP imp2 = method_getImplementation(m2);
method_setImplementation(m1, imp2);
method_setImplementation(m2, imp1);
```

>也是给我们解释下，方法交换的原理，其实就是更换方法的IMP即更换方法的函数指针。然后交换ViewController的方法，实现打印类名，就留作家庭作业吧，哈哈！！！！

4、给类的分类添加属性

>首先来一道面试题：类的分类是否可以添加属性？

>正常逻辑上来说，不能的，因为分类里面声明的属性，系统不会自动生成setter和getter方法。既然我们知道了系统不会自动为我们生成setter和getter方法，那么我们就手动去实现setter以及getter方法。正常来说我们实现setter以及getter方法是这么做的:

```
- (void)setUserInfo:(NSObject *)userInfo{
    _userInfo = userInfo;
}

- (NSObject *)userInfo{
    return _userInfo;
}
```

>但是在分类中不行，因为系统没有为我们生成成员变量，而且页不允许我们在分类里面添加成员变量。所以，我就需要换种思路，利用runtime，接下来一起看下怎么实现setter以及getter方法。

```
#import "NSObject+KGObject.h"
#import <objc/runtime.h>

static NSString *key = @"UserInfoConstKey";

@implementation NSObject (KGObject)

- (void)setUserInfo:(NSObject *)userInfo{
    objc_setAssociatedObject(self, &key, userInfo, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
}

- (NSObject *)userInfo{
    return objc_getAssociatedObject(self, &key);
}
```

>先来解读函数```void objc_setAssociatedObject(id _Nonnull object, const void * _Nonnull key,id _Nullable value, objc_AssociationPolicy policy)```文档解释：使用给定键和关联策略为给定对象设置关联值。即：使用我们指定的```key```（给定键）和属性声明修饰符```objc_AssociationPolicy```（关联策略），给指定的对象```object```设置关联值```value```，这个函数没有返回值。需要注意下，这块给定键需要的是一个```const void *```指针，所以我们先声明一个静态字符串key，取地址传入进去。```objc_AssociationPolicy```这个我们也一起看下，具体有哪些关联策略。

```
typedef OBJC_ENUM(uintptr_t, objc_AssociationPolicy) {
    OBJC_ASSOCIATION_ASSIGN = 0,           /**< Specifies a weak reference to the associated object. */指定对关联对象的弱引用
    OBJC_ASSOCIATION_RETAIN_NONATOMIC = 1, /**< Specifies a strong reference to the associated object. 指定对关联对象的强引用，关联不是原子化的
                                            *   The association is not made atomically. */
    OBJC_ASSOCIATION_COPY_NONATOMIC = 3,   /**< Specifies that the associated object is copied. 指定已复制关联对象，关联不是原子化的
                                            *   The association is not made atomically. */
    OBJC_ASSOCIATION_RETAIN = 01401,       /**< Specifies a strong reference to the associated object.指定对关联对象的强引用，关联是原子化的
                                            *   The association is made atomically. */
    OBJC_ASSOCIATION_COPY = 01403          /**< Specifies that the associated object is copied.指定已复制关联对象，关联是原子化的
                                            *   The association is made atomically. */
};
```

>上面这个枚举里面的原子化和原子化我们用property声明时的使用状态表示出来是这样:

```
/*OBJC_ASSOCIATION_RETAIN_NONATOMIC*/
@property (nonatomic, strong)
/*OBJC_ASSOCIATION_COPY_NONATOMIC*/
@property (nonatomic, copy)

/*OBJC_ASSOCIATION_RETAIN*/
@property (atomic, strong)
/*OBJC_ASSOCIATION_COPY*/
@property (atomic, copy)
```

>接着来看取值函数```id _Nullable objc_getAssociatedObject(id _Nonnull object, const void * _Nonnull key)```文档解释：为给定键返回给定对象的关联值。即：使用我们给定的键```key```，从给定的对象```object```中取出我们想要的值，返回的是一个```id```类型。

## 三、总结

>在iOS开发中，如果去阅读那些优秀的三方库，runtime就是家长便饭一样，到处都有，但是使用runtime也需要看需求，具体还需要自己在实际开发中慢慢去总结。到此我的对于runtime的基本使用已经探索完成了，欢迎开发者一起探讨交流。QQ：969840184