---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-事件处理概述
date: 2017-05-05 16:14:50
tags: [Android,实用库,Litho]
category: Android知识
---


欢迎转载,转载请标明出处.
英文原文文档地址: [Litho-doc](http://fblitho.com/docs/events-overview)
## 事件处理
### 概述

</p>
</p>

框架提供了一套多用途的API用来在事件发生的时候与Component通信.事件被声明成一个POJO并且使用@Event注释.为了方便,我们命名事件类时使用Event作为后缀.事件的类不一定必须是LayoutSpec或者MountSpec的内部类.这是因为从设计上看,Spec一般被认为是私有的概念,而事件却可以允许被多个Component使用.
``` java
@Event
public class ColorChangedEvent {
  public int color;
}
```
在这个例子中我们假设我们有一个Component名叫ColorComponent.为了指明一个ColorComponent可以分发ColorChangedEvent事件,我们的ColorComponentSpec必须在注释中注明这一点.注明的方式是使用@MountSpec或者@LayoutSpec注释中的events参数.一个Component的注释可以分派多个事件.

``` java
@LayoutSpec(events = { ColorChangedEvent.class })
class ColorComponentSpec {
  ...
  @OnCreateLayout
  static ComponentLayout onCreateLayout(
      Context c,
      @Prop EventHandler colorChangedHandler,
      @FromPrepare int color) {
    ...
    ColorComponent.dispatchColorChangedEvent(
        colorChangedHandler,
        color);
    ...
  }
}
```

对一个FooEvent类型的事件,将会自动生成一个对应的dispatchFooEvent方法和一个供事件回调使用的事件标识符.

dispatchFooEvent方法使用一个[EventHandler](http://fblitho.com/javadoc/com/facebook/litho/EventHandler)作为第一个参数,它后面紧跟着的参数是你在@Event类中定义的参数的列表.我们规定为每一个你的Component暴露出的事件使用一个EventHandler prop.

在上面的例子中,ColorComponent使用一个colorChangedHandler作为prop,并且使用自动生成的dispatchColorChangedEvent()方法把ColorChangedEvent分发给它.

</p>
</p>
#### 回调
---
为了处理其他Component分发过来的事件,你需要一个EventHandler实例和一个匹配的回调.

你可以使用自动生成的Component中的相对应的eventHandler factory方法来创建EventHandler实例.这个方法的名称将会和你的事件回调方法的名称相同.

你可以使用@OnEvent注释定义事件回调.@OnEvent将接受一个参数:事件类.这个方法的第一个参数必须是一个ComponentContext,框架将会为你填充它.

举例来说,下面是一个Component如何为上面声明的ColorChangedEvent定义一个handler.

``` java
@LayoutSpec
class MyComponentSpec {

  @OnCreateLayout
  static ComponentLayout onCreateLayout(
      LayoutContext c,
      @Prop String someColor) {

    return Column.create(c)
        ...
        .child(
            ColorComponent.create(c)
                .color(someColor)
                .colorChangedHandler(MyComponent.onColorChanged(c))
        ...
        .build();

  }

  @OnEvent(ColorChangedEvent.class)
  static void onColorChanged(
      ComponentContext c,
      @FromEvent int color,
      @Prop String someProp) {
    Log.d("MyComponent", "Color changed: " + color);
  }
}
```
对一个或者多个回调方法的参数使用@Param注释,你可以定义动态的事件参数.如果你想要定义一个确定类型的事件的回调(例如onAvatarClicked()),但是你也想知道哪一个头像被点击了,使用@Param注释会变得非常实用.这个情况中的avatar参数将会被传递到eventHandler factory方法中.

正如你所见,@OnEvent回调可以访问所有的Component的prop,就像其他的Spec方法一样.
``` java
@LayoutSpec
class FacePileComponentSpec {

  @OnCreateLayout
  static ComponentLayout onCreateLayout(
      LayoutContext c,
      @Prop Uri[] faces) {
    ComponentLayout.Builder builder = Column.create(c);
    for (Uri face : avatarUrls) {
      builder.child(
          FrescoImage.create(c)
              .uri(face)
              .withLayout()
              .clickHandler(FacePileComponent.onFaceClicked(c, face));
    }
    
    return builder.build();
  }

  @OnEvent(ClickEvent.class)
  static void onFaceClicked(
      ComponentContext c,
      @Param Uri face) {
    Log.d("FacePileComponent", "Face clicked: " + face);
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