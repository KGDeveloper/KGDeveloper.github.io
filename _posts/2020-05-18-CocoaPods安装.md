---
layout:     post
title:      CocoaPods
subtitle:   新版pod安装指南
date:       2020-05-18
author:     KG丿夏沫
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - iOS
    - CococPods
    - pod安装
---

# CocoaPods最新安装教程

## 前言：

    公司项目用的React-Native，之前一直用的低版本开发，5月份开始苹果不再接受UIWebview新应用，12月底不再接受UIWebview旧应用，所以打算将公司项目进行升级，React-native版本0.60.0开始UIWebview被替换成WKWebview，于是开启了我的艰辛升级路。
    
## 准备工作

### 之前有安装

    1、因为我的是Mac mini机，之前有安装过Cocoapod，然后我直接进行的更新，但是遇到报错，连接被关闭，也百度、谷歌查看了很多资料，没有得到好的解决办法，所以最终决定重新安装
    
    2、首先查看本地pod路径
    ```
    which pod
    ```
    如果vim中输出一下内容，说明你的之前安装过
    ```
    ➜  ~ which pod
    /usr/local/bin/pod
    ➜  ~ 
    ```

    3、然后查看pod本地安装目录

    ```
    gem list --local | grep cocoapods
    ```

    vim中会输出一下内容，只是类似，不会完全一致，因为我这是安装成功后整理的笔记，所以是最新版本信息
    
    ```
    ➜  ~ gem list --local | grep cocoapods
    cocoapods-core (1.9.1)
    cocoapods-deintegrate (1.0.4, 1.0.2)
    cocoapods-downloader (1.2.2)
    cocoapods-plugins (1.0.0)
    cocoapods-search (1.0.0)
    cocoapods-stats (1.0.0)
    cocoapods-trunk (1.4.1, 1.3.1)
    cocoapods-try (1.1.0)
    ```

    4、如果vim中显示以上信息，那么我们可以继续一下步骤，运行命令

    ```
    sudo gem uninstall cocoapods-core
    ```
    vim输出：
    ```
    ➜  ~ sudo gem uninstall cocoapods-core
    Successfully uninstalled cocoapods-core-1.9.1
    ```
    重复步骤4直到所有卸载完成，中间类似这种的库```cocoapods-deintegrate (1.0.4, 1.0.2)```会提示一下信息

    ```
    ➜  ~ sudo gem uninstall cocoapods-deintegrate

    Select gem to uninstall:
        1. cocoapods-deintegrate-1.0.2
        2. cocoapods-deintegrate-1.0.4
        3. All versions
    > 3
    Successfully uninstalled cocoapods-deintegrate-1.0.2
    Successfully uninstalled cocoapods-deintegrate-1.0.4
    ```
    选择所有版本回车就行，直到所有库卸载完成后，运行```gem list --local | grep cocoapods```,查看是否还有预留，如果全部移除干净了，继续一下步骤

    5、运行cocoapods安装命令

    ```
    sudo gem install -n /usr/local/bin cocoapods
    ```

    等待安装完成后，运行以下命令

    ```
    pod repo list
    ```

    vim如果输入以下情况:

    ```
    ➜  ~ pod repo list

    0 repos
    ```

    继续运行设置命令

    ```
    pod setup
    ```
    vim输出一下内容:
    ```
    ➜  ~ pod setup
    Setup completed
    ```
    然后cd到pod目录，进行远程仓库拉去
    ```
    cd ~/.cocoapods/repos
    ```

    ```
    git clone --depth 1 git://github.com/CocoaPods/Specs.git master
    ```
    其中```--depth 1```为1代表拉去最新库，0是拉去所有版本，推荐使用1，所有版本的话时间很长差不多要下载1G内容，而用1的话只需要大概150M就可以，https速度比较慢，推荐使用git:代替

    到此差不多就结束了，等待master分支拉去完成，就可以到自己项目中使用pod了

### 首次安装

    1、如果是之前没有安装过pod，按照以下步骤进行：

    ```
    推荐一篇，懒的自己写，毕竟人家写的也很详细了[https://www.jianshu.com/p/6d51362b7e64]
    ```

    2、使用步骤推荐文章当中也有，如果无法访问，请联系我，到时候补充上去。

### 结束语

    只是用作笔记，如果描述不当或者错误，请及时联系

