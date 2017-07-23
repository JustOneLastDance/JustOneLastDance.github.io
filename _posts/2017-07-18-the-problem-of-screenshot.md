---
layout: post
title: 开发中遇到的截图问题
date: 2017-07-18 16:17:17 +0800
categories: 技术
tags: iOS
keywords: 截图
description: 遇到的问题
---

## 问题出现描述

自己做了个小程序，想实现一键截图分享到微信朋友圈的功能。
当所有配置、授权、代码全部搞定之后，接下来发生的事情让我一脸懵逼。

![截图一](http://wx3.sinaimg.cn/small/a73cca61gy1fho4n3eycpj208c09mmxy.jpg)

图片说明：
在上图中可以发现一个很难看的透明深灰色矩形。
实际上是这个样子的，两个粉色矩形中夹着一个 `date pickerview`，在截图的时候 `date pickerview` 里莫名其妙多出了这个深灰色的矩形。
正确的截图效果应该没有这个难看的矩形。

实现截图功能代码如下：
```
UIGraphicsBeginImageContextWithOptions(self.view.frame.size, false, UIScreen.main.scale)
        view.layer.render(in: UIGraphicsGetCurrentContext()!)
        let image = UIGraphicsGetImageFromCurrentImageContext()!
        UIGraphicsEndImageContext()
```
上述代码是从网上找来直接用的，可是并没有达到预期的效果。

## 开始解决问题

最后问题定位到了这行代码
```
view.layer.render(in: UIGraphicsGetCurrentContext()!)
```

在官方文档中是这样描述的：
>Renders the layer and its sublayers into the specified context.

意思是将这个图层和他的子图层渲染到特定的图形上下文中去。
请注意这两个名词  **图层**、**子图层**。

从哪出现问题就从哪儿开始解决问题。既然是网上找到的错误代码，那正确的代码肯定也藏在网络中，最后在最大的“同性交友网站” `stackoverflow` 上找到了解决的方法。

解决方法很简单简单，将错误代码替换成如下代码即可解决：
```
view.drawHierarchy(in: view.bounds, afterScreenUpdates: true)
```

官方文档中是这样描述的：
>Renders a snapshot of the complete view hierarchy as visible onscreen into the current context.

大概意思是将一个显示在屏幕上，具有完整视图结构的截图渲染到图形上下文中。

## 总结
个人认为，这个问题出现的可能原因是由于各个图层之间的渲染方式不同和对于 `view hierarchy` 的不理解而导致。
如果你点击 `debug view hierarchy` 会发现，`date picker` 的图层不是平面的，而是立体的。我的理解是，正因为是这样的设计，才使得 `date picker` 在滑动的时候，可以有类似拨动旅行箱密码盘一样的效果。

**为何 render 会出现这样的问题？**
个人的猜测是因为 `date picker` 的图层颜色是淡灰色且透明的，其中又有多个类似的子图层层叠在了一起，最终导致当进行渲染的时候，出现了一个深灰色且透明的矩形。可能不依靠图层结构，仅仅是简单的将图层叠在了一起罢了。
而 `drawHierarchy` 是根据 `view hierarchy` 来进行渲染，每个图层之间具有一定的"距离"，截图之后显示的就是实际屏幕上看到的效果。








## THE END







