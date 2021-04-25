---
layout:     post
title:      Mac下node和npm管理
subtitle:   Mac下node和npm管理
date:       2021-04-25
author:     KG丿夏沫
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Mac
    - node
    - npm
    - 版本管理
    - 版本升级
---

# Mac下node和npm管理

在Mac环境下配置node和npm有两种管理方式，都是依赖其他管理工具来管理安装、卸载、升级、降级等操作，目前来说主要用的有两种```nvm```和```n```，下面分别介绍下两种工具的使用方式以及安装

### nvm

nvm是目前来说在Mac以及Windows中使用最广泛的一种，他的安装以及使用也比较简单，下面依次介绍下它的安装、卸载以及node控制

1、nvm安装:

```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash
```

注：后面0.33.8是nvm的版本号，然后后面bash是环境控制脚本，目前来说macOS10以前基本都是```bash```，但是从10以后使用的是```bszh_profile```，目前M1中默认使用的是```zsh```。

2、node控制:

> node安装
>
> ```
> nvm install <version>
> ```
> 
> node卸载
> 
> ```
> nvm uninstall <version>
> ```
> 
> node版本切换
> 
> ```
> nvm use <version>
> ```
> 
> 指定运行node版本
> 
> ```
> nvm run <version> <target>
> ```
> 
> 查看node的LTS版本
> 
> ```
> nvm ls -remote 
> ```

### n

n在mac中也是控制node版本的

1、n安装

```
sudo npm install -g n 
```

2、node控制

> node安装
> 
> ```
> n <version>
> ```
> 
> node卸载
> 
> ```
> n rm <version>
> ```
> 
> node版本切换
> 
> ```
> n
> ```
> 
> 注：输入指令，出现node已安装版本列表后，通过上下键切换node版本，选择想要切换的目标版本后直接回车
> 
> 查看node的LTS版本
> 
> ```
> n lts
> ```