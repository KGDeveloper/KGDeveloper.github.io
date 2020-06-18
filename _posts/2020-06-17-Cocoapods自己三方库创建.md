---
layout:     post
title:      Cocoapods自己三方库创建
subtitle:   Cocoapods自己三方库创建
date:       2020-06-17
author:     KG丿夏沫
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - iOS
    - Cocoapods自己三方库创建
    - Cocoapods
    - 三方库
---

# Cocoapods自己三方库创建

    在平时工作中，很多时候都是在做重复的事情，赋值(Ctrl + C)、粘贴(Ctrl + V)，所以感觉很累很枯燥，所以今天心血来潮，我们可以用别人的三方库，例如AFNetworking、SDWebImage。。。。,那么我也可以把自己的常用的库放到Github，每次使用的时候直接拉去下来，省时省力啊，所以有了今天这一篇博客的诞生，怎么创建自己的Cocoapods自己三方库创建，哎~，一路艰辛啊，最终成功了，我很😺😸😸。

## 前期准备工作

    1、将自己封装的文件或者库，进行整理、修改，借用一句网络用语”往往最高端的食物都是用最简单的方式烹饪“，嗯，就是这个意思，我们把自己的库进过修改封装后，不管别人拿来使用还是自己使用，只需要调用几个API就实现功能。

    2、如果自己的库中用到了资源文件，那么整理出来，创建一个bundle文件，以便使用库的时候不用找图片，而且也减少报错和bug。

    3、为自己的库起一个高端大气上档次的名字。

## 开始进入正题，创建私有库

1、使用Cocoapods的模板创建私有库，打开终端，依次输入以下命令，中间不会报错，放心大胆的做吧！

    # 创建命令（此处ProjectName就是你的私有库名称，后面要用到，取名最好严谨一些）
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

2、然后到你的<a href="https://github.com/">Github</a>，创建一个新的库，库名称就是前面创建的时候起的名称

示例：<img src="/img/github_pods.png"  alt="Github创建公共库" />

3、提交到你的Github仓库，因为Cocoapods创建出来的文件里面包含了.git，所以我们只需要把本地仓库和Github远程仓库关联起来就行，具体步骤如下：

  cd ./上一步骤创建出来的Cocoapods项目文件夹下

  //关联本地仓库和Github远程仓库
  git remote add origin 在第2步创建的远程仓库地址（.git结尾的）

  //添加本地仓库所有文件到提交列表中(注意add和点之间有个空格)
  git add .

  //提交并添加描述
  git commit -m '你的本次提交描述'

  //本地代码同步到远程仓库
  git push -u origin master

  上传完成后执行后续操作，如果出现问题，按照提示修改就行，基本上这一步不会有啥问题

4、修改编辑podspec文件，在终端输入命令``` open ./ ```，打开创建的项目文件，进入``` Example ```目录，打开后缀为``` .xcworkspace ```的工程。

  记住不要在这里执行``` pod install ```  ``` pod update ```
  记住不要在这里执行``` pod install ```  ``` pod update ```
  记住不要在这里执行``` pod install ```  ``` pod update ```

示例：<img src="/img/github_pods_01.png"  alt="Github创建公共库" />

开始编辑文件，具体文件字段的内容文件内都有注释

```
Pod::Spec.new do |s|
  s.name             = 'KGPhotoLibary' //私有库名称
  s.version          = '1.0.0' //私有库版本
  s.summary          = '自定义相册.' //私有库简短描述

  s.description      = "可以选择视频、图片或者两着共存" //私有库具体描述

  s.homepage         = 'https://github.com/KGDeveloper/KGPhotoLibary' //私有库首页地址
  s.license          = { :type => 'MIT', :file => 'LICENSE' }  //协议，默认MIT
  s.author           = { 'KGDeveloper' => 'myloveqzk@163.com' } //作者
  s.source           = { :git => 'https://github.com/KGDeveloper/KGPhotoLibary.git', :tag => s.version.to_s } //私有库url

  s.ios.deployment_target = '10.0' //私有库最低支持版本

  s.source_files = 'KGPhotoLibary/Classes/**/*' //私有库文件路径

  s.resource_bundles = {
     'KGPhotoLibary' => ['KGPhotoLibary/Assets/KGLibary.bundle']
   } //如果用到了资源文件，这里修改

  s.frameworks = 'UIKit' //如果使用了原生库，在这里添加
  s.dependency 'SVProgressHUD', '~> 2.2.5' //如果依赖了其他三方库，在这里添加
end

```
5、安装Pod依赖库

