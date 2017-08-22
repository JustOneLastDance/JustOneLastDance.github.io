---
layout: post
title: Selector为何是Objective-C的重中之重
date: 2017-08-22 16:45:05 +0800
categories: 技术
tags: iOS
keywords: selector
description: 知识整理
---

**什么是 selector**  
`selector` 是用一段字符串来表示对象的某个函数。从专业的角度来说的话，`selector` 就是 `Objective-C` 的 `virtual table` 中指向实际执行函数指针的一个 c 字符串。由于函数可以用字符串来表示，那么 `selector` 可以看成是一个可以传递的参数。

**runtime**  
在 `Objective-C` 中，一个对象有多少个方法，那么相应的，这个对象的类的 `virtual table` 中就有多少个 `selection` 或者是说有多少个 `c function pointer`。

`Objective-C` 是一门灵活的语言。之所以说灵活，是因为对象有哪些方法是在 `runtime`决定的。正因为如此，可以在 `runtime` 的时候对对象进行修改。对象可以在不继承的情况下，对对象添加新的方法，这个称之为 `category`。
我们可以随时把已有的 `selector` 指向两个不同的 `c function pointer`。原本的 `selector` 不再指向原有的 `c function pointer` ，而是指向了另外的函数指针，让 `selector` “徒有其表”。程序执行的时候，当调用了 A 的 `selector` 的时候，实际上是实现了 B 的函数，这个称之为 `swizzling method`。
在 `AFNetworking` 中，就有这样的运用，是否发现当调用网络请求的时候，不用再去写 `resume` 了？可以参考 `AFURLSessionManager` 下的一个类 `_AFURLSessionTaskSwizzling`。

**Objective-C 为何没有重载**  
重载（overloading）指的是函数名相同，但是参数的类型和个数不同的函数。
为何没有重载的原因是由于 `selector` 调用函数的方式导致的。一个 `selector` 只能指向一个实现方法。如果有多个同名的函数名的话，最终调用的是最后在 `runtime` 加入的那个函数。
`Objective-C` 中的 `virtual table` 类似一个 dictionary。当每次调用函数的时候，需要到这个表中，查询和函数名相匹配的字符串的方法，执行效率较差。但是有一个优点，只要函数名不变，无论函数内部怎么改都能够被调用，因此新版本的 iOS 可以运行老版本的 app，新版本能够最大化的兼容老版本。
`Swift` 的 `virtual table` 和 c++， java 一样，类似一个数组。当调用方法的时候，类似数组获取元素那样，类似于调用第几个方法。这样以来，执行效率就会变高，毋需再一一查询。
这可能就是 `Swfit` 比 `Objective-C` 执行效率高的原因之一吧。


