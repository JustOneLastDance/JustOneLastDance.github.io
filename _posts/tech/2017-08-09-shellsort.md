---
layout: post
title: 排序（二）—— 希尔排序
date: 2017-08-09 18:29:29 +0800
categories: 技术
tags: Data Structure
keywords: sort
description: 知识整理
---

**希尔排序的基本思想**  
把待排序的数据元素分成若干个小组，对同一小组内的数据元素用直接插入排序；小组的个数逐次缩小；当完成了所有数据元素都在一个组内的排序后，排序过程结束。希尔排序又称做缩小增量排序。   

**实现方法**  

```
for m in 0..<spanArray.count {

    let span = spanArray[m]
    for k in 0..<span {
        
        for i in k..<(array.count - span) {
            let temp = array[i + span]
            var j = i
            if j > -1 && temp <= array[j] {
                (array[j + span], array[j]) = (array[j], array[j + span])
                j = j - span
            }
        }
    }
}
```  

**理解**  
每次分组是根据增量来进行分组。例如3为增量，一组的数分别是数组中序号分别为 `1` `4` `7`…… 这是一个从疏到密的过程，最后一个增量必然是1。个人的理解是，每次进行分组直接排序后，相对小的数排列会靠前，大的数则靠后。到最后一次排序时大多数的元素已经是有序的了。当需要排序的数组中元素是接近有序的，那么直接插入排序的时间效率是接近好的。




