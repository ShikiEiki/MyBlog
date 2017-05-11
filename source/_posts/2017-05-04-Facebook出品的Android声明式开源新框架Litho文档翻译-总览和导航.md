---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-总览和导航
date: 2017-05-04 10:59:55
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.

今天逛github发现facebook开源了一个新框架Litho,大概看了一下介绍,这是一个声明式UI框架,最初是用于为RecyclerView生成复杂的可滚动的UI而做的.它的特点是可以在后台线程中计算布局结构,并且支持资源回收再利用.使用Recycler的思想,自己重新写了一套UI布局框架,可以大大的改进现有的RecyclerView的渲染效率.
果然是facebook,听起来很牛逼啊,在网上搜了一下,暂时还没有中文的文档什么的,所以为了提升英语水平我也来边学边翻译吧,如果翻译有问题的地方,欢迎指出.

英文原文文档地址: [Litho-doc](http://fblitho.com/)
github地址: [Litho-Github](https://github.com/facebook/litho)

# Litho 官方文档翻译

### 介绍Litho
--- 
##### [Litho是什么?](https://shikieiki.github.io/2017/04/25/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-Litho%E6%98%AF%E4%BB%80%E4%B9%88/)
##### [编写动机](https://shikieiki.github.io/2017/04/25/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E7%BC%96%E5%86%99%E5%8A%A8%E6%9C%BA%20/)
##### [使用](https://shikieiki.github.io/2017/04/25/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E4%BD%BF%E7%94%A8/)


### 快速开始
---
##### [准备工作](https://shikieiki.github.io/2017/04/27/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E5%87%86%E5%A4%87%E5%B7%A5%E4%BD%9C/)
##### [教程](https://shikieiki.github.io/2017/04/27/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%95%99%E7%A8%8B/)
##### [编写Component](https://shikieiki.github.io/2017/04/27/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E7%BC%96%E5%86%99Component/)
##### [使用Component](https://shikieiki.github.io/2017/04/27/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E4%BD%BF%E7%94%A8Component/)

### 参考
--- 
##### [Layout Specs](https://shikieiki.github.io/2017/04/28/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-LayoutSpecs/)
##### [Mount Specs](https://shikieiki.github.io/2017/04/28/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-MountSpecs/)
##### [Props](https://shikieiki.github.io/2017/05/02/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-Props/)
##### [State](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-State/)
##### [Layout](https://shikieiki.github.io/2017/05/05/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-Layout/)
##### [Recycler](https://shikieiki.github.io/2017/05/05/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-Recycler/)

### 处理事件
--- 
##### [概述](https://shikieiki.github.io/2017/05/05/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E4%BA%8B%E4%BB%B6%E5%A4%84%E7%90%86%E6%A6%82%E8%BF%B0/)
##### [触摸事件处理](https://shikieiki.github.io/2017/05/08/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E8%A7%A6%E6%91%B8%E4%BA%8B%E4%BB%B6/)
##### [可见新的处理](https://shikieiki.github.io/2017/05/08/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E5%8F%AF%E8%A7%81%E6%80%A7%E7%9A%84%E5%A4%84%E7%90%86/)

### 兼容性
---
##### [Style](https://shikieiki.github.io/2017/05/08/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-Styles/)
##### [无障碍环境](https://shikieiki.github.io/2017/05/09/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%97%A0%E9%9A%9C%E7%A2%8D%E7%8E%AF%E5%A2%83/)
##### [RTL](https://shikieiki.github.io/2017/05/09/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-RTL/)

### 测试
---
##### [单元测试](https://shikieiki.github.io/2017/05/09/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95/)

### 进阶指引
--- 
##### [自定义布局](https://shikieiki.github.io/2017/05/10/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E8%87%AA%E5%AE%9A%E4%B9%89%E5%B8%83%E5%B1%80/)
##### [TreeProp](https://shikieiki.github.io/2017/05/10/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-TreeProp/)
##### [增量式挂载](https://shikieiki.github.io/2017/05/10/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%89%8B%E5%8A%A8%E5%A2%9E%E9%87%8F%E5%BC%8F%E6%8C%82%E8%BD%BD/)
##### [创建ComponentTree](https://shikieiki.github.io/2017/05/10/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E5%88%9B%E5%BB%BA%E4%B8%80%E4%B8%AAComponentTree/)

### 架构
---
##### [代码生成器](https://shikieiki.github.io/2017/04/27/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E4%BB%A3%E7%A0%81%E7%94%9F%E6%88%90%E5%99%A8/)
##### [异步布局](https://shikieiki.github.io/2017/04/27/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E5%BC%82%E6%AD%A5%E5%B8%83%E5%B1%80/)
##### [增量式挂载](https://shikieiki.github.io/2017/04/27/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E5%A2%9E%E9%87%8F%E5%BC%8F%E6%8C%82%E8%BD%BD/)
##### [View的扁平化](https://shikieiki.github.io/2017/04/27/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-View%E7%9A%84%E6%89%81%E5%B9%B3%E5%8C%96/)
##### [回收机制](https://shikieiki.github.io/2017/04/27/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E5%9B%9E%E6%94%B6%E6%9C%BA%E5%88%B6/)

### 最佳实践
---
##### [最佳实践](https://shikieiki.github.io/2017/05/10/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%9C%80%E4%BD%B3%E5%AE%9E%E8%B7%B5/)

### 工具
---
##### [调试](https://shikieiki.github.io/2017/05/11/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E8%B0%83%E8%AF%95/)
##### [开发者选项](https://shikieiki.github.io/2017/05/11/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E5%BC%80%E5%8F%91%E8%80%85%E9%80%89%E9%A1%B9/)

### 参与
---
##### [如何参与](https://shikieiki.github.io/2017/05/11/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E5%A6%82%E4%BD%95%E5%8F%82%E4%B8%8E/)
##### [仓库架构](https://shikieiki.github.io/2017/05/11/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E4%BB%93%E5%BA%93%E6%9E%B6%E6%9E%84/)