---
layout: post
title: Objective-C Class & Object 是什么
date: 2017-08-27 20:53:39 +0800
categories: 技术
tags: iOS
keywords: 底层结构
description: 知识整理
---

`Objective-C` 是基于 C 发展而来的，在 C 的基础上添加了面向对象的概念。
在 `Objective-C` 的程序中，可以调用 C 的 API。如果将 .m 文件的后缀改成 .mm，程序内还能混用 `C++` 的语法，那就变成了 `Objective-C++`。  
  
`Objective-C` 程序在编译的时候，编译器会首先将程序编程 C 语言然后再接着编译。所有 OC 的 `class` 会变成 C 的 `structure`(结构体) ，所有的 `method`(包括block)会变成 C 的 `function`。接着，在执行的时候，`runtime` 会建立起 `function`(method) 和 `structure`(class) 之间对应的关系。**总的来说，一个对象到底有多少个方法，是在 runtime 决定的**。

**为 Class 添加 Method**  
在程序执行的时候，runtime 会为每个 `Class` 准备一个表格（virtual table），表格内会用字符串作为 key，每个 key 会对应到 C 的 `function` 的指针位置。  
在 runtime 里，C 的 `function` 会被定义为 `IMP`（函数指针）。而用来当作 key 的字符串其实就是 `selector`，会被重新定义成 `SEL`。可以用 `@selector` 来使用 `selector`，实际上 `SEL` 就是一个字符串。当对象调用方法的时候，runtime 做的事情就是把方法的方法名当作字符串，查找与之相匹配的 C 的 `function`，然后再执行。

**函数的调用**  

```
[self xmt_testMethod];
[self performSelector:@selector(xmt_testMethod)];
objc_msgSend(self, @selector(xmt_testMethod));
```

上述是3个函数调用的方式，他们是等价的，其执行结果都是一样的，调用 `xmt_testMethod` 这个方法。**需要注意的是** `objc_msgSend` 需要对程序进行处理，首先需要导入 `<objc/message.h>`，在项目下的 `Build Setting` 下的 `Enable Strict Checking of objc_msgSend Calls` 修改成 NO，这样之后才能使用。这3个函数的意思是让某个对象调用某个方法，可以换一种方式来讲，叫做对一个 `receiver` 发送一个消息，这是源自于 `Small Talk`，`Objective-C` 实际上是借鉴了 `Small Talk`，这个方式也可以称之为消息机制。

**需要注意的是**  
在 `Objective-C` 中，一个 `class` 会有哪些方法是不固定的，如果一个对象调用了某个不存在的方法，编译器并不会报错，仅仅是发出警告而已，只有当实际执行的时候，才会发生 `unrecognized selector sent to instance` 意思是这个对象没有这个方法能调用，从而导致了程序的崩溃。

**为什么只有警告，而不是编译错误**  
由于 `runtime` 的缘故，对象可以在任何时候添加新的方法，`Apple` 觉得开发者会在某个地方或者时候确实添加了该方法。这或许就是“灵活”的代价吧。 `Objective-C` 是门灵活的动态语言，可以被归入像 `Perl`、`Ruby`、`Python` 等所谓的动态语言之中。**一个对象会有哪些 method 可以调用，会比这个对象属于哪一个 class 来得更为重要**。


