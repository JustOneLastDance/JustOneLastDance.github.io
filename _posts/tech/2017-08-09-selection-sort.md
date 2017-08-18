---
layout: post
title: 排序（三）—— 选择排序
date: 2017-08-09 20:32:34 +0800
categories: 技术
tags: Data Structure
keywords: sort
description: 知识整理
---


**直接选择排序的基本思想**  
每次从待排序的数据元素集合中选取关键字最小的数据元素并将它与原始数据元素集合中的第一个数据元素交换位置；然后从不包括第一个位置上数据元素的集合中选取关键字最小的数据元素并将它与原始数据元素集合中的第二个数据元素交换位置；如此重复，直到数据元素集合中只剩下一个数据元素为止。  

代码如下  

```
for i in 0..<(tempArray.count - 1) {
            var smallIndex = i
            for j in (i + 1)..<(tempArray.count) {
                if tempArray[j] < tempArray[smallIndex] {
                    smallIndex = j
                }
            }
            
            if smallIndex != i {
                swap(&tempArray[i], &tempArray[smallIndex])
            }
            
        }
```  

**需要注意的是** 需从第二个位置开始比较大小。  

<br/>

**堆排序的基本思想 （首先需要了解什么是树）**
堆实际上是一个树。
在直接选择排序中，待排序的数据元素集合构成一个线性表结构，要从有n个数据元素的线性表中选择出一个最小的数据元素需要比较（n-1）次。如果能把待排序的数据元素集合构成一个完全二叉树结构，则每次选择出一个最大（或最小）的数据元素只需比较完全二叉树的高度次。  

**I	创建一棵二叉树，调整成为最大堆**
最大堆指的是父结点大于子结点，为后续步骤服务。当发现子结点大于父结点时，在数组中的表现就是将2个元素进行交换。
举例：数组 `a` 中存放了n个元素，数组下标从0开始，当数组下标 `2i + 1 < n` 时，`a[i] >= a[2i + 1]`；如果当数组下标 `2i + 2 < n` 时，`a[i] > a[2i + 2]`，这样的数组结构就是最大堆。  

代码如下

``` 
func createHeap(n: Int, height: Int) {
    var flag: Bool = false
    var i: Int = height
    var j: Int = 2 * i + 1
    let temp = heapArray[i]
    
    while j < n && flag != true {
        if j < n - 1 && heapArray[j] < heapArray[j + 1] {
            j = j + 1
        }
        
        if temp > heapArray[j] {
            flag = true
        } else {
            heapArray[i] = heapArray[j]
            i = j
            j = 2 * i + 1
        }
        
        heapArray[i] = temp
    }
    
}
```
`height` 为树的高度；`n` 为数组长度，目的是为下面的步骤做铺垫。  

**II	初始化创建最大堆**

```
func initBigHeap() {
    for i in (0...(heapArray.count - 1)/2).reversed() {
        createHeap(n: heapArray.count, height: i)
    }
}
```  
`(heapArray.count - 1)/2` 表示的是第一个非子结点。创建一个完整最大堆的过程是从数据元素下标为 `(n - 1)/2` 开始，直到根节点，即下标为 `0` 为止的循环调用 `createHeap`。  

**III 调整每个堆的父结点满足最大堆的要求**

```
func heapSort() {
    initBigHeap()
    for i in (1...(heapArray.count - 1)).reversed() {
        (heapArray[i], heapArray[0]) = (heapArray[0], heapArray[i])
        createHeap(n: i, height: 0)
    }
}
```


