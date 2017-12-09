---
layout: post
title: 什么是 Extension
date: 2017-12-09 18:10:48 +0800
categories: 技术
tags: iOS
keywords: extension
description: 知识整理
---


`Extension`在语法上和`Category`非常相似，可实际上却又不一样。在语法上，`Extension`更像是一个没有名字的`Category`。在`class`名称后面直接加上一个空的括号`()`就是`Extension`，而在`Extension`中定义的方法需要在原来的`class`的`impletement`中实现。形如下：

```
@interface MyClass: NSObject
@end

@interface MyClass()
- (void)test
@end

@impletementation MyClass
- (void) test {}
@end
```  

**Extentsion 的作用**  
1. 为`header`(.h 文件)进行分组  
如果在一个`class`声明了太多的的`method`，可以使用`Extension`对这些`method`进行分组归类，将这些`method`放到`Extension`中去。`Extension`除了可以对`method`进行分组，也能对成员变量进行分组。  
2. 管理 Private Methods  
在早期的`Xcode`中，若没有声明`method`而是直接实现这些`method`，当调用这些`method`时，会出现警告，而这些警告往往是很重要的。把这些`method`移到其他方法之前，可以消除警告。但是不能保证完全消除，因为这些`method`之间也有可能相互调用。那就需要进一步知道这些`method`调用顺序，这样做实在是太麻烦了，并不可取。苹果给的建议是在`.m`的开头写一个`Extension`，然后将这些`method`放在这个`Extension`中，这样就能避免那些警告。  

在`Swift`中，同样也有一个`Extension`，可以直接使用用来对`class`进行扩展。这个`Extension`并不是`Objective-C`中的`Extension`，更像是其中的`Category`。这三者的差别是，`Objective-C`中的`Category`需要给定名字，`Objective-C`中的`Extension`是没有名字的`Category`，`Swift`中的`Extension`也同样没有名字。`Swift`中的`Extension`还能对`protocol`和`struct`进行扩展。

**Category 和 Extension 到底有什么区别**  
如果两者有什么明显的不同点话应该就是，`Extension`可以添加成员变量而`Category`不可以。但是`Category`可以通过一种方法来实现添加成员变量的效果。苹果提出一套叫做`Assocaited Object`的方案，可以在`Category`中添加`getter/setter`方法。这个方法的大致思想是在`runtime`，创建了一张表，这张表格能够记录哪个对象关联了哪个类。


