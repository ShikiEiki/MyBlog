---
title: Facebook出品的Android声明式开源新框架Litho文档翻译(一)
date: 2017-04-25 10:58:01
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.

今天逛github发现facebook开源了一个新框架Litho,大概看了一下介绍,这是一个声明式UI框架,最初是用于为RecyclerView生成复杂的可滚动的UI而做的.
听起来很牛逼啊,网上搜了一下,暂时还没有中文的文档什么的,所以为了提升英语水平我也来边学边翻译吧,如果翻译有问题的地方,欢迎指出.

# 介绍Lithos

### Litho是什么?
Litho是一个为在Android系统上高效的构建用户界面(UI)的声明式框架.它允许你通过使用一套基于Java注解的简单的API来编写最优化的Android views.这个框架主要是被设计用来实现在RecyclerView上构建复杂的可滚动的UI的.

通过Litho,你可以使用Litho的Components(组件)代替传统的Android Views来构建UI.一个Component本质上是一个带有不可变输入(我们称之为props)并且返回component hierachy(组件层级结构)来描述UI的方法.

``` java
@LayoutSpec
class HelloComponentSpec {

  @OnCreateLayout
  static ComponentLayout onCreateLayout(
      ComponentContext c,
      @Prop String name) {

    return Text.create(c)
        .text("Hello, " + name)
        .textSizeRes(R.dimen.my_text_size)
        .textColor(Color.BLACK)
        .withLayout()
        .paddingDip(ALL, 10)
        .build();
  }
}
```

你只需要简单的声明你想要展示的元素,然后Litho就会自动的渲染他们,并且以一种高效的方式——使用后台线程计算布局参数、自动扁平化层级结构、还有使用增量式挂载机制处理复杂的component。

我们的【教程】可以指导你一步一步的在你的APP里使用Litho，或者你也可以阅读【快速开始】来学习如何编写和使用Litho的Component。

### 编写Litho的动机
在Android上构建一个列表式界面相当的简单，虽然大多数app要更加复杂一点，但是大体上你只需要编写一个列表中Item的layout，然后再将其挂载到RecytclerView的adapter上去就完成了。

当你的adapter拥有许多类型的view的时候，你就不得不去考虑一种更健壮的方法来回收view。如果你有很多很多种类型的view时，每次一个新的类型的view被显示的时候，RecyclerView都会不断的inflate新的view，这很可能导致滚动时帧数的下降。

你可以通过回收和重复利用,在多个item之前共享一些view实例,来达到减少view的类型的目的,但是随着你添加了更多的逻辑到你的产品中去,bug往往会越来越容易发生.

如果列表的item比较复杂,你很可能会优化你的layouts.对于简单的情况,你可以直接使用Android库里的layout来避免许多的"坑",但是情况往往比较复杂.在这种情况下,一个通用的做法是特别为你的需求定制特殊的Custom view(客制化view).使用Custom view可以有效的提升UI的效率,但是由于它们会增添项目的复杂度并且维护成本也很高,所以会拖慢项目进度.

你也可以通过分解一个复杂的item为多个小的item,来把滚动时计算布局和绘制每一帧的花费的时间分解开来,从而优化RecyclerView的item.这种方法在一些情况下效果不错,但是它通常会导致adapter中view类型的爆炸式增长,并且有可能仍然需要花费代价去创建Custom Views,这就依然导致了我们上面所讲到的问题.

Litho主要工作就是把优化RecyclerView的复杂工作打包起来.在使用Litho时,没有view类型的区别,你可以在后台线程中提前、无缝的构建你的UI,并且自动地把他们渲染成更加扁平化的层级结构。这些梦幻的特性，你都可以免费的通过一套非常简单的编程模式来获得。

### 使用
Litho的首要使用场景为:具有复杂内容的RecyclerView.因为它甚至在具有复杂的item和多种类型的内容RecyclerView上也表现出了极好的滚动特性.这在使用传统的android view的情况下是非常具有挑战性的工作.

你也可以因为Litho简单的声明式的API来选择采用Litho来构建UI,Litho的基于单向数据流函数式编程模型会在你的产品变得复杂的时候也很容易理解.

高动态化的UI动画和复杂手势现在还很难用Litho实现,新的针对layout专场和手势的API将会很快推出.

