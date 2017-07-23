---
layout: post
title: Core Foundation & Foundation 的区别
date: 2017-07-18 21:55:27 +0800
categories: 技术
tags: iOS
keywords: toll-free bridging
description: 框架
---

`Core Foundation` 是一组 C 语言的接口，为 iOS 提供最基本的数据管理和服务功能。
`Core Foundation` 比 `Foundation` 出现的更早，`Core Foundation` 在一定程度上是为`Foundation` 提供支持的。
`Cocoa Touch` 是苹果电脑公司的用户界面软件框架，用于在iOS操作系统上开发应用软件来运行在iPhone, iPad和iPod Touch上。Cocoa Touch提供了iOS操作系统的抽象层。Cocoa Touch 基于 Mac OS X Cocoa API，主要用Objective-C程序语言写成。在 `Cocoa Touch` 中最重要的两个框架分别是 `Foundation` 和 `UIKit`。
`Cocoa Touch` 是以 OC 语言为基础的，因此 `Foundation` 是一组 OC 语言的接口。
可以说 `Foundation` 是对于 `Core Foundation` 更高层次的封装。

两个框架中存在许多类似的类型，名字相同，只是前缀不同而已。


| Core Foundation | Foundation |  
| --------------- | ---------- |  
| CFString        | NSString   |  
| CFArray         | NSArray    |  
| CFDictionary    | NSDictionary |  


`CF` 取自于 `Core Foundation` 的首字母
`NS` 取自于乔布斯回归苹果之前创建的公司名 “NEXT STEP”

但是在使用 `Core Foundation` 一些类型的时候，发现无法直接使用上表中的类型，因为类型名称被起了一个别名

```
typedef const struct CF_BRIDGED_TYPE(NSString) __CFString * CFStringRef;
```

在某些情况下，需要同时使用到某些 `Core Foundation` 以及 `Foundation` 中的相同数据类型，但是无法对不同的类型进行操作，那此时该怎么办？  

> Toll-Free-Bridging  

在 MRC 下，不做过多展开，因为 MRC 过于久远且操作简单，直接对类型进行强制转换即可。

```
NSString *nsStr = (NSString *)cfStr;
```

但是在 ARC 下，情况就比较复杂。因为 ARC 只能管理 OC 对象的内存分配，即 Foundation 下的类型，而无法对 `Core Foundation` 的对象进行管理，因此需要手动设置。

在 ARC 下，进行 `Toll-Free-Briding` 有3种修饰符分别是：
__bridge, __bridge_retained, __bridge_transfer

**__bridge**  

使用此修饰符是要告诉编译器不用转移内存管理权，但是进行 bridge 之后，编译器仍然负责 OC 对象的引用计数，而 CF 的对象需要手动管理。当 OC 转 CF 的时候，运行可能会出现崩溃。

```
OC -> CF
CFStringRef cfStr = (__bridge CFStringRef)nsStr;

CF -> OC
NSString *nsStr = (__bridge NSString *)cfStr;
```

**__bridge_retained**  

使用这个可以避免运行时可能出现的崩溃问题。本质上是在 bridging 时 编译器对 C 对象进行了 `retain` 操作。但是释放 C 对象的时候需要手动释放。

**这仅仅针对当 OC 转 C 的时候。**  


```
OC -> CF
CFStringRef cfStr = (__bridge_retained CFStringRef)nsStr;
```  

**__bridge_transfer**  

当 CF 转 OC的时候，使用这个可以不用手动去释放 C 对象

```
CF -> OC
NSString *nsStr = (__bridge_transfer NSString *)cfStr;
```  

## 总结  

| __bridge          | bridging时，不会对对象做任何操作 |
| ----------------- | ---------------------------- |  
| __bridge_retained | bridging时，当 OC 转 CF 时，会对 CF 对象进行 retain 操作|  
| __bridge_transfer | bridging时，当 CF 转 OC 时，会对 CF 对象进行 release 操作|  

一切的一切，都是由于在 ARC 模式下使用了 CF 对象，ARC 无法对 CF 对象进行内存管理导致的。

## 问题  

**那在 Swift 中是否也存在这样的问题？**  

在 Swift 中对于 `Core Foundation` (以及其他一系列 Core 开头的框架) 在内存管理进行了一系列简化，大大降低了与这些 `Core Foundation` API 打交道的复杂程度。
同时 CF 类型名称不再是 CFXXXRef, 改成了 CFXXX。
在 Swift 中可以直接使用 CF 对象，不再需要 bridging。因为 CF 对象已经纳入了 ARC 中，可以对其进行内存管理。本质上来说，编译器会在恰当的地方添加 `CF_RETURNS_RETAINED` 和 `CF_RETURNS_NOT_RETAINED` 这样的标注。

当然了还有特例，就是自己的编写或者第三方的 CF API（非系统内部的）。导致的原因是因为可能没有添加上述的2个标记，并没有指明是以哪种方式进行内存管理。











