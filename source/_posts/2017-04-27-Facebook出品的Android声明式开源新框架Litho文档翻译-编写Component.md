---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-编写Component
date: 2017-04-27 16:05:10
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.
英文原文文档地址: [Litho-doc](http://fblitho.com/docs/writing-components)
## 快速开始
### 编写Component

</p>
</p>

#### Component Specs
---
一个Component Spec可以生成一个你在UI中使用的Component.有两种类型的Component Spec:
- Layout spec: 可以结合其他component至一个特定的布局中.类似于Android中的ViewGroup.
- Mount spec: 一个可以渲染view或者drawable的的组件.

现在，我们来看一下布局spec的整体结构：

``` java
@LayoutSpec
class MyComponentSpec {

  @OnCreateLayout
  static ComponentLayout onCreateLayout(
      ComponentContext c,
      @Prop String title,
      @Prop Uri imageUri) {
    ...
  }
}
```
有几件事需要注意:
- Component Spec只是有着特殊注释的普通java类.
- Component Spec是完全无状态的,并且不包含任何的成员变量.
- 带有@Prop注释的参数将会自动的添加到Component构造器中.
- 为了能从Component Spec自动生成Component,你需要添加Litho注释处理器至你的BUCK或者Gradle文件中.请参阅[入门指南](),了解如何做到这一点.你可以通过向类注释添加isPublic=false来使生成的类变为private的.

</p>
</p>

#### Spec,生命周期和Component类
---
一个Component Spec子类将被处理用于生成一个[ComponentLifecycle](http://fblitho.com/javadoc/com/facebook/litho/ComponentLifecycle)的子类,这个子类的名字将会是Spec的名字去掉soec后缀.例如,MyComponentSpec将会生成MyComponent类.

这个生成的ComponentLifeCycle类就是今后你会在你的产品中[使用](https://shikieiki.github.io/2017/04/27/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E4%BD%BF%E7%94%A8Component/)的类.而Spec类将在运行的时候在新生成的代码里被用作一个代表类.

生成的新类暴露出来的唯一的API是一个create（...）方法，它为您在Spec类中声明的@Props返回相应的Component.Builder。

在运行的时候,同一种类型的component的所有实例都共享相同的componentLifecycle引用.这意味着一个spec实例对应的是一种component类型,而不是一个component实例.

</p>
</p>
</p>
</p>

[回到导航页](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%80%BB%E8%A7%88%E5%92%8C%E5%AF%BC%E8%88%AA/)

</p>
</p>
</p>
</p>