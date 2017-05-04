---
title: Facebook出品的Android声明式开源新框架Litho文档翻译--Props
date: 2017-05-02 11:19:18
tags: [Android,实用库,Litho]
category: Android知识
---

# 参考

欢迎转载,转载请标明出处.

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

##### 资源类型

当创建布局的时候,经常会使用到Android资源系统中的值,例如dimension,color,string等等.Component框架提供了一个非常方便的方法使用注释来设置Android资源系统里的值到prop中.

让我们看一个简单的例子:
``` java
@LayoutSpec
public class MyComponentSpec {

  @OnCreateLayout
  static ComponentLayout onCreateLayout(
      LayoutContext context,
      @Prop CharSequence someString,
      @Prop int someSize,
      @Prop int someColor) {
    ...
  }
}
```

在上面的例子中,MyComponent拥有几个prop,它们预计会被设置为一个代表color的integer(someColor),一个代表像素的dimension(someSize)和一个String(someString).通常的,你需要使用资源的值来设置它们:
``` java
Resources res = context.getResources();

MyComponent.create(c)
    .someString(res.getString(R.string.my_string))
    .someSize(res.getDimensionPixelSize(R.dimen.my_dimen))
    .someColor(res.getColor(R.color.my_color))
```

而component 框架允许你使用资源类型来注释你的prop,以便你的component builder可以生成更加方便的方法来供你直接使用资源的值.

``` java
@LayoutSpec
public class MyComponentSpec {

  @OnCreateLayout
  static ComponentLayout onCreateLayout(
      LayoutContext context,
      @Prop(resType = ResType.STRING) CharSequence someString,
      @Prop(resType = ResType.DIMEN_SIZE) int someSize,
      @Prop(resType = ResType.COLOR) int someColor) {
    ...
  }
}
```

当你做了以上的更改后,MyComponent的builder将会根据被注释的prop的资源类型自动包含响应的Res,Attr,Dip,Px方法.因此你就可以像下面这么做:
``` java
MyComponent.create(c)
    .someStringRes(R.string.my_string)
    .someSizePx(10)
    .someSizeDip(10)
    .someColorAttr(android.R.attr.textColorTertiary)
```

其他支持的资源类型包括 ResType.STRING_ARRAY, ResType.INT, ResType.INT_ARRAY, ResType.BOOL, ResType.COLOR, ResType.DIMEN_OFFSET, ResType.FLOAT, and ResType.DRAWABLE.

##### 不可变性
Component中的prop是只读的.当Component的父级创建Component时传递给它的prop响应的值之后,在component的生命周期中,它们的值就不能被改变了.如果prop的值必须被更新,那么它的父级创建一个新的Component并且传递将新的值传递给它的prop.prop对象必须保证是不可变的.因为在[后台布局计算]()中prop可能被多个线程同时访问.Prop的不可变性能够确保在你的component层级中不会出现线程安全问题.


[回到导航页](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%80%BB%E8%A7%88%E5%92%8C%E5%AF%BC%E8%88%AA/)