在这里有个很大的坑，如果忘记了，那么你会遇到很多报错，我都记不清改了多少，请注意以下操作，仔细看。

从项目根目录，进入``` Example ```文件下，打开``` Podfile ```文件，在里面有这么一句代码``` use_frameworks! ```，没错就是这个坑货，我们需要做的事情就是把它扼杀在萌芽中，直接删除或者``` # use_frameworks!  ```，熟悉的操作，没错就是注释这句。

然后执行命令，开始安装Pod依赖。

cd到当前项目目录下的``` Example ```文件下，执行``` pod install ``` 或 ``` pod update ```。

  两个命令的区别在于，pod install只是安装Podfile里面的依赖库，pod update会执行更新pod索引文件，然后安装Podfile文件里面的依赖库。

6、编写自己的代码文件

示例：<img src="/img/github_pods_02.png"  alt="编写自己代码" />

7、检查项目是否通过编译

  cd到项目更目录下，然后执行命令``` pod lib lint ```，如果出现``` ERROR ```字样，那就根据``` NOTE ```的提示去修改，以下罗列我所遇到的问题

```
➜ KGPhotoLibary git:(master) ✗ pod lib lint
-> KGPhotoLibary (1.0.0)
- WARN | url: The URL (https://github.com/myloveqzk@163.com/KGPhotoLibary) is not reachable. - ERROR | [iOS] xcodebuild: Returned an unsuccessful exit code. You can use `--verbose` for more
information.
- NOTE | xcodebuild: note: Using new build system
- NOTE | xcodebuild: note: Building targets in parallel
- NOTE | [iOS] xcodebuild: note: Planning build
- NOTE | [iOS] xcodebuild: note: Constructing build description
- NOTE | xcodebuild: error: Unexpected duplicate tasks:
- NOTE | [iOS] xcodebuild: warning: Skipping code signing because the target does not have an
Info.plist file and one is not being generated automatically. (in target 'App' from project 'App')
[!] KGPhotoLibary did not pass validation, due to 1 error and 1 warning. You can use the `--no-clean` option to inspect any issue.
```

出现这个错误的时候没有给出具体的信息，只是说项目运行报错了，所以我执行``` pod lib lint --verbose ```

