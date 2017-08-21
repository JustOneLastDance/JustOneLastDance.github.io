---
layout: post
title: 关于 Cell 的重用机制
date: 2017-08-20 22:24:12 +0800
categories: 技术
tags: iOS
keywords: reuse
description: 知识整理
---

最近遇到一个问题：`UICollectionView` 下面嵌套 `UITableView`，初始化后只拖动其中一个 `tableView` 再拖动 `collectionView`，会发现其他 `tableView` 的 `contentOffSet` 也发生了变化。虽然第一时间是想到了可能是 `cell` 重用导致的问题，但是解决这个问题花费了挺多时间。因此，写下这篇文章为自己重新梳理一下对重用机制的理解。

**什么是重用机制**  
重用机制指的是反复使用类似 `cell` 这样的控件。 `cell` 会放在 `可见cell数组` 或者 `重用cell数组`(简称重用池) 中。`可见cell数组` 中存放的是在屏幕上可以看到的 `cell`。重用池中存放的是可以被重用的 `cell`。怎样的 `cell` 能够被重用呢？很简单，类型一样。 `cell` 内部结构是一样的，就好比食堂里面的餐盘一样。食堂的餐盘是可以反复使用的，只是每次装的菜和汤是不一样的。`cell` 是个容器，这个容器的内部结构是一样，只不过每次获取的数据是不一样的罢了。`cell` 就是食堂里的餐盘，数据就是菜和汤了。

**为什么需要重用**  
如果你理解了我上面拿餐盘作为例子的话，聪明的你已经可以回答这个问题了。有哪个食堂是吃完饭之后直接把餐盘给扔掉的？如果有，请喊我去，我想体验一下这样的土豪食堂。食堂会估算每次用餐时间最高峰的人数来定需要多少个餐盘，餐盘是可以重复利用的，如此一来可以节省成本又能满足用餐需要。其实 `iOS` 也是这么想的，多么的精明。在程序中，内存是极为精贵的，恨不得一个 `bit` 掰成2个用。如果说总共有10万条数据，相应的来创建10万个 `cell` ，内存当然不够用。在程序中，创建 `cell` 的个数取决于一个屏幕内能够显示多少条 `cell`，然后再加一。显而易见的是，这种做法刚好和食堂的做法是一致的，都会考虑最大用量。但是，`cell`为什么要多建一个呢？我的理解是，多出来的这个是为了预加载。当想要浏览其他 `cell` 的时候，程序已经提前加载好了数据，此时只要上拉浏览即可。

**重用机制的使用**  
首先需要为 `UITableView` 或者 `UICollectionView` 注册 `cell`

```
register(_ cellClass: Swift.AnyClass?, forCellReuseIdentifier identifier: String)
```

`cellClass` 是你需要重用的 `cell` 的类型，而 `ReuseIdentifier`， 则是唯一标志这种 `cell` 的标识，为下面的重用使用作铺垫。

重用机制的体现主要是这2种方法

```
open func dequeueReusableCell(withIdentifier identifier: String) -> UITableViewCell? 
// Used by the delegate to acquire an already allocated cell, in lieu of allocating a new one.

@available(iOS 6.0, *)
open func dequeueReusableCell(withIdentifier identifier: String, for indexPath: IndexPath) -> UITableViewCell 
// newer dequeue method guarantees a cell is returned and resized properly, assuming identifier is registered
```

首先解释一下 `dequeue`，“remove from a queue”，从一个队列中移除。在程序中的体现是，当 `cell` 移除屏幕之外，会把这个 `cell` 从 `可见cell数组` 中移除，同时添加到重用池当中去，为即将显示的 `cell` 做准备。什么时候需要重用 `cell`，还是创建新的 `cell` 都得靠上述的2个函数来实现。

回过头来看上述的两个函数分别是什么含义。  
`dequeueReusableCell(withIdentifier identifier: String)` ：根据官方文档注释来看的话意思是说通过代理获得一个已经创建好的 `cell`，而不是重新创建一个新的 `cell`。同时需要注意的是返回值，返回值是 `UITableViewCell?`。在 `Swift` 的语义里，对象加一个问号代表这个对象是个 `Optional`，它是一个可选项。可选项的意思是说，这个返回值啊，可以有也可以没有。
`dequeueReusableCell(withIdentifier identifier: String, for indexPath: IndexPath)`：根据官方文档注释来看的话意思是说，这是一个最新的函数，能够保证返回 `cell` 是一定有值的，但是必须是注册过标志符的。建议使用这个最新的函数来实现重用机制。  

**为何会出现开头提到的问题**  




