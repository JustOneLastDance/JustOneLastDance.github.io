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


