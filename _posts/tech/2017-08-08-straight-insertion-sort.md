---
layout: post
title: 排序（一）—— 直接插入排序
date: 2017-08-08 17:00:10 +0800
categories: 技术
tags: Data Structure
keywords: sort
description: 知识整理
---

**何谓插入排序？**  
插入排序的基本思想是从初始有序的自己和开始，不断地把心的数据元素插入到已排列有序子集合的合适位置上，使子集合中数据元素的个数不断增加，当子集合等于集合时，插入排序算法结束。常用的插入排序有直接插入排序和希尔排序两种。本文介绍直接插入排序。  

**直接插入排序的基本思想**  
顺序地把待排序的数据元素按其关键字值的大小插入到已排序数据元素子集合的适当位置。子集合的数据元素个数从只有一个数据元素开始逐次增大。当子集合大小最终和集合大小相同时排序完毕。  

**实现方法一: 创建一个新数组来进行直接插入排序**  

```
        var orderedArray: [Int] = Array()
        
        for i in 0...(waitToOrderArray.count - 1) {
            // 数组为空时，插入第一个元素
            if orderedArray.count == 0 && i == 0 {
                orderedArray.append(waitToOrderArray[0])
                continue
            }
            
            for j in 0...(orderedArray.count - 1) {
                if waitToOrderArray[i] <= orderedArray[j] {
                    orderedArray.insert(waitToOrderArray[i], at: j)
                    break
                }
                // 必须添加条件 (j == orderedArray.count) 否则会出现多次插入同一个元素的可能
                if j == orderedArray.count - 1 && waitToOrderArray[i] > orderedArray[j] {
                    orderedArray.append(waitToOrderArray[i])
                }
            }
        }
```  
**需要注意的是**在第二个 `for` 循环中的第二个 `if` 语句的判断条件必须有 `j == orderedArray.count - 1` 。如果没有这个条件可能会出现一个元素插入多次的情况。由于 `orderedArray` 是由小到大进行排序的，若待插入元素比已经排序的数组中的任何数都大，那么就会出现插入多次的情况。正因为是已排序的数组，因此需要判断是否这个待插入元素比已排序数组的每个元素元素都大，然后在和最后一个元素进行比较的时候，把这个元素插入到数组尾部。  

**实现方法二： 在原有数组的基础上进行直接插入排序**  
 
```
for i in 0..<(array.count - 1) {
            let temp = array[i + 1]
            var j = i
            while j >= 0 && temp < array[j] {
                (array[j+1], array[j]) = (array[j], array[j+1])
                j = j - 1
            }
            array[j+1] = temp
}
```


