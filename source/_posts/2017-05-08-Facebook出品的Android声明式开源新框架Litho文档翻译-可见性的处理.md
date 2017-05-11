---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-可见性的处理
date: 2017-05-08 16:33:22
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.
英文原文文档地址: [Litho-doc](http://fblitho.com/docs/visibility-handling)
## 事件处理
### 可见性的处理

</p>
</p>

#### 可见范围的类型
---

框架现在支持4中类型过的可见性事件:
- [可见事件](http://fblitho.com/javadoc/com/facebook/litho/VisibleEvent):这种事件会在Component至少有1像素是可见的时候被触发.
- [不可见事件](http://fblitho.com/javadoc/com/facebook/litho/InvisibleEvent):这种事件会在Component不再有任何像素是可见的时候被触发.
- [聚焦可见事件](http://fblitho.com/javadoc/com/facebook/litho/FocusedVisibleEvent):这种事件会在Component至少占据视窗一半的的时候被触发,如果Component的大小小于视窗的一半,则会在Component完全可见的时候被触发.
- [完全展示可见事件](http://fblitho.com/javadoc/com/facebook/litho/FullImpressionVisibleEvent):当整个Component在某个时间点通过视窗时，会触发此事件。

</p>
</p>

#### 使用
---

可见性范围需要相关的Component支持[增量式挂载]()

为一个Component注册可见性事件handler,你可以按照注册其他事件handler相同的[步骤](https://shikieiki.github.io/2017/05/05/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E4%BA%8B%E4%BB%B6%E5%A4%84%E7%90%86%E6%A6%82%E8%BF%B0/)来操作.

下面是示例:

``` java
@LayoutSpec
class MyLayoutSpec {

  @OnCreateLayout
  static ComponentLayout onCreateLayout(ComponentContext c) {
  
    return Column.create(c)
        .alignItems(Align.STRETCH)
        .child(Text.create(c)
            .text("This is MY layout spec")
            .withLayout()
            .visibleHandler(MyLayoutSpec.onTitleVisible(c))
            .invisibleHandler(MyLayoutSpec.onTitleInvisible(c)))
        .focusedHandler(MyLayoutSpec.onComponentFocused(c, "someStringParam"))
        .fullImpressionHandler(MyLayoutSpec.onComponentFullImpression(c)))
        .build();
  }

  @OnEvent(VisibleEvent.class)
  static void onTitleVisible(ComponentContext c) {
    Log.d("VisibilityRanges", "The title entered the Visible Range");
  }

  @OnEvent(InvisibleEvent.class)
  static void onTitleInvisible(ComponentContext c) {
    Log.d("VisibilityRanges", "The title is no longer visible");
  }

  @OnEvent(FocusedVisibleEvent.class)
  static void onComponentFocused(
      ComponentContext c,
      @Param String stringParam) {
    Log.d(
      "VisibilityRanges",
      "The component is focused with param: " + contentString);
  }

  @OnEvent(FullImpressionVisibleEvent.class)
  static void onComponentFullImpression(ComponentContext c) {
    Log.d("VisibilityRanges", "The component has logged a full impression");
  }
};
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