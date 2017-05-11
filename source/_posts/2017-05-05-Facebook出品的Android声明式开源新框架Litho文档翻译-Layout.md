---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-Layout
date: 2017-05-05 14:30:13
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.
英文原文文档地址: [Litho-doc](http://fblitho.com/docs/layout)
## 参考
### Layout(布局)

</p>
</p>

Litho使用[Yoga](https://facebook.github.io/yoga/),一种[Flexbox](https://www.w3.org/TR/css-flexbox-1/)的实现来测量Component和在屏幕上布局.如果你以前已经在web端使用过Flexbox,那么你使用Litho时将会非常熟悉.如果你更加熟悉Android普通的布局的工作方式,那么Flexbox常常会让你想起LinearLayout.

在Litho中你可以使用一个Row(行)来实现一个类似于水平的LinearLayout的布局.
``` java
Row.create(c)
    .child(...)
    .child(...)
    .build();
```

或者使用一个Column(列)来实现一个类似于竖直LinearLayout的布局.
``` java
Column.create(c)
    .child(...)
    .child(...)
    .build();
```

为了实现类似于LinearLayout中的weight的效果,Flexbox提供了一个叫做flexGrow(<weight>)的概念.
``` java
Row.create(c)
    .child(
        SolidColor.create(c)
            .color(RED)
            .withLayout()
            .flexGrow(1))
    .child(
        SolidColor.create(c)
            .color(BLUE)
            .withLayout()
            .flexGrow(1))
    .build();
```
如果你比较喜欢FrameLayout中把一个view放置到其他view的上方的效果,Flexbox中可以使用positionType(ABSOLUTE)来实现.
``` java
Row.create(c)
    .child(
        SolidColor.create(c)
            .color(RED)
            .withLayout()
            .flexGrow(1))
    .child(
        SolidColor.create(c)
            .color(BLUE)
            .withLayout()
            .flexGrow(1))
    .build();
```

如果需要查看更多关于Flexbox特性的文档,你可以查阅[Yoga文档](https://facebook.github.io/yoga/docs/getting-started/)或者其他任何讲述Flexbox如何工作的网络资源.

</p>
</p>
</p>
</p>

##### [回到导航页](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%80%BB%E8%A7%88%E5%92%8C%E5%AF%BC%E8%88%AA/)
</p>
</p>
</p>
</p>