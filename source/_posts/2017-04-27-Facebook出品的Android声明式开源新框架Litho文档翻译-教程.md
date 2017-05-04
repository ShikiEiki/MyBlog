---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-教程
date: 2017-04-27 16:05:10
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.

# 快速开始

### 教程
本教程已经假定你已经按照入门指南正确设置了Litho.

在本教程中，你将首先使用Litho在屏幕上构建一个基本的"Hello World！"，然后再构建一个"Hello World！" item组成的列表。在这个过程中,你将了解到Litho的构建块：Component和LithoView。你将学习如何设置Component的属性。

##### 1.Hello World
在最初的步骤中,你将显示一个带有"Hello World"的View.

首先,在Application中初始化SoLoader.
``` java
public class SampleApplication extends Application {

  @Override
  public void onCreate() {
    super.onCreate();
    
    SoLoader.init(this, false);
  }
}
```

Litho在后台使用Yoga加载布局.Yoga需要依赖本地库,而我们引入SoLoader来处理加载这些本地库的工作.在此处初始化SoLoader确保你稍后不会引用到那些未加载的库.

另外,如果你想要调试你的Component层级结构,你可以按照其中的步骤安装[Stetho]().

下一步,添加一个Litho中预定义好的Text Component到activity中:
``` java 
@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    final ComponentContext context = new ComponentContext(this);

    final Component component = Text.create(context)
        .text("Hello World")
        .textSizeDip(50)
        .build();

    setContentView(LithoView.create(context, component));
}
```
LithoView是一个可以渲染Component的Android ViewGroup.它是连接Android view和Litho Component的桥梁.上面的例子就把一个展示Text Component的LithoView设置到了activity中的content中.

那么component如何发挥作用呢?让我们看一下这段代码:
``` java
Text.create(context)
    .text("Hello World")
    .textSizeDip(50)
    .build();
```

Text是在com.facebook.litho.widget中定义的核心组件.如你所见,它有诸如text和textSize这样的一系列的属性.我们从React中获得灵感,这些属性我们称之为props.

稍后,你将学习到如何编写自己的component,但是值得注意的是,Text类是由TextSpec类生成的,生成的component类提供了一套API方法来设置props的值.

在示例中,这个Text Component被作为一个单独的子控件添加至LithoView中.你也可以用一个根Component包含许多子Component来替代示例中的做法.在后续的例子中,你将会学习如何这么做.

完成了!让我们运行app,你可以在屏幕上看到这样的显示.

![](/image/20170428092530.png)

虽然并不漂亮,但是这已经是一个好的开端了!

##### 2.你的第一个自定义Component
在本教程的末尾,你将会拥有一个可以滚动的列表,这个列表将循环的显示一个包含标题和副标题的item.简直激动人心!

在教程的本部分中,你将编写一个简单的component作为列表的item.当然,现实世界中的app的item会更加复杂,但是你会在这个示例中学到你今后需要所有基础的知识.

准备好了吗？是时候深入探索并构建该Component了。在Litho中，你可以编写Spec类来声明Component的布局。框架随后会生成底层的Component类供您在代码中创建实例。

您的自定义component将被称为ListItem,它将包含一个标题,和一个在其下方的稍小的副标题.因此,你需要创建一个包含以下内容的ListItemSpec类.

``` java
@LayoutSpec
public class ListItemSpec {

  @OnCreateLayout
  static ComponentLayout onCreateLayout(ComponentContext c) {
  
    return Column.create(c)
        .paddingDip(ALL, 16)
        .backgroundColor(Color.WHITE)
        .child(
            Text.create(c)
                .text("Hello world")
                .textSizeSp(40))
        .child(
            Text.create(c)
                .text("Litho tutorial")
                .textSizeSp(20))
        .build();
  }
}
```

你应该已经认出了之前教程中使用过的Text Component.在这个例子中,你要将其作为一个子属性添加至一个Column中.你可以把Column等同于HTML中的<div>标签.它是一个包装器,用于把组件整合在一起,并且可能会添加一些背景样式.由于Litho使用Yoga,捏可以添加flexbox属性来设置Column或Row的子项的布局.在此处,你只需简单设置padding(填充大小)和背景颜色.

你如何渲染这个Component呢?在你的activity中,简单的修改Component定义为:

``` java 
final Component text = ListItem.create(context).build();
```
注意:你使用的是ListItem,而不是ListItemSpec.

这个ListItem是哪里来的呢?create方法和build方法是在哪里定义的呢?这是Litho Specs的魔力所在.

