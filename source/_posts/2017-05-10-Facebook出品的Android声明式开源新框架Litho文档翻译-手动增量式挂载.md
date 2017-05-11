---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-增量式挂载
date: 2017-05-10 14:37:16
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.
英文原文文档地址: [Litho-doc](http://fblitho.com/docs/inc-mount)
## 进阶指引
### 增量式挂载

</p>
</p>

#### 手动增量式挂载
---

如果你没有使用[Recycler](http://fblitho.com/javadoc/com/facebook/litho/widget/Recycler),你也仍然可以集成[增量式挂载](https://shikieiki.github.io/2017/04/27/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E5%A2%9E%E9%87%8F%E5%BC%8F%E6%8C%82%E8%BD%BD/)到你的现有的UI实现里.你必须在每次LithoView的可见区域发生变化的时候显式的通知框架,通过调用:

``` java
myLithoView.performIncrementalMount();
```

举例来说,如果你在ListView中使用Component,你可以在一个OnScrollListener中调用performIncrementalMount().

</p>
</p>
</p>
</p>

##### [回到导航页](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%80%BB%E8%A7%88%E5%92%8C%E5%AF%BC%E8%88%AA/)
</p>
</p>
</p>
</p>