```
➜ KGPhotoLibary git:(master) ✗ pod lib lint --verbose
CDN: trunk Relative path: CocoaPods-version.yml exists! Returning local because checking is only perfomed in repo update
KGPhotoLibary (1.0.0) - Analyzing on iOS 10.0 platform. Preparing
Analyzing dependencies
Inspecting targets to integrate
Using `ARCHS` setting to build architectures of target `Pods-App`: (``)
Fetching external sources
-> Fetching podspec for `KGPhotoLibary` from `/Users/bszh/KGPhotoLibary`
Resolving dependencies of
CDN: trunk Relative path: CocoaPods-version.yml exists! Returning local because checking is only
perfomed in repo update
CDN: trunk Relative path: all_pods_versions_b_e_8.txt exists! Returning local because checking is only
perfomed in repo update
CDN: trunk Relative path: Specs/b/e/8/SVProgressHUD/2.2.5/SVProgressHUD.podspec.json exists!
Returning local because checking is only perfomed in repo update
CDN: trunk Relative path: Specs/b/e/8/SVProgressHUD/2.2.5/SVProgressHUD.podspec.json exists!
Returning local because checking is only perfomed in repo update
Comparing resolved specification to the sandbox manifest A KGPhotoLibary
A SVProgressHUD
Downloading dependencies
-> Installing KGPhotoLibary (1.0.0)
-> Installing SVProgressHUD (2.2.5)
> Copying SVProgressHUD from `/Users/bszh/Library/Caches/CocoaPods/Pods/Release/
SVProgressHUD/2.2.5-1428a` to `../../../private/var/folders/np/kl73wv292blcg8h8zbxlhlfh0000gn/T/CocoaPods-
Lint-20200617-36516-1qg60wn-KGPhotoLibary/Pods/SVProgressHUD`
- Running pre install hooks
- Writing Lockfile in `../../../var/folders/np/kl73wv292blcg8h8zbxlhlfh0000gn/T/CocoaPods-
 Lint-20200617-36516-1qg60wn-KGPhotoLibary/Podfile.lock`
- Writing Manifest in `../../../private/var/folders/np/kl73wv292blcg8h8zbxlhlfh0000gn/T/CocoaPods-
Lint-20200617-36516-1qg60wn-KGPhotoLibary/Pods/Manifest.lock`
Generating Pods project
- Creating Pods project
- Installing files into Pods project
- Adding source files
- Adding frameworks
- Adding libraries
- Adding resources
- Adding development pod helper files - Linking headers
- Installing Pod Targets
- Installing target `KGPhotoLibary` iOS 10.0
- Generating module map file at `../../../private/var/folders/np/kl73wv292blcg8h8zbxlhlfh0000gn/T/ CocoaPods-Lint-20200617-36516-1qg60wn-KGPhotoLibary/Pods/Target Support
Files/KGPhotoLibary/KGPhotoLibary.modulemap`
- Generating umbrella header at `../../../private/var/folders/np/kl73wv292blcg8h8zbxlhlfh0000gn/T/ CocoaPods-Lint-20200617-36516-1qg60wn-KGPhotoLibary/Pods/Target Support
Files/KGPhotoLibary/KGPhotoLibary-umbrella.h`
- Generating Info.plist file at `../../../private/var/folders/np/kl73wv292blcg8h8zbxlhlfh0000gn/T/ CocoaPods-Lint-20200617-36516-1qg60wn-KGPhotoLibary/Pods/Target Support
Files/KGPhotoLibary/KGPhotoLibary-Info.plist`
- Generating dummy source at `../../../private/var/folders/np/kl73wv292blcg8h8zbxlhlfh0000gn/T/ CocoaPods-Lint-20200617-36516-1qg60wn-KGPhotoLibary/Pods/Target Support
Files/KGPhotoLibary/KGPhotoLibary-dummy.m` - Installing target `SVProgressHUD` iOS 8.0
- Generating module map file at `../../../private/var/folders/np/kl73wv292blcg8h8zbxlhlfh0000gn/T/ CocoaPods-Lint-20200617-36516-1qg60wn-KGPhotoLibary/Pods/Target Support
Files/SVProgressHUD/SVProgressHUD.modulemap`
- Generating umbrella header at `../../../private/var/folders/np/kl73wv292blcg8h8zbxlhlfh0000gn/T/ CocoaPods-Lint-20200617-36516-1qg60wn-KGPhotoLibary/Pods/Target Support
Files/SVProgressHUD/SVProgressHUD-umbrella.h`
- Generating Info.plist file at `../../../private/var/folders/np/kl73wv292blcg8h8zbxlhlfh0000gn/T/ CocoaPods-Lint-20200617-36516-1qg60wn-KGPhotoLibary/Pods/Target Support
Files/SVProgressHUD/SVProgressHUD-Info.plist`
- Generating dummy source at `../../../private/var/folders/np/kl73wv292blcg8h8zbxlhlfh0000gn/T/ CocoaPods-Lint-20200617-36516-1qg60wn-KGPhotoLibary/Pods/Target Support
Files/SVProgressHUD/SVProgressHUD-dummy.m` - Installing Aggregate Targets
- Installing target `Pods-App` iOS 10.0
- Generating Info.plist file at `../../../private/var/folders/np/kl73wv292blcg8h8zbxlhlfh0000gn/T/
CocoaPods-Lint-20200617-36516-1qg60wn-KGPhotoLibary/Pods/Target Support Files/Pods-App/Pods-App-Info.plist`
- Generating module map file at `../../../private/var/folders/np/kl73wv292blcg8h8zbxlhlfh0000gn/T/
CocoaPods-Lint-20200617-36516-1qg60wn-KGPhotoLibary/Pods/Target Support

 Files/Pods-App/Pods-App.modulemap`
- Generating umbrella header at `../../../private/var/folders/np/kl73wv292blcg8h8zbxlhlfh0000gn/T/ CocoaPods-Lint-20200617-36516-1qg60wn-KGPhotoLibary/Pods/Target Support
Files/Pods-App/Pods-App-umbrella.h`
- Generating dummy source at `../../../private/var/folders/np/kl73wv292blcg8h8zbxlhlfh0000gn/T/ CocoaPods-Lint-20200617-36516-1qg60wn-KGPhotoLibary/Pods/Target Support
Files/Pods-App/Pods-App-dummy.m`
- Stabilizing target UUIDs
- Running post install hooks
- Writing Xcode project file to `../../../private/var/folders/np/kl73wv292blcg8h8zbxlhlfh0000gn/T/
CocoaPods-Lint-20200617-36516-1qg60wn-KGPhotoLibary/Pods/Pods.xcodeproj` Cleaning up sandbox directory
Integrating client project
[!] Please close any current Xcode sessions and use `App.xcworkspace` for this project from now on.
Integrating target `Pods-App` (`../../../var/folders/np/kl73wv292blcg8h8zbxlhlfh0000gn/T/CocoaPods- Lint-20200617-36516-1qg60wn-KGPhotoLibary/App.xcodeproj` project)
Adding Build Phase '[CP] Embed Pods Frameworks' to project. Adding Build Phase '[CP] Check Pods Manifest.lock' to project.
-> Pod installation complete! There is 1 dependency from the Podfile and 2 total pods installed. Building with `xcodebuild`.
$ /usr/bin/xcodebuild clean build -workspace /var/folders/np/kl73wv292blcg8h8zbxlhlfh0000gn/T/ CocoaPods-Lint-20200617-36516-1qg60wn-KGPhotoLibary/App.xcworkspace -scheme App -configuration
Release CODE_SIGN_IDENTITY=- -sdk iphonesimulator -destination id=F651099B- B4BD-4F9B-8A74-291B966093FF
2020-06-17 15:35:34.439 xcodebuild[36527:891349] [MT] DVTPlugInManager: Required plug-in compatibility UUID C80A9C11-3902-4885-944E-A035869BA910 for KSImageNamed.ideplugin (com.ksuther.KSImageNamed) not present
2020-06-17 15:35:34.685 xcodebuild[36527:891349] [MT] PluginLoading: Required plug-in compatibility UUID C80A9C11-3902-4885-944E-A035869BA910 for plug-in at path '~/Library/Application Support/ Developer/Shared/Xcode/Plug-ins/XActivatePowerMode.xcplugin' not present in DVTPlugInCompatibilityUUIDs
2020-06-17 15:35:34.686 xcodebuild[36527:891349] [MT] PluginLoading: Required plug-in compatibility UUID C80A9C11-3902-4885-944E-A035869BA910 for plug-in at path '~/Library/Application Support/ Developer/Shared/Xcode/Plug-ins/VVDocumenter-Xcode.xcplugin' not present in DVTPlugInCompatibilityUUIDs
2020-06-17 15:35:34.687 xcodebuild[36527:891349] [MT] PluginLoading: Required plug-in compatibility UUID C80A9C11-3902-4885-944E-A035869BA910 for plug-in at path '~/Library/Application Support/ Developer/Shared/Xcode/Plug-ins/SCXcodeMinimap.xcplugin' not present in DVTPlugInCompatibilityUUIDs
2020-06-17 15:35:34.687 xcodebuild[36527:891349] [MT] PluginLoading: Required plug-in compatibility UUID C80A9C11-3902-4885-944E-A035869BA910 for plug-in at path '~/Library/Application Support/ Developer/Shared/Xcode/Plug-ins/FKConsole.xcplugin' not present in DVTPlugInCompatibilityUUIDs
2020-06-17 15:35:34.688 xcodebuild[36527:891349] [MT] PluginLoading: Required plug-in compatibility

 UUID C80A9C11-3902-4885-944E-A035869BA910 for plug-in at path '~/Library/Application Support/ Developer/Shared/Xcode/Plug-ins/ESJsonFormat.xcplugin' not present in DVTPlugInCompatibilityUUIDs
2020-06-17 15:35:34.688 xcodebuild[36527:891349] [MT] PluginLoading: Required plug-in compatibility UUID C80A9C11-3902-4885-944E-A035869BA910 for plug-in at path '~/Library/Application Support/ Developer/Shared/Xcode/Plug-ins/Auto-Importer.xcplugin' not present in DVTPlugInCompatibilityUUIDs
2020-06-17 15:35:34.689 xcodebuild[36527:891349] [MT] PluginLoading: Required plug-in compatibility UUID C80A9C11-3902-4885-944E-A035869BA910 for plug-in at path '~/Library/Application Support/ Developer/Shared/Xcode/Plug-ins/Alcatraz.xcplugin' not present in DVTPlugInCompatibilityUUIDs
2020-06-17 15:35:34.690 xcodebuild[36527:891349] [MT] PluginLoading: Required plug-in compatibility UUID C80A9C11-3902-4885-944E-A035869BA910 for plug-in at path '~/Library/Application Support/ Developer/Shared/Xcode/Plug-ins/ActivatePowerMode.xcplugin' not present in DVTPlugInCompatibilityUUIDs
Command line invocation:
/Applications/Xcode.app/Contents/Developer/usr/bin/xcodebuild clean build -workspace /var/
folders/np/kl73wv292blcg8h8zbxlhlfh0000gn/T/CocoaPods-Lint-20200617-36516-1qg60wn- KGPhotoLibary/App.xcworkspace -scheme App -configuration Release CODE_SIGN_IDENTITY=- -sdk iphonesimulator -destination id=F651099B-B4BD-4F9B-8A74-291B966093FF
Build settings from command line: CODE_SIGN_IDENTITY = - SDKROOT = iphonesimulator13.5
note: Using new build system note: Building targets in parallel
** CLEAN SUCCEEDED **
note: Using new build system
note: Building targets in parallel
note: Planning build
note: Constructing build description
Build system information
error: Unexpected duplicate tasks:
1) Target 'KGPhotoLibary' (project 'Pods') has copy command from '/Users/bszh/KGPhotoLibary/
KGPhotoLibary/Classes/KGLibary.bundle/播放@3x.png' to '/Users/bszh/Library/Developer/Xcode/ DerivedData/App-awelneilcjqehtabzuwdsoykoavd/Build/Products/Release-iphonesimulator/ KGPhotoLibary/KGPhotoLibary.framework/播放@3x.png'
2) Target 'KGPhotoLibary' (project 'Pods') has copy command from '/Users/bszh/KGPhotoLibary/ KGPhotoLibary/Classes/KGLibary.bundle/播放@3x.png' to '/Users/bszh/Library/Developer/Xcode/ DerivedData/App-awelneilcjqehtabzuwdsoykoavd/Build/Products/Release-iphonesimulator/ KGPhotoLibary/KGPhotoLibary.framework/播放@3x.png'
Build system information
error: Unexpected duplicate tasks:
1) Target 'KGPhotoLibary' (project 'Pods') has copy command from '/Users/bszh/KGPhotoLibary/

 KGPhotoLibary/Classes/KGLibary.bundle/撤销.png' to '/Users/bszh/Library/Developer/Xcode/ DerivedData/App-awelneilcjqehtabzuwdsoykoavd/Build/Products/Release-iphonesimulator/ KGPhotoLibary/KGPhotoLibary.framework/撤销.png'
2) Target 'KGPhotoLibary' (project 'Pods') has copy command from '/Users/bszh/KGPhotoLibary/ KGPhotoLibary/Classes/KGLibary.bundle/撤销.png' to '/Users/bszh/Library/Developer/Xcode/ DerivedData/App-awelneilcjqehtabzuwdsoykoavd/Build/Products/Release-iphonesimulator/ KGPhotoLibary/KGPhotoLibary.framework/撤销.png'
Build system information
error: Unexpected duplicate tasks:
1) Target 'KGPhotoLibary' (project 'Pods') has copy command from '/Users/bszh/KGPhotoLibary/
KGPhotoLibary/Classes/KGLibary.bundle/对号.png' to '/Users/bszh/Library/Developer/Xcode/ DerivedData/App-awelneilcjqehtabzuwdsoykoavd/Build/Products/Release-iphonesimulator/ KGPhotoLibary/KGPhotoLibary.framework/对号.png'
2) Target 'KGPhotoLibary' (project 'Pods') has copy command from '/Users/bszh/KGPhotoLibary/ KGPhotoLibary/Classes/KGLibary.bundle/对号.png' to '/Users/bszh/Library/Developer/Xcode/ DerivedData/App-awelneilcjqehtabzuwdsoykoavd/Build/Products/Release-iphonesimulator/ KGPhotoLibary/KGPhotoLibary.framework/对号.png'
Build system information
error: Unexpected duplicate tasks:
1) Target 'KGPhotoLibary' (project 'Pods') has copy command from '/Users/bszh/KGPhotoLibary/
KGPhotoLibary/Classes/KGLibary.bundle/旋转.png' to '/Users/bszh/Library/Developer/Xcode/ DerivedData/App-awelneilcjqehtabzuwdsoykoavd/Build/Products/Release-iphonesimulator/ KGPhotoLibary/KGPhotoLibary.framework/旋转.png'
2) Target 'KGPhotoLibary' (project 'Pods') has copy command from '/Users/bszh/KGPhotoLibary/ KGPhotoLibary/Classes/KGLibary.bundle/旋转.png' to '/Users/bszh/Library/Developer/Xcode/ DerivedData/App-awelneilcjqehtabzuwdsoykoavd/Build/Products/Release-iphonesimulator/ KGPhotoLibary/KGPhotoLibary.framework/旋转.png'
Build system information
error: Unexpected duplicate tasks:
1) Target 'KGPhotoLibary' (project 'Pods') has copy command from '/Users/bszh/KGPhotoLibary/
KGPhotoLibary/Classes/KGLibary.bundle/对号_blod.png' to '/Users/bszh/Library/Developer/Xcode/ DerivedData/App-awelneilcjqehtabzuwdsoykoavd/Build/Products/Release-iphonesimulator/ KGPhotoLibary/KGPhotoLibary.framework/对号_blod.png'
2) Target 'KGPhotoLibary' (project 'Pods') has copy command from '/Users/bszh/KGPhotoLibary/ KGPhotoLibary/Classes/KGLibary.bundle/对号_blod.png' to '/Users/bszh/Library/Developer/Xcode/ DerivedData/App-awelneilcjqehtabzuwdsoykoavd/Build/Products/Release-iphonesimulator/ KGPhotoLibary/KGPhotoLibary.framework/对号_blod.png'
Build system information
error: Unexpected duplicate tasks:
1) Target 'KGPhotoLibary' (project 'Pods') has copy command from '/Users/bszh/KGPhotoLibary/
KGPhotoLibary/Classes/KGLibary.bundle/图层.png' to '/Users/bszh/Library/Developer/Xcode/ DerivedData/App-awelneilcjqehtabzuwdsoykoavd/Build/Products/Release-iphonesimulator/ KGPhotoLibary/KGPhotoLibary.framework/图层.png'

 2) Target 'KGPhotoLibary' (project 'Pods') has copy command from '/Users/bszh/KGPhotoLibary/ KGPhotoLibary/Classes/KGLibary.bundle/图层.png' to '/Users/bszh/Library/Developer/Xcode/ DerivedData/App-awelneilcjqehtabzuwdsoykoavd/Build/Products/Release-iphonesimulator/ KGPhotoLibary/KGPhotoLibary.framework/图层.png'
Build system information
error: Unexpected duplicate tasks:
1) Target 'KGPhotoLibary' (project 'Pods') has copy command from '/Users/bszh/KGPhotoLibary/
KGPhotoLibary/Classes/KGLibary.bundle/缩放.png' to '/Users/bszh/Library/Developer/Xcode/ DerivedData/App-awelneilcjqehtabzuwdsoykoavd/Build/Products/Release-iphonesimulator/ KGPhotoLibary/KGPhotoLibary.framework/缩放.png'
2) Target 'KGPhotoLibary' (project 'Pods') has copy command from '/Users/bszh/KGPhotoLibary/ KGPhotoLibary/Classes/KGLibary.bundle/缩放.png' to '/Users/bszh/Library/Developer/Xcode/ DerivedData/App-awelneilcjqehtabzuwdsoykoavd/Build/Products/Release-iphonesimulator/ KGPhotoLibary/KGPhotoLibary.framework/缩放.png'
Build system information
error: Unexpected duplicate tasks:
1) Target 'KGPhotoLibary' (project 'Pods') has copy command from '/Users/bszh/KGPhotoLibary/
KGPhotoLibary/Classes/KGLibary.bundle/播放@2x.png' to '/Users/bszh/Library/Developer/Xcode/ DerivedData/App-awelneilcjqehtabzuwdsoykoavd/Build/Products/Release-iphonesimulator/ KGPhotoLibary/KGPhotoLibary.framework/播放@2x.png'
2) Target 'KGPhotoLibary' (project 'Pods') has copy command from '/Users/bszh/KGPhotoLibary/ KGPhotoLibary/Classes/KGLibary.bundle/播放@2x.png' to '/Users/bszh/Library/Developer/Xcode/ DerivedData/App-awelneilcjqehtabzuwdsoykoavd/Build/Products/Release-iphonesimulator/ KGPhotoLibary/KGPhotoLibary.framework/播放@2x.png'
Build system information
error: Unexpected duplicate tasks:
1) Target 'KGPhotoLibary' (project 'Pods') has copy command from '/Users/bszh/KGPhotoLibary/
KGPhotoLibary/Classes/KGLibary.bundle/形状.png' to '/Users/bszh/Library/Developer/Xcode/ DerivedData/App-awelneilcjqehtabzuwdsoykoavd/Build/Products/Release-iphonesimulator/ KGPhotoLibary/KGPhotoLibary.framework/形状.png'
2) Target 'KGPhotoLibary' (project 'Pods') has copy command from '/Users/bszh/KGPhotoLibary/ KGPhotoLibary/Classes/KGLibary.bundle/形状.png' to '/Users/bszh/Library/Developer/Xcode/ DerivedData/App-awelneilcjqehtabzuwdsoykoavd/Build/Products/Release-iphonesimulator/ KGPhotoLibary/KGPhotoLibary.framework/形状.png'
Build system information
error: Unexpected duplicate tasks:
1) Target 'KGPhotoLibary' (project 'Pods') has copy command from '/Users/bszh/KGPhotoLibary/
KGPhotoLibary/Classes/KGLibary.bundle/播放@1x.png' to '/Users/bszh/Library/Developer/Xcode/ DerivedData/App-awelneilcjqehtabzuwdsoykoavd/Build/Products/Release-iphonesimulator/ KGPhotoLibary/KGPhotoLibary.framework/播放@1x.png'
2) Target 'KGPhotoLibary' (project 'Pods') has copy command from '/Users/bszh/KGPhotoLibary/ KGPhotoLibary/Classes/KGLibary.bundle/播放@1x.png' to '/Users/bszh/Library/Developer/Xcode/ DerivedData/App-awelneilcjqehtabzuwdsoykoavd/Build/Products/Release-iphonesimulator/

 KGPhotoLibary/KGPhotoLibary.framework/播放@1x.png'
Build system information
error: Unexpected duplicate tasks:
1) Target 'KGPhotoLibary' (project 'Pods') has copy command from '/Users/bszh/KGPhotoLibary/
KGPhotoLibary/Classes/KGLibary.bundle/back.png' to '/Users/bszh/Library/Developer/Xcode/ DerivedData/App-awelneilcjqehtabzuwdsoykoavd/Build/Products/Release-iphonesimulator/ KGPhotoLibary/KGPhotoLibary.framework/back.png'
2) Target 'KGPhotoLibary' (project 'Pods') has copy command from '/Users/bszh/KGPhotoLibary/ KGPhotoLibary/Classes/KGLibary.bundle/back.png' to '/Users/bszh/Library/Developer/Xcode/ DerivedData/App-awelneilcjqehtabzuwdsoykoavd/Build/Products/Release-iphonesimulator/ KGPhotoLibary/KGPhotoLibary.framework/back.png'
Build system information
error: Unexpected duplicate tasks:
1) Target 'KGPhotoLibary' (project 'Pods') has copy command from '/Users/bszh/KGPhotoLibary/
KGPhotoLibary/Classes/KGLibary.bundle/形状@2x.png' to '/Users/bszh/Library/Developer/Xcode/ DerivedData/App-awelneilcjqehtabzuwdsoykoavd/Build/Products/Release-iphonesimulator/ KGPhotoLibary/KGPhotoLibary.framework/形状@2x.png'
2) Target 'KGPhotoLibary' (project 'Pods') has copy command from '/Users/bszh/KGPhotoLibary/ KGPhotoLibary/Classes/KGLibary.bundle/形状@2x.png' to '/Users/bszh/Library/Developer/Xcode/ DerivedData/App-awelneilcjqehtabzuwdsoykoavd/Build/Products/Release-iphonesimulator/ KGPhotoLibary/KGPhotoLibary.framework/形状@2x.png'
warning: Skipping code signing because the target does not have an Info.plist file and one is not being generated automatically. (in target 'App' from project 'App')
** BUILD FAILED **
Testing with `xcodebuild`. -> KGPhotoLibary (1.0.0)
- ERROR | [iOS] xcodebuild: Returned an unsuccessful exit code. - NOTE | xcodebuild: note: Using new build system
- NOTE | xcodebuild: note: Building targets in parallel
- NOTE | [iOS] xcodebuild: note: Planning build
- NOTE | [iOS] xcodebuild: note: Constructing build description
- NOTE | xcodebuild: error: Unexpected duplicate tasks:
- NOTE | [iOS] xcodebuild: warning: Skipping code signing because the target does not have an
Info.plist file and one is not being generated automatically. (in target 'App' from project 'App')
[!] KGPhotoLibary did not pass validation, due to 1 error. You can use the `--no-clean` option to inspect any issue.
/Users/bszh/.rvm/rubies/ruby-2.5.3/lib/ruby/gems/2.5.0/gems/cocoapods-1.9.1/lib/cocoapods/ command/lib/lint.rb:104:in `block in run' /Users/bszh/.rvm/rubies/ruby-2.5.3/lib/ruby/gems/2.5.0/gems/cocoapods-1.9.1/lib/cocoapods/ command/lib/lint.rb:69:in `each' /Users/bszh/.rvm/rubies/ruby-2.5.3/lib/ruby/gems/2.5.0/gems/cocoapods-1.9.1/lib/cocoapods/

