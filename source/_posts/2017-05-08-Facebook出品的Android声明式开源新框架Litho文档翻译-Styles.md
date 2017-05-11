---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-Styles
date: 2017-05-08 17:36:02
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.
英文原文文档地址: [Litho-doc](http://fblitho.com/docs/styles)
## 兼容性
### Styles

</p>
</p>

Component可以像Android View的属性集的构建器一样,使用Android的style资源来构建它的prop.它让开发者能够直接使用style资源来定义静态的prop值或者prop的默认值.

可以通过在你的Component的Spec中实现@OnLoadStyle方法来支持Style.它的第一个参数是ComponentContext,你可以用它来检索生成一个包含stype资源的TypedArray.其他的参数需要是Output类型,并且名称与类型都与你想要设置的prop相同.

``` java
@LayoutSpec
class MyComponentSpec {

  @OnCreateLayout
  static ComponentLayout onCreateLayout(
      ComponentContext c,
      @Prop String prop1,
      @Prop int prop2) {

    return ...;
  }
}
```
举个例子,为了对上面的MyComponent的两个prop实现style的支持,你先要按照惯例定义样式属性:

``` java
<?xml version="1.0" encoding="utf-8"?>
<resources>

  <attr name="prop1" format="string" />
  <attr name="prop2" format="integer" />

  <declare-styleable name="MyComponent">
    <attr name="prop1" />
    <attr name="prop2" />
  </declare-styleable>

</resources>
```

接着你可以在你的@OnLoadStyle方法中收集这些属性的值:
``` java
@OnLoadStyle
void onLoadStyle(
    ComponentContext c,
    Output<String> prop1,
    Output<Integer> prop2) {

  final DataBoundTypedArray a =
      c.obtainDataBoundAttributes(R.styleable.Text, 0);

  for (int i = 0, size = a.getIndexCount(); i < size; i++) {
    final int attr = a.getIndex(i);

    if (attr == R.styleable.MyComponent_prop1) {
      prop1.set(a.getString(attr));
    } else if (attr == R.styleable.MyComponent_prop2) {
      prop2.set(a.getInteger(attr));
    }
  }

  a.recycle();
}
```

这样,你就可以在style中定义prop1和prop2的值了:
``` java
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <style name="SomeStyle">
        <item name="prop1">@string/some_string</item>
    </style>
</resources>
```

并且在MyComponent中使用它:
``` java
MyComponent.create(c, 0, R.style.SomeStyle)
    .prop2(10)
    .build();
```

这样,prop1就能够从@string/some_string资源中获取值了.


</p>
</p>
</p>
</p>

##### [回到导航页](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%80%BB%E8%A7%88%E5%92%8C%E5%AF%BC%E8%88%AA/)
</p>
</p>
</p>
</p>