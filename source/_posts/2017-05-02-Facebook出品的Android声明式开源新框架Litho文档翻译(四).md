---
title: Facebook出品的Android声明式开源新框架Litho文档翻译(四)
date: 2017-05-02 11:19:18
tags: [Android,实用库,Litho]
category: Android知识
---

# 参考

### Props

Litho使用不可变的输入作为单向数据流提供给Component.Component使用[React](https://facebook.github.io/react/)中提出的名称props,作为它的输入.

##### 定义和使用props

一个给定component中的prop是指在你的spec方法中,所有使用@Prop注释标注的参数的集合.你可以把它作为@Prop的参数声明出来,以在所有的方法中访问它的值.

在多个lifecycle方法中,我们可以定义和访问相同的prop.注释处理器会确保你在所有的spec方法中使用一致的prop类型和注释参数.

以下面的Component Spec为例:

``` java
@MountSpec
class MyComponentSpec {

  @OnPrepare
  static void onPrepare(
      ComponentContext c,
      @Prop(optional = true) String prop1) {
    ...
  }

  @OnMount
  static SomeDrawable onMount(
      ComponentContext c,
      SomeDrawable convertDrawable,
      @Prop(optional = true) String prop1,
      @Prop int prop2) {
    if (prop1 != null) {
    	...
    }
  }
}
```

MyComponentSpec定义了两个Prop,一个String类型的Prop,名叫prop1,和一个int类型的prop,名叫prop2.prop1是可选的,它需要在所有定义它的方法中进行标注,否则注释处理器将会抛出一个异常.

当生命周期方法被调用时,@Prop参数将保存component创建时从它们的父级传递过来的值(或者它们的默认值).

Prop在LayoutSpec和MountSpec中定义和使用的方法都是一样的.

##### 设置Prop
对每一个在spec中定义的独立的prop,注释处理器都会创建一个与prop名称相同的构造器模式的方法在Component的Builder中,用来把传入的参数设置到对应的prop上.

你可以调用自动生成的Component Builder中的对应的方法传入参数来设置prop.

``` java
MyComponent.create(c)
    .prop1("My prop 1")
    .prop2(256)
    .build();
```

##### Prop的默认值

对可选的prop你可以省略设置它的value的动作,它会被自动设置为它这种类型在java中的默认值.你可能也会经常需要明确的定义component prop的默认值为你想要的值,而不是简单的使用java的默认值.

你可以使用@PropDefault注释在Spec类中定义一个静态的成员变量作为prop的默认值.让我们为上面例子中prop定义一个默认值:

``` java
@MountSpec
public class MyComponentSpec {
  @PropDefault static final String prop1 = "mydefaultvalue";
  @PropDefault static final int prop2 = -1;

  ...
}
```


