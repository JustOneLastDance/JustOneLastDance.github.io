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


