---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-创建一个ComponentTree
date: 2017-05-10 14:55:40
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.
英文原文文档地址: [Litho-doc](http://fblitho.com/docs/component-tree)
## 进阶指引
### 创建一个ComponentTree

</p>
</p>

在[使用Component](https://shikieiki.github.io/2017/04/27/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E4%BD%BF%E7%94%A8Component/)指引中,我们看到了如何创建一个根Component并且把它传递给一个LithoView,接着LithoView将会处理使用给出的根Component创建[ComponentTree](http://fblitho.com/javadoc/com/facebook/litho/ComponentTree)的其他步骤.ComponentTree会使用线程安全的方法来管理你的Component的生命周期,使得你可以在任何的线程中创建和使用它们.虽然通常你可以不需要去创建和管理你自己的ComponentTree,但是也有部分情况你需要自己去做.下面就会讲到是你应该如何创建一个ComponentTree,传递给它一个根Component,并且把它添加到一个LithoView中去.这个ComponentTree的create()方法会返回一个暴露出ComponentTree的设置方法的[Builder](http://fblitho.com/javadoc/com/facebook/litho/ComponentTree.Builder).

``` java
@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    final LithoView lithoView = new LithoView(this);
    final ComponentContext context = new ComponentContext(this);

    final Component text = Text.create(context)
        .text("Hello World")
        .textSizeDip(50)
        .build();
    final ComponentTree componentTree = ComponentTree.create(context, text).build();

    lithoView.setComponentTree(componentTree);
    setContentView(lithoView);
}
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
