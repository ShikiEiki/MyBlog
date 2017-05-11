---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-使用Component
date: 2017-04-27 16:05:10
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.
英文原文文档地址: [Litho-doc](http://fblitho.com/docs/using-started)
## 快速开始
### 使用Component

</p>
</p>

生成的Component类为你在spec中定义的props提供了一个简单的构造器.为了在你的UI中使用生成的Component,你需要一个[LithoView](http://fblitho.com/javadoc/com/facebook/litho/LithoView),它是一个Android ViewGroup并且可以渲染component.

你可以通过以下的代码制定一个LithoView来渲染一个Component.
``` java
final Component component = MyComponent.create()
    .title("My title")
    .imageUri(Uri.parse("http://example.com/myimage")
    .build();
LithoView view = LithoView.create(context, component);
```

在这个例子中,MyComponent将被托管给LithoView,你可以在你的程序中像使用一个普通的Android View一样去使用这个LithoView.你可以在[教程](https://shikieiki.github.io/2017/04/27/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%95%99%E7%A8%8B/)里看到如何在一个Acitivity中使用这个LithoView.

重要提醒:示例中的LithoView,如果你在你的View层级中直接使用,将会在主线程中同步的执行布局任务.有关在主线程之外执行布局任务的更多信息,请参阅[异步布局](https://shikieiki.github.io/2017/04/27/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E5%BC%82%E6%AD%A5%E5%B8%83%E5%B1%80/).

</p>
</p>
</p>
</p>

[回到导航页](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%80%BB%E8%A7%88%E5%92%8C%E5%AF%BC%E8%88%AA/)

</p>
</p>
</p>
</p>
