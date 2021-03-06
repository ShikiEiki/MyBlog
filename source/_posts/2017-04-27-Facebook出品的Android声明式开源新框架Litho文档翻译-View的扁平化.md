---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-View的扁平化
date: 2017-04-27 10:09:23
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.
英文原文文档地址: [Litho-doc](http://fblitho.com/docs/view-flattening)
## 架构
### View的扁平化

</p>
</p>

让我们看看下方的例子.它包含一个图片一个标题和一个副标题.在传统的Android View 系统里,你可以使用几个view来包含这些元素,再用一个viewGroup来包裹它们.

![](/image/20170426150749.png)

Litho会自动减少最终UI层级结构所包含的View数量。布局计算这一步产生的布局树只是你的UI的蓝图，与Android的View没有直接的耦合。这允许框架在挂载组件之前处理布局树以获得最佳渲染性能。

我们使用两种途径来实现:
首先，Litho能在布局计算后完全忽略容器类，因为它们在挂载步骤中不会被用到。拿我们的例子来说，在挂载时，不会有单独包含标题和子标题的View。
第二，Litho可以选择挂载一个view或者挂载一个drawable。事实上，对于Litho框架里的大多数核心组件，如Text和Image，挂载的是drawable，而不是view。

这些优化的结果是,示例中UI的组件实际上将被渲染为一个独立,扁平的视图。你可以在下面的屏幕截图中看到这一点([启用了开发者选项中的显示布局边界]()).

![](/image/20170426150806.png)

虽然扁平化的view对于减少内存使用和缩短绘制时间有相当大的好处，但它并不是一颗银弹(译者注:即它不是一个对所有情况都能用的万用方案)。当我们希望依赖Android view的特定功能时（例如触摸事件处理，可访问性或局部无效化），Litho提供一个非常通用的系统来自动对挂载Component的视图层次结构"去扁平化"。例如，如果要在示例中启用单击图像或文本，如果设置了[点击处理器](),框架将自动把文字或图像包装在view一个新的view中。

</p>
</p>
</p>
</p>

[回到导航页](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%80%BB%E8%A7%88%E5%92%8C%E5%AF%BC%E8%88%AA/)

</p>
</p>
</p>
</p>