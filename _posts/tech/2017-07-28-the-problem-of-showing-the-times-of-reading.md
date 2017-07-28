---
layout: post
title: 添加显示浏览数功能有问题，可以看这里
date: 2017-07-28 17:21:13 +0800
categories: 技术
tags: 疑难杂症
keywords: html，jekyll
description: 解决blog问题
---

如果你是参考 [如何让Jekyll支持文章查看数的统计](http://www.cloudchou.com/android/post-981.html)，来为博客添加浏览数功能的话，可能会遇到一些问题。

在 `Leancloud` 中只会显示一行数据，并且不论浏览那个文章，都会显示这一行数据的 `time`。

![截图](http://wx3.sinaimg.cn/mw690/a73cca61gy1fhzq8fa7paj215w05w3zp.jpg)

由于并不了解 `html` ，只能在网上一行行地粘贴代码进行翻译。同时直接对作者的博客页，右击 `view page source`，最终发现了问题在哪里。

有两个参数需要注意，他们分别是目录 `/_layouts/post.html` 文件中的 `id` & `data-flag-title`，在原博客中并没有提及。这是导致出现无法显示浏览数的重要因素。  

`id` : 在创建文章时，`jekyll` 会以 `/2017/aaa-bbb-ccc-ddd` 的形式来命名，这样一来目的是为了唯一标志来识别文章。  

`data-flag-title` : 正文的标题。正是看到别人的博客源码中，在这个标签下有内容，而我的博客源码中，在这个标签下的内容为空时，才发现问题所在的。

修改方式很简单，在原博客中描述时这样的

```
<span id="http://www.cloudchou.com" class="leancloud_visitors meta_data_item" data-flag-title="">
     <span class="post-meta-item-icon">
       <span class="octicon octicon-eye"></span> 
     </span>
     <span class="old-visitors-count" style="display: none;"></span>
     <span class="leancloud-visitors-count"></span>
     <span class="post-meta-item-text">次浏览</span>
</span>
```

第一行代码，修改成如下即可

```
<span id="{{ page.url }}" class="leancloud_visitors meta_data_item" data-flag-title="{{ page.title }}">
```

在此感谢这篇 [在个人博客中添加文章点击次数](http://www.voidcn.com/blog/u013553529/article/p-6519768.html)，给我指了一条明路。


