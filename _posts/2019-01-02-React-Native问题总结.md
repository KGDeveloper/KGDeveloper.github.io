---
layout:     post
title:      React-Native问题总结
subtitle:   React-Native问题总结
date:       2019-01-02
author:     KG丿夏沫
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - iOS
    - React-Native
    - React-Native问题总结
---

### 项目中所有图片资源不显示

>1、路径react-native/Libraries/Image/RCTUIImageViewAnimated.m下，修改源码为如下代码

```
#pragma mark - CALayerDelegate

- (void)displayLayer:(CALayer *)layer
{
  if (_currentFrame) {
    layer.contentsScale = self.animatedImageScale;
    layer.contents = (__bridge id)_currentFrame.CGImage;
  }else{
      [super displayLayer:layer];
  }
}
```

>2、

```
events.js:292
      throw er; // Unhandled 'error' event
      ^

Error: EMFILE: too many open files, watch
    at FSEvent.FSWatcher._handle.onchange (internal/fs/watchers.js:178:28)
Emitted 'error' event on NodeWatcher instance at:
    at NodeWatcher.checkedEmitError (/Users/bszh/Desktop/RN_HJ/node_modules/sane/src/node_watcher.js:143:12)
    at FSWatcher.emit (events.js:315:20)
    at FSEvent.FSWatcher._handle.onchange (internal/fs/watchers.js:184:12) {
  errno: -24,
  syscall: 'watch',
  code: 'EMFILE',
  filename: null
}
```

