---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-开发者选项
date: 2017-05-11 14:52:00
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.
英文原文文档地址: [Litho-doc](http://fblitho.com/docs/developer-options)
## 工具
### 开发者选项

</p>
</p>

除了Stetho之外,我们还提供了两个编译时标志,用于可视化你的应用程序的Component层级结构.这类似于Android的内部设置:显示View边界,但是由于Litho并不是总是使用Android View,所以我们实现了我们自己的方式使它变得更加实用.

在[ComponentsConfiguration](http://fblitho.com/javadoc/com/facebook/litho/config/ComponentsConfiguration)类中有两个字段来控制这些.

</p>
</p>

##### debugHighlightInteractiveBounds
---
高亮显示Component的交互边界以及Component的扩展触摸边界(如果存在的话).

</p>
</p>

##### debugHighlightMountBounds
---
高亮显示可挂载的drawable和view的边界.由框架自动添加(例如,在Component被点击的时候)的View将会被使用不同的颜色来高亮显示.

</p>
</p>

这些是默认关闭的.如果你想要在你的程序中打开它们,你可以在你的程序中的任何地方覆写他们:

``` java
ComponentsConfiguration.debugHighlightInteractiveBounds = true;
ComponentsConfiguration.debugHighlightMountBounds = true;
```

</p>
</p>
</p>
</p>

##### [回到导航页](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%80%BB%E8%A7%88%E5%92%8C%E5%AF%BC%E8%88%AA/)
</p>
</p>
</p>
</p>