在入门指南中,我们学习了如何添加依赖至项目中,来使代码生成器能够工作.这会在你的代码上运行一个注释处理器.它会自动查找FooSpec的类名,并且自动生成根据Spec类在同一个包下生成Foo类.Litho将会为这些类自动添加所需的所有方法.此外,根据规则,注释处理器还将生成的额外方法(例如Text的textSizeSp方法或者Column/Row的backgroundColor方法).

就这么简单。运行你的app,你应该看到如下的画面:

![](/image/20170428102947.png)

##### 3.创建一个列表
你可以使用Litho的核心组件Recycler Component来处理列表相关的工作.这个component在概念上类似于Android的RecyclerView,然而,使用Litho,所有的布局计算都是在一个子线程中处理的,这带来了显著的性能提升.在教程的本部分中,你将使用一个RecyclerBinder来为Recycler提供Component,方式与使用LayoutManager与Adapter配合向RecyclerView提供View的方式相同.
首先,在你的activity中,像下面这样定义Component:
``` java
final RecyclerBinder recyclerBinder = new RecyclerBinder(
    context,
    new LinearLayoutInfo(this, OrientationHelper.VERTICAL, false));

final Component component = Recycler.create(context)
    .binder(recyclerBinder)
    .build();
```
这些代码构造了一个RecyclerBinder并且连接它到了一个Recycler上.新的RecyclerBinder使用context和layoutInfo作为构造参数.

然后再创建Recycler并且把它传递给LithoView.

现在,将重点放在使用item填充binder上.让我们定义一个helper方法来做这件事:
``` java
private static void addContent(RecyclerBinder recyclerBinder, ComponentContext context) {
    for (int i = 0; i < 32; i++) {
      recyclerBinder.insertItemAt(
          i,
          ComponentInfo.create()
              .component(ListItem.create(context).build())
              .build());
    }
}    
```

在代码中,我们需要创建一个ComponentInfo来描述Recycler需要展示的component.在本例中,我们需要展示ListItem.

最后,在组件定义工作完成后,在activity的onCreate回调中调用addContent方法.
``` java
addContent(recyclerBinder, context);
```
运行app,你会看见一个可以滚动的具有32个ListItem的列表.

![](/image/20170428105417.png)

##### 4.定义Component的属性
列表如果只是简单的包含同一个component的拷贝就没有意义了.在这个部分,你需要把目光集中到属性,或者说props上.你可以设置很多的属性到Component上来改变它的外观和行为.

为一个component添加props非常的简单.props是componentSpec的方法的参数,使用@Prop定义.

像下面这样修改ListItemSpec:
``` java
@OnCreateLayout
static ComponentLayout onCreateLayout(
    ComponentContext c,
    @Prop int color,
    @Prop String title,
    @Prop String subtitle) {
    
  return Column.create(c)
        .paddingDip(ALL, 16)
        .backgroundColor(color)
        .child(
            Text.create(c)
                .text(title)
                .textSizeSp(40))
        .child(
            Text.create(c)
                .text(subtitle)
                .textSizeSp(20))
        .build();
}
```

这样就添加了3个props:title,subtitle和color.注意现在背景颜色和Text的文字内容不再是写死的了,而是取决于onCreateLayout方法的参数了.

神奇的事就发生在@Prop和注释处理器中,处理器以正确的方法生成符合props的component构造器.你现在可以修改你的binder构造方法如下:
``` java
private void addContent(
    RecyclerBinder recyclerBinder, 
    ComponentContext context) {
  for (int i = 0; i < 32; i++) {
    ComponentInfo.Builder componentInfoBuilder = ComponentInfo.create();
    componentInfoBuilder.component(
        ListItem.create(context)
            .color(i % 2 == 0 ? Color.WHITE : Color.LTGRAY)
            .title("Hello, world!")
            .subtitle("Litho tutorial")
            .build());
    recyclerBinder.insertItemAt(i, componentInfoBuilder.build());
  }
}
```

现在,当ListItem被构建出来时,color,title和subtitle 这些props就被传递进去了来改变每一行的背景颜色.

运行app,你可以看到如下画面:

![](/image/20170428111627.png)

你可以为@Prop注释指定更多的选项.例如下面的属性:
``` java 
@Prop(optional = true, resType = ResType.DIMEN_OFFSET) int shadowRadius,
```

它告诉注释处理器构造一些函数，如shadowRadiusPx，shadowRadiusDip，shadowRadiusSp以及shadowRadiusRes。

恭喜完成本教程！这个基础教程向你介绍了开始使用Litho所需要的所有基础构建块,并且教你构建了自己的Component。你可以在com.facebook.litho.widgets包中找到可以使用的预定义好的组件Component。你可以在[这里]()找到完整的教程。请务必查看[此示例]()和Litho API文档以获取更深入的代码。

[回到导航页](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%80%BB%E8%A7%88%E5%92%8C%E5%AF%BC%E8%88%AA/)