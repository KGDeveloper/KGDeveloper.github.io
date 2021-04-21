---
layout:     post
title:      Linux系统下打包uni-app
subtitle:   Linux系统下打包uni-app
date:       2021-04-20
author:     KG丿夏沫
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Linux
    - uni-app
---

# Linux系统下打包uni-app

> 连接Linux服务器

```
ssh root@192.168.x.x
```

> 输入登密码

> 新建文件夹，并进入文件夹

```
mkdir weekly & cd weekly
```

> 初始化git仓库

```
git init
```

> 添加ssh-key

```
cd ~/.ssh & ssh-keygen -t rsa -C "qizk@bszhihui.com"
```

> 获取刚新建的ssh-key，并添加到github/gitlib的ssh-key中

```
cat id_rsa_qizk.pub
```

> 添加完成后拉去代码

```
git clone git@192.168.0.10:web-design/uni-app_weekly.git --branch dev
```

> 完成后检查node以及npm版本

```
node -v 
npm -v
```

> 如果版本与开发环境不一致，升级版本，cd进入到```~/usr/local/src```下，删除之前的```node-v15.11.0-linux-x64```和```node-v15.11.0-linux-x64.tar.gz```，然后执行以下命令：

```
wget https://nodejs.org/dist/v15.11.0/node-v15.11.0-linux-x64.tar.gz
```

> 命令中的地址进入```https://nodejs.org/dist/```选择相应的版本，点击进入文件夹，选择相应平台下的压缩包，右键复制连接```https://nodejs.org/dist/v15.11.0/node-v15.11.0-linux-x64.tar.gz```

> 然后进行解压

```
tar -xvf node-v15.11.0-linux-x64.tar.gz
```

> 然后运行以下命令查看node全局配置路径:

```
npm root -g
```

> 找到路径后进入路径下，删除对应的```npm```和```node```文件
> 
> 然后执行连接命令：

```
ln -s ~/usr/local/src/node-v15.11.0-linux-x64/bin/node /usr/bin/node
ln -s ~/usr/local/src/node-v15.11.0-linux-x64/bin/npm /usr/bin/npm
```

> 然后进入到项目根目录下，执行打包命令

```
npm run build:h5
```