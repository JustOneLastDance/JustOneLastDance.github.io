---
layout: post
title: 关于 __autoreleasing 问题
date: 2017-08-04 15:42:50 +0800
categories: 技术
tags: iOS
keywords: 修饰符
description: 知识点
---
	
在提及类似于 `__strong` 有哪些时，想必可以脱口而出的是 `__weak` `__unsafe_unretained`, 可还有一个修饰符和之前几个相比，并不常见。  

>__autoreleasing  

`__strong` & `__weak` 分别是强弱引用。`__unsafe_unretained` 的作用和 `weak` 类似，但是区别在于当指向的对象被释放时 `weak` 会将指针置为 nil ，而 `__unsafe_unretained` 则不会，这会导致野指针的产生，这或许就是这个修饰符中包含 `__unsafe` 字符的原因吧。  

**那 __autoreleasing 是什么？**  

首先需要明确几条内存管理的原则： 
1. 自己创建的对象，自己拥有
2. 不是自己创建的对象，也能够自己拥有
3. 自己不想再拥有的对象，可以释放
4. 不是自己拥有的对象，无法释放  

还有一条至关重要的原则是，来自官方文档 `Memory Management Policy` 章节中的这样一句话
>You own any object you create
>You create an object using a method whose name begins with “alloc”, “new”, “copy”, or “mutableCopy” (for example, alloc, newObject, or mutableCopy).

在创建对象时，会用到包含了 `alloc`、`new`、`copy`、`mutableCopy` 这些字眼的方法，用这些方法创建出来的对象，才是被认为是自己创建，并且需要自己拥有。换句话来说，要对这些“对象”负责。


那么，没有用到这些方法创建出的对象该如何管理？这就要引出 `autorelease` & `__autoreleasing`。  
在 `MRC` 下，是需要对对象进行 `retain` & `release` 操作的，即手动管理内存。不是以上述方法创建出来的对象，就用 `autorelease` 方法。这个方法的作用是将一些无法自己来进行内存管理的对象，放进 `autoreleapool` 中去。在 `ARC` 下，完全摒弃了 `retain` & `release` 这样的操作。取而代之的，是让编译器在合适的地方添加这些对象的内存管理代码。而 `__autoreleasing` 正是在 `ARC` 下， 等价于 `MRC` 下的 `autorelease` 操作。  

在日常的开发中，似乎并没有出现过 `__autoreleasing` 的影子。因为 `autoreleasing` 显式使用并不多，更多的是隐式使用。编译器会自动为对象添加 `__autoreleasing` 修饰符。

## Tips
1. 全局变量不能使用 `__autoreleasing` 进行修饰
2. 当局部变量用 `__autoreleasing` 这个变量在当前的 `autoreleasepool` 的一次完整循环结束之前，不会被销毁。

