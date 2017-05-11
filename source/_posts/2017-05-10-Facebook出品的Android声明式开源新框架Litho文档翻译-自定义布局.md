---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-自定义布局
date: 2017-05-10 11:01:27
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.
英文原文文档地址: [Litho-doc](http://fblitho.com/docs/custom-layout)
## 进阶指引
### 自定义布局

</p>
</p>

Litho依赖[Yoga](https://facebook.github.io/yoga/)(一个可以创建复杂UI的强大的布局引擎)来进行布局计算.然而,也会存在一些例外情况,这些时候只使用Yoga还不够,你可能还需要自己实现测量和布局的工作.

Litho提供了一套自定义布局的API,用来在[ComponentTree](http://fblitho.com/javadoc/com/facebook/litho/ComponentTree)被创建的和被测量时访问尺寸信息,或者在一个隔离的环境中测量Component.

**重要,请注意**:这些API会造成**不可忽略**的性能开销,因此,我们建议仅仅在确定有必要使用它们的时候再去使用它们.

</p>
</p>

#### 用例
---

- **一个Component布局树依赖于它自己的大小 和/或 它的孩子的大小**.例如这种情况:一个Component布局只能在它的孩子们的大小符合父亲的限制的时候才使用它们,如果不符合,这个布局就需要使用作为后备的孩子来代替.
- **一个容器的孩子们必须根据它们的大小或者它们父亲的大小进行绝对位置的手动定位**.Yoga可以绝对定位孩子在父亲中的位置.然而,这个位置本身可能取决于使用父母的大小限制计算后的孩子的大小尺寸.如果需要,margin或者padding也需要手动被计算在内.

</p>
</p>

#### 大小约束
---

在使用API之前,你需要已经熟悉了传统Android View的onMeasure方法并且了解什么是MeasureSpec,因为Litho使用了一个类似的概念,名叫SizeSpec.

类似于Android的MeasureSpec,Litho的SizeSpec也是由size和mode组成的.可用的mode与MeasureSpec一样,有三种:UNSPECIFIED,EXACTLY和AT_MOST.

</p>
</p>

#### 测量一个Component
---
我们可以使用给定的SizeSpec在隔离环境下测量Component.结果将会被填充进一个作为参数传递进去的Size对象中.

在下面的例子中,我们使用一个UNSPECIFIED的SizeSpec去测量一个Text Component,这表示文字的一行能够是无限长的.

``` java
final Component<Text> textComponent = Text.create(c)
    .textSizeSp(16)
    .text(“Some text to measure.”)
    .build();

final Size outputSize = new Size();
textComponent.measure(
    c, 
    SizeSpec.makeSizeSpec(0, UNSPECIFIED),
    SizeSpec.makeSizeSpec(0, UNSPECIFIED),
    outputSize);

final int textComponentWidth = outputSize.width;
final int textComponentHeight = outputSize.height;
```

</p>
</p>

#### SizeSpec
---

在布局创建期间,API可以提供Component将要被测量时使用的SizeSpec信息.为了获取这些信息,我们需要用一个新的@OnCreateLayoutWithSizeSpec注释替代原来的@OnCreateLayout.新的注释方法的参数,除了标准的ComponentContext,还有两个整型,它们分别代表width spec和height spec.

在下面的例子中,我们测量了一个Text Component来检查给定的文字是否适合可用的空间.如果不适合,则使用一个Image Component代替Text Component.

``` java
@LayoutSpec
class MyComponentSpec {

  @OnCreateLayoutWithSizeSpec
  static ComponentLayout onCreateLayoutWithSizeSpec(
      ComponentContext c,
      int widthSpec,
      int heightSpec,
      @Prop SomeObject someProp) {

    final Component<Text> textComponent = Text.create(c)
        .textSizeSp(16)
        .text(“Some text to measure.”)
        .build();

    // UNSPECIFIED sizeSpecs will measure the text as being one line only,
    // having unlimited width.
    final Size textOutputSize = new Size();
    textComponent.measure(
        c, 
        SizeSpec.makeSizeSpec(0, UNSPECIFIED),
        SizeSpec.makeSizeSpec(0, UNSPECIFIED),
        textOutputSize);

    // Small component to use in case textComponent doesn’t fit within
    // the current layout.
    final Component<Image> imageComponent = Image.create(c)
        .srcRes(R.drawable.some_icon)
        .build();

    // Assuming SizeSpec.getMode(widthSpec) == EXACTLY or AT_MOST.
    final int layoutWidth = SizeSpec.getSize(widthSpec);
    final boolean textFits = (textOutputSize.width <= layoutWidth);
    
    return Layout.create(c, textFits ? textComponent : imageComponent)
        .build();
  }
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