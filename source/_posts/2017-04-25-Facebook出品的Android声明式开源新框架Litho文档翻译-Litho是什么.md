---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-Litho是什么?
date: 2017-04-25 10:58:01
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.

# 介绍Litho

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

[回到导航页](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%80%BB%E8%A7%88%E5%92%8C%E5%AF%BC%E8%88%AA/)