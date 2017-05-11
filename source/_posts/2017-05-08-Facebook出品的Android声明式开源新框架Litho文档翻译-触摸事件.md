---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-触摸事件
date: 2017-05-08 16:15:18
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.
英文原文文档地址: [Litho-doc](http://fblitho.com/docs/events-touch-handling)
## 事件处理
### 触摸事件处理

</p>
</p>


所有的Component都支持通过框架的事件系统处理触摸事件.所有的Component都默认支持处理下列几种事件:ClickEvent(点击事件),LongClickEvent(长按事件)和TouchEvent(触摸事件).

这意味着所有的布局builder都分别拥有叫做clickHandler,longClickHandler和touchHandler的EventHandler prop.你可以在你的@OnEvent注释里把你想要处理的事件类作为参数指定出来.

举例来说,在任意一个Component指定点击handler都非常简单:

``` java
@LayoutSpec
class MyComponentSpec {

	@OnCreateLayout
	static ComponentLayout onCreateLayout(
	    ComponentContext c,
	    @Prop String title) {
	    
	  return Text.create(c)
	      .text(title)
	      .withLayout()
	      .clickHandler(MyComponent.onClick(c))
	      .build();
    }
}
```

并且在MyComponentSpec中的回调可以写成这样:

``` java
@LayoutSpec
class MyComponentSpec {
... 
    @OnEvent(ClickEvent.class)
    static void onClick(
        ComponentContext c,
        @FromEvent View view,
        @Prop String someProp) {
        // Handle click here.
    }
}
```

</p>
</p>

#### 触摸范围扩大
---
你可以使用布局builder中的触摸扩展API来扩展可交互的范围边界.

``` java
Text.create(c)
    .text(title)
    .withLayout()
    .clickHandler(MyComponent.onClick(c))
    .touchExpansionDip(ALL, 10);
```
在这个例子中,text Component的可点击范围比Component的所有边界(上,下,左,右)都扩展了10dip.

</p>
</p>
</p>
</p>

##### [回到导航页](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%80%BB%E8%A7%88%E5%92%8C%E5%AF%BC%E8%88%AA/)
</p>
</p>
</p>
</p>

