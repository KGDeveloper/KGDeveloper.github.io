---
layout:     Cocoapods
title:      Cocoapods私有库
subtitle:   Cocoapods私有库
date:       2020-06-17
author:     KG丿夏沫
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - iOS
    - Cocoapods私有库
    - Cocoapods
---

# Cocoapods私有库

    在平时工作中，很多时候都是在做重复的事情，赋值(Ctrl + C)、粘贴(Ctrl + V)，所以感觉很累很枯燥，所以今天心血来潮，我们可以用别人的三方库，例如AFNetworking、SDWebImage。。。。,那么我也可以把自己的常用的库放到Github，每次使用的时候直接拉去下来，省时省力啊，所以有了今天这一篇博客的诞生，怎么创建自己的Cocoapods私有库，哎~，一路艰辛啊，最终成功了，我很😺😸😸。

## 前期准备工作

    1、将自己封装的文件或者库，进行整理、修改，借用一句网络用语”往往最高端的食物都是用最简单的方式烹饪“，嗯，就是这个意思，我们把自己的库进过修改封装后，不管别人拿来使用还是自己使用，只需要调用几个API就实现功能。

    2、如果自己的库中用到了资源文件，那么整理出来，创建一个bundle文件，以便使用库的时候不用找图片，而且也减少报错和bug。

    3、为自己的库起一个高端大气上档次的名字。

## 开始进入正题，创建私有库

    1、使用Cocoapods的模板创建私有库，打开终端，依次输入以下命令，中间不会报错，放心大胆的做吧！

    # 创建命令
    pod lib create ProjectName
    # 选择编程语言
    What language do you want to use?? [ Swift / ObjC ]
    Objc  
 
    # 在你的项目中是否创建一个demo工程，为了方便测试，我选择了Yes
    Would you like to include a demo application with your library? [ Yes / No ]
    Yes  
 
    # 测试框架选择哪一个
    Which testing frameworks will you use? [ Specta / Kiwi / None ]
    None
 
    #要不要做视图测试
    Would you like to do view based testing? [ Yes / No ]
    Yes
 
    # 类前缀名
    What is your class prefix?
    KG


示例如下：

```
 ➜  ~ pod lib create KGPhotoLibary
 Cloning `https://github.com/CocoaPods/pod-template.git` into `KGPhotoLibary`.
 Configuring KGPhotoLibary template.
 
 ------------------------------
 
 To get you started we need to ask a few questions, this should only take a minute.
 
 2020-06-17 14:42:48.346 defaults[33644:760448] 
 The domain/default pair of (org.cocoapods.pod-template, HasRunBefore) does not exist
 If this is your first time we recommend running through with the guide: 
  - https://guides.cocoapods.org/making/using-pod-lib-create.html
  ( hold cmd and double click links to open in a browser. )
 
  Press return to continue.
 
 
 What platform do you want to use?? [ iOS / macOS ]
   iOS
 
 What language do you want to use?? [ Swift / ObjC ]
   ObjC
 
 Would you like to include a demo application with your library? [ Yes / No ]
   Yes
 
 Which testing frameworks will you use? [ Specta / Kiwi / None ]
   None
 
 Would you like to do view based testing? [ Yes / No ]
   No
 
 What is your class prefix?
   KG
 
 Running pod install on your new library.
 
 Analyzing dependencies
 Downloading dependencies
 Installing KGPhotoLibary (0.1.0)
 Generating Pods project
 Integrating client project
 
 [!] Please close any current Xcode sessions and use `KGPhotoLibary.xcworkspace` for this project from now on.
 Pod installation complete! There is 1 dependency from the Podfile and 1 total pod installed.
 
  Ace! you're ready to go!
  We will start you off by opening your project in Xcode
   open 'KGPhotoLibary/Example/KGPhotoLibary.xcworkspace'
 
 To learn more about the template see `https://github.com/CocoaPods/pod-template.git`.
 To learn more about creating a new pod, see `https://guides.cocoapods.org/making/making-a-cocoapod`.
 ```