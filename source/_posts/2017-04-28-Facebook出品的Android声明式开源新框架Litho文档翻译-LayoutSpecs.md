---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-LayoutSpecs
date: 2017-04-28 14:12:50
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.
英文原文文档地址: [Litho-doc](http://fblitho.com/docs/layout-specs)
## 参考
### Layout Specs

</p>
</p>

Layout Spec在逻辑上等同于Android的View的组合.它简单的把一些已经存在的component组合到一个不可变的布局树中.

实现一个layout spec非常简单:你只需要写一个标注为@OnCreateLayout的方法,并且让它返回一个不可变的[ComponentLayout](http://fblitho.com/javadoc/com/facebook/litho/ComponentLayout)对象的树.

让我们从一个简单的例子开始:
``` java
@LayoutSpec
public class MyComponentSpec {
  @OnCreateLayout
  static ComponentLayout onCreateLayout(
      ComponentContext c,
      @Prop int color,
      @Prop String title) {

      return Row.create(c)
          .alignItems(CENTER)
          .child(
              SolidColor.create(c)
                  .colorRes(color)
                  .withLayout()
                  .widthDip(40)
                  .heightDip(40))
          .child(
              Text.create(c)
                  .text(title)
                  .textSizeRes(R.dimen.my_text_size)
                  .withLayout()
                  .flexGrow(1f))
          .build();
  }
}
```

正如你所见,layout spec类使用@LayoutSpec注释.

用@OnCreateLayout注释标注的方法必须以[ComponentContext](http://fblitho.com/javadoc/com/facebook/litho/ComponentContext)作为第一个参数,并且在它后面有以@Prop标注的其他参数.注释处理器将会在编译的时候对它们和其他API的不变性进行验证.

在上面的示例中,布局树中有一个根容器,容器中有两个水平堆叠(Row.create)且垂直居中(Align.CENTER)的子节点.

第一个子节点是一个[SolidColor](http://fblitho.com/javadoc/com/facebook/litho/widget/SolidColor) component,它拥有一个colorRes的Prop和40dp的宽和高.

``` java
SolidColor.create(c)
    .uri(imageUri)
    .withLayout()
    .width(40)
    .height(40)
```

第二个子节点是一个[Text](http://fblitho.com/javadoc/com/facebook/litho/widget/Text) Component,它拥有一个名叫text的prop,并且使用grow(1f)填充了Myconponent中的剩余的水平空间(等同于Android LinearLayout中的layoutWeight).文字大小实在my_text_size尺寸文件中定义的.
``` java
Text.create(c)
    .text(title)
    .textSizeRes(R.dimen.my_text_size)
    .withLayout()
    .grow(1f)
```

你可以查看完整的[Yoga](https://facebook.github.io/yoga/docs/learn-more/)文档来获取所有框架开放出的布局特性.

</p>
</p>
</p>
</p>

[回到导航页](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%80%BB%E8%A7%88%E5%92%8C%E5%AF%BC%E8%88%AA/)

</p>
</p>
</p>
</p>