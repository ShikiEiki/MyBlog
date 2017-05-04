---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-编写动机
date: 2017-04-25 10:58:01
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.

# 介绍Litho

### 编写动机
在Android上构建一个列表式界面相当的简单，虽然大多数app要更加复杂一点，但是大体上你只需要编写一个列表中Item的layout，然后再将其挂载到RecytclerView的adapter上去就完成了。

当你的adapter拥有许多类型的view的时候，你就不得不去考虑一种更健壮的方法来回收view。如果你有很多很多种类型的view时，每次一个新的类型的view被显示的时候，RecyclerView都会不断的inflate新的view，这很可能导致滚动时帧数的下降。

你可以通过回收和重复利用,在多个item之前共享一些view实例,来达到减少view的类型的目的,但是随着你添加了更多的逻辑到你的产品中去,bug往往会越来越容易发生.

如果列表的item比较复杂,你很可能会优化你的layouts.对于简单的情况,你可以直接使用Android库里的layout来避免许多的"坑",但是情况往往比较复杂.在这种情况下,一个通用的做法是特别为你的需求定制特殊的Custom view(客制化view).使用Custom view可以有效的提升UI的效率,但是由于它们会增添项目的复杂度并且维护成本也很高,所以会拖慢项目进度.

你也可以通过分解一个复杂的item为多个小的item,来把滚动时计算布局和绘制每一帧的花费的时间分解开来,从而优化RecyclerView的item.这种方法在一些情况下效果不错,但是它通常会导致adapter中view类型的爆炸式增长,并且有可能仍然需要花费代价去创建Custom Views,这就依然导致了我们上面所讲到的问题.

Litho主要工作就是把优化RecyclerView的复杂工作打包起来.在使用Litho时,没有view类型的区别,你可以在后台线程中提前、无缝的构建你的UI,并且自动地把他们渲染成更加扁平化的层级结构。这些梦幻的特性，你都可以免费的通过一套非常简单的编程模式来获得。

[回到导航页](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%80%BB%E8%A7%88%E5%92%8C%E5%AF%BC%E8%88%AA/)