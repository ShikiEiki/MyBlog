---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-最佳实践
date: 2017-05-10 16:23:58
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.
英文原文文档地址: [Litho-doc](http://fblitho.com/docs/best-practices)
## 最佳实践

</p>
</p>

#### 编码风格
---
指引:
- 使用NAMEComponentSpec命名你的Spec来生成一个名叫NAMEComponent的Component.
- ComponentContext参数应该被命名为c,这样能避免你的布局代码变得冗长并且能够使它变得可读性更强.
- 在适当的地方使用资源类型(ResType.STRING, ResType.COLOR, ResType.DIMEN_SIZE等等)能够让用Android资源设置prop变得更简单.
- 先定义所有必须的prop然后再定义可选的prop(optional=ture).
- 使用静态导入所有的enum(YogaEdge,YogaAlign,YogaJustify等等)来减少你的布局代码,并使其变得可读性更强.
- 对withLayout()下的prop不使用额外的缩进等级
- 生命周期方法,例如@OnCreateLayout,是静态的并且对包外对象是私有的.
- 如果可能的话,对可选的孩子Component使用内联选项,这样能使你的布局结构变得更加流畅.
- 如果你正在构建一个孩子容器,在下一行添加该容器.这使布局代码显得更加结构化.

使用推荐编码风格编码的实例代码如下:

``` java
@LayoutSpec
class MyComponentSpec {

  @OnCreateLayout
  static ComponentLayout onCreateLayout(
      ComponentContext c,
      @Prop(resType = STRING) String title,
      @Prop(optional = true) Drawable image) {

  return Row.create(c)
      .alignItems(CENTER)
      .paddingRes(R.dimen.some_dimen)
      .child(
          Image.create(c)
              .drawable(image)
              .withLayout()
              .width(40)
              .height(40)
              .marginRes(RIGHT, R.dimen.my_margin))
      .child(TextUtils.isEmpty(title) ? null :
          Text.create(c)
              .text(title)
              .textColorAttr(R.attr.textColorTertiary)
              .withLayout()
              .marginDip(5)
              .flexGrow(1f))
      .build();
  }
}
```
</p>
</p>

#### Prop vs State
---
Litho Component一共有两种类型的数据模型:[Prop](https://shikieiki.github.io/2017/05/02/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-Props/)和[State](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-State/).在你需要使用它们的时候,明白两种模型的差别是非常重要的.

Prop的作用是在树中把数据从Component传递给它的孩子们.Prop在定义一个Component模型的静态部分中是非常有用的,因为Prop是不可变的.

State主要是用于处理那些与Component交互产生的更新或者那些只能被Component拦截到的更新.State是由组件自己管理的并且从外部是不可见的,Component的父级完全不知道它孩子的State的信息.

以下是关于如何决定对Component中的某些数据是采用prop还是State的简要介绍:
- 数据是否定义了一个属性并且值会保持不变?如果是,则它应该是一个Prop.
- 数据是否应该由父级定义并且传递下来?如果是,则它应该是一个Prop.
- 它是否应该在用户与这个Component交互后改变它的值(例如点击)?如果是,它应该是一个State.
- 你是否能够通过其他已有的Prop和State的值来计算出它的值?如果是,你就不应该使用State.

比起使用由上至下传递的Prop的方式,使用更多State的方式会让你的应用程序的复杂度升高,变得更加难以维护和更加难以推导出数据流.你应该尝试控制你的Compoennt中的State在最小的数量并且保持的数据流是从上到下的.如果你有多个兄弟Component并且他们的State是互相依赖的,这时你应该确定一个父级Component来替代孩子们持有并且管理它们的state.

让我们举个拥有单选按钮的列表的例子,列表中不能同时选中多个项.列表中所有的单选按钮的state都取决于其他项被点击和选中的状态.比起让所有的单选按钮都使用state,你应该在父Component中持有"哪一个列表项被点击"的state,并且通过prop从上而下的把这个信息传递给它的孩子们.

</p>
</p>

#### 不可变性
---

Component本质上是一系列方法的集合,数据通过不可变的参数的方式传递给这些方法.当Component的prop或者state更新后,框架将会使用更新后的信息创建一个新的Component实例,因为先前的Component是不能改变的.

虽然Component本身是不可变的,但是使用可变的对象来表示prop和state很容易就会使Component变得线程不安全.Litho[在后台线程中计算布局](https://shikieiki.github.io/2017/04/27/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E5%BC%82%E6%AD%A5%E5%B8%83%E5%B1%80/),如果构成Component的Prop或者State的对象在另一个线程中被改变了,将会导致同样的Component渲染出不一样的效果.

你必须保证你的prop和state是使用天生不可变的原始类型,如果做不到的话,也必须确保你使用的对象是不可变的.

</p>
</p>
</p>
</p>

##### [回到导航页](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%80%BB%E8%A7%88%E5%92%8C%E5%AF%BC%E8%88%AA/)
</p>
</p>
</p>
</p>