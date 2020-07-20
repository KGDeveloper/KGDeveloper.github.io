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

>

4、给类的扩展添加属性
