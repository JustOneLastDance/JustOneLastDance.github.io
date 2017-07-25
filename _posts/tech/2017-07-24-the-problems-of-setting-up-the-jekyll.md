---
layout: post
title: 使用 jekyll 时遇到的一些问题
date: 2017-07-24 05:54:21 +0800
categories: 技术
tags: 疑难杂症
keywords: jekyll
description: 解决问题
---


一直想用一种简单的方式来搭建一个属于自己的博客，在网上找了很多方法之后，最终决定用 github + jekyll 的方式来搭建，既方便又省事。不过，在搭建的过程中，遇到了很多小问题。虽然最后找到的解决方法，方式非常简单，但是找解决问题的方法非常耗时。特地写下此篇博客来记录一下，兴许能够帮助同样遇到此类问题的同学。

### 搭建的步骤

点击[这里](http://louisly.com/2016/04/used-jekyll-to-create-my-github-blog/)，有详细的搭建步骤，这里就不再一一详述了。

这个[网站](http://jekyllthemes.org/)，有许多 `jekyll` 免费的博客主题，可以选一款自己喜欢的来进行博客的搭建。

### 关于 Jekyll 的问题

![无网页格式博客](https://user-images.githubusercontent.com/9898287/28408569-8e88fcd6-6d55-11e7-8752-915b96bdd9f9.png)

当搭建完博客后，进行浏览，可能会出现如图的问题。这是因为配置文件 `_config.yml` 没有设置正确而导致的。我的解决方法是修改 Sites setting 下的 `url` 和 `baseurl`，按照如下格式进行修改

```
url: 'https://yourname.github.io'
baseurl: 'https://yourname.github.io/'
```

如果仍然不行，可以参考[这篇博客](http://downtothewire.io/2015/08/15/configuring-jekyll-for-user-and-project-github-pages/)
产生这个问题的原因是在 jekyll 内部路径信息可能配置错误，导致 github 无法获取到正确的网页样式。

![博客顶部出现异常文字](https://user-images.githubusercontent.com/7417427/27721552-a0a75d86-5d7e-11e7-8334-c615c9f7b1d9.png)

点击博客进入浏览时，有可能会出现如上图的问题，在网页顶部会显示博文的内容。

解决方法是进入路径 `_includes/head.html`，将 `remove: '<p>' | remove: '</p>'` 修改成 `strip_html`
为何如此修改，我也不懂。不了解网页开发，希望有懂的同学能够告诉我一下为什么。

### 关于 markdown 的问题

如果以上问题都解决之后，你就能够拥有一个属于自己的，漂亮的博客了。博文的格式一般是为 `markdown` 格式来书写的。如果你的博文中插入了表格，在本地浏览的时候是正常的。但是，当你将博文 `push` 到了 `github` 上之后，你会发现表格的边框不见了，而且表格内的文字格式也出现了异常。

解决方式同样是对 `_includes/head.html` 进行修改。
在标签 `<head>``</head>` 内添加

```
<style>
  table {
    border:#ccc 1px solid;
    border-radius:3px;
  }
	table th {
    padding:21px 25px 22px 25px;
    border-top:1px solid #fafafa;
    border-bottom:1px solid #e0e0e0;
  }
	table tr {
    padding-left:20px;
  }
	table td {
    padding:18px;
    border-top: 1px solid #ffffff;
    border-bottom:1px solid #e0e0e0;
    border-left: 1px solid #e0e0e0;
  }
</style>
```

由于 `github` 对 `markdown` 有一套自己的解析方式。然而 `jekyll` 默认是使用的解析方式是 `kramdown`，由于解析方式的不同，导致表格显示的异常。


### PS 看了有助于快速创建博文

强烈推荐 `Rake`
这是一个 `ruby` ，使用起来非常方便，专门为 `jekyll` 而设计的。
由于在 `jekyll` 中，对于创建博文，博文的名字有一定的要求，创建博文时会相当的繁琐。使用 `Rake` 就免去了一些不必要的麻烦。

点击[这里](http://www.jeffjade.com/2016/03/26/2016-03-26-rakefile-for-jekyll/index.html)，查看使用教程



