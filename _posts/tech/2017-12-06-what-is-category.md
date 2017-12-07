---
layout: post
title: 什么是 Category
date: 2017-12-06 17:03:26 +0800
categories: 技术
tags: iOS
keywords: category
description: 知识整理
---


不用继承父类，就能直接对该类添加新的方法，或者重写原有的方法。在`OC`中，通过`Category`能够更加深入地去理解，每个类有哪些方法。`Category`之所以能够直接为类添加防啊，是通过`runtime`来实现的，`runtime`提供了一个叫做`class_addMethod`的方法。在`Swift`中的`Extension`，和`Category`有类似的特性。  

**在何时需要使用 Category**   
当需要对某个类进行扩展，但是这个类很难被继承时使用，有以下几种情况：
1. Foundation 对象
2. 使用工厂方法创建的对象
3. 单例对象
4. 在项目中出现次数较多的对象

**Foundation**  
当使用`alloc/init`创建一个`Foundation`对象时，会同时有`Foundation`和`Core Foundation`实现的`subclass`，实际创建出来的对象会超出预想。
根据以往的惯性思维，我们会认为`NSMutableString`是继承自`NSString`的，然而实际上并不是。在创建`NSString`的时候，得到的是`__NSCFConstantString`；创建`NSMutableString`的时候，得到的是`__NSCFString`。**然而`__NSCFConstantString`是继承自`__NSCFString`的。**很难以置信吧！  
![Foundation类型验证截图](http://wx4.sinaimg.cn/mw690/a73cca61gy1fm76mpr9m3j212q08an01.jpg)  
`CLS`是一个宏定义，如下

```
#define CLS(x) NSStringFromClass([x class])
```  
需要注意的是仅仅传入类型是无法显示出`Core Foundation`类型的，`x`必须是`[x string]`，目前并不明白这个`string`的作用是什么，看注释`string`的返回值是一个空的字符串，有知道这是为什么的同学可以知会我一声。  

当需要继承`Foundation`类来创建自己的类时，如果没有重写创建实例的方法，**那么就无法保证创建出来的对象时自己创建的类的对象**。一般来说，我们无法知道`Foundation`内部代码是如何实现的，继承该类不能保证能够实现自己所需要的功能，因此可以使用`Category`来对该类进行扩展。  

**什么是工厂方法**  
工厂方法(Factory Method Pattern)指的是用来解决不用指定是哪个类，就能创建对象的方法。在某个类下，可能有多个子类，但对外界来说并不需要去知道这些子类是什么，只需要根据这些子类的上一层的类输入指定的条件，然后上一层的类会根据输入的条件，选择适合的子类创建对象并返回。  

举个例子：有一个类叫车，这个车类下面有好几个子类分别是：独轮车、自行车、三轮车和小汽车。还有一个类叫做造车厂，造车厂下面包含了车的所有子类。用户并不需要知道造车厂是怎么造车的，用户只需要告诉造车厂，我要一个有多少个轮胎的车，那么造车厂会根据轮胎的数量这个条件来造相对应的车。用户说我要一个有3个轮子的车，那么造车厂就会返回“三轮车”这个对象给用户。  

当需要为所有子类添加一个新的方法，而又无法改动他们的父类，这时就可以使用`Category`来对父类进行扩展。  

**Singleton**  
`Singletion`就是单例。某个类，有且只有一个对象，并且只能对这个对象进行操作，而不是创建新的对象，例如`[UIApplication sharedApplication]`、`[NSUserDefaults standardUserDefaults]`就是在系统中常见的单例。  

`Singleton`的创建方式

```
OC
static xxxx *instance = nil;
static dispatch_once_T onceToken;
dispatch_once(&onceToken, ^{
	instance = [[self alloc] init];
});

swift
static let instance = xxxx()
```  

**需要注意的是**如果自己的类继承了该单例的类，却没有重写创建单例的方法，那么创建出来的单例依然是这个类的单例，而不是自己的类的单例。但是重写该方法也有风险，需要注意在这个类中创建单例的方法中是否存在**除创建单例实例的其他代码，例如一些属性的初始化**，如果一味的重写而不去自习观察原有的创建单例的方法，可能会引起意想不到的问题。


