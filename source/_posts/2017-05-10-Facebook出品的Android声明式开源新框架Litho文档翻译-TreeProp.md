---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-TreeProp
date: 2017-05-10 11:47:43
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.
英文原文文档地址: [Litho-doc](http://fblitho.com/docs/tree-props)
## 进阶指引
### TreeProp

</p>
</p>

@TreeProp是那些自动并且静默的从父Component传递给子Component的[prop](https://shikieiki.github.io/2017/05/02/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-Props/).

TreeProp非常方便,它能够在一个布局树上分享上下文数据或者工具而又不需要显式的传递prop给每一个层级结构上的Component.

预加载器就是一个很好的例子,它能够在渲染图片之前从网络上预抓取图片.由于图片在程序中非常常见,预加载器也被广泛的应用.我们可以不用@Prop把预加载器传递给整个布局树,而是把预加载器的实现定义给那些需要使用它的Component.

</p>
</p>

#### 声明一个TreeProp
---
每一个TreeProp都是在一个被注释为@OnCreateTreeProp的方法中被声明和定义的.

``` java
@LayoutSpec
public class ParentComponentSpec {
  @OnCreateTreeProp
  static Prefetcher onCreatePrefetcher(
      ComponentContext c,
      @Prop Prefetcher prefetcher) {
      
    return prefetcher;
  }
  
  @OnCreateLayout
  static ComponentLayout onCreateLayout(
      ComponentContext c,
      @Prop Uri imageUri) {
      
    return ChildComponent.create(c)
        .imageUri(imageUri)
        .buildWithLayout();
  }
}
```

你只能为任何给定类型声明一个@TreeProp.如果一个ParentComponent的子类也定义了一个Prefetcher类型的@TreeProp,它将会覆写它的所有子类的相应的@TreeProp值(但是不包括它自己的值).

</p>
</p>

#### 使用一个TreeProp
---
孩子component可以使用@TreeProp注释来声明一个和父亲Component的@OnCreateTreeProp方法中同样类型的参数的方式来访问TreeProp的值.

``` java
@LayoutSpec
class ChildComponentSpec {
  @OnCreateLayout
  static ComponentLayout onCreateLayout(
      ComponentContext context,
      @TreeProp Prefetcher prefetcher,
      @Prop Uri imageUri) {
    if (prefetcher != null) {
      prefetcher.prefetch(imageUri);
    }
    ...
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