command/lib/lint.rb:69:in `run' /Users/bszh/.rvm/rubies/ruby-2.5.3/lib/ruby/gems/2.5.0/gems/claide-1.0.2/lib/claide/ command.rb:334:in `run' /Users/bszh/.rvm/rubies/ruby-2.5.3/lib/ruby/gems/2.5.0/gems/cocoapods-1.9.1/lib/cocoapods/ command.rb:52:in `run' /Users/bszh/.rvm/gems/ruby-2.5.3@global/gems/cocoapods-1.9.1/bin/pod:55:in `<top (required)>' /usr/local/bin/pod:23:in `load'
/usr/local/bin/pod:23:in `<main>' /Users/bszh/.rvm/gems/ruby-2.5.3/bin/ruby_executable_hooks:24:in `eval' /Users/bszh/.rvm/gems/ruby-2.5.3/bin/ruby_executable_hooks:24:in `<main>'
```

这个错误是因为我的资源文件.bundle加载不正确导致。修改```KGPhotoLibary.podspec```文件中的```s.resource_bundles```路径，可以仿照我的代码路径修改。

第二个错误提示我```SVProgressHUD```相关的东西，那是因为我依赖了```SVProgressHUD```，然后在我的库中引入的方式不对，在自己库中引入不能使用```#import <SVProgressHUD.h>```了，修改为```#impoer "SVProgressHUD.h"```。

然后一直运行``` pod lib lint --verbose ```，直到没有```ERROR```后，执行后续步骤。

8、提交代码到Git

  git add . //不做解释了，之前已经写了注释

  git commit -m '首版提交'

9、重点来了，因为Cocoapods是依赖tag版本的，所以必须打tag，后续升级自己三方库也有用。

  git tag 1.0.0

  git push tag

在这里说点扩展的，git命令
  #查看tag版本列表
  git tag

  #删除tag
  git tag -d 1.0.0

10、验证KGPhotoLibary.podspec文件

  // --verbose 如果验证失败会报错误信息
  pod spec lint KGPhotoLibary.podspec --verbose

11、发布

  pod trunk push KGPhotoLibary.podspec

12、到此完成Cocoapods自己三方库创建了，如果有问题请联系QQ：969840184，或者简书留言