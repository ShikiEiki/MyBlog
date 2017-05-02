---
title: Facebook出品的Android声明式开源新框架Litho文档翻译(三)
date: 2017-04-28 14:12:50
tags: [Android,实用库,Litho]
category: Android知识
---

# 参考

### Layout Specs
Layout Spec在逻辑上等同于Android的View的组合.它简单的把一些已经存在的component组合到一个不可变的布局树中.

实现一个layout spec非常简单:你只需要写一个标注为@OnCreateLayout的方法,并且让它返回一个不可变的[ComponentLayout]()对象的树.

让我们从一个简单的例子开始:
``` java
@LayoutSpec
public class MyComponentSpec {
  @OnCreateLayout
  static ComponentLayout onCreateLayout(
      ComponentContext c,
      @Prop int color,
      @Prop String title) {

      return Row.create(c)
          .alignItems(CENTER)
          .child(
              SolidColor.create(c)
                  .colorRes(color)
                  .withLayout()
                  .widthDip(40)
                  .heightDip(40))
          .child(
              Text.create(c)
                  .text(title)
                  .textSizeRes(R.dimen.my_text_size)
                  .withLayout()
                  .flexGrow(1f))
          .build();
  }
}
```

正如你所见,layout spec类使用@LayoutSpec注释.

用@OnCreateLayout注释标注的方法必须以[ComponentContext]()作为第一个参数,并且在它后面有以@Prop标注的其他参数.注释处理器将会在编译的时候对它们和其他API的不变性进行验证.

在上面的示例中,布局树中有一个根容器,容器中有两个水平堆叠(Row.create)且垂直居中(Align.CENTER)的子节点.

第一个子节点是一个[SolidColor]() component,它拥有一个colorRes的Prop和40dp的宽和高.

``` java
SolidColor.create(c)
    .uri(imageUri)
    .withLayout()
    .width(40)
    .height(40)
```

第二个子节点是一个Text Component,它拥有一个名叫text的prop,并且使用grow(1f)填充了Myconponent中的剩余的水平空间(等同于Android LinearLayout中的layoutWeight).文字大小实在my_text_size尺寸文件中定义的.
``` java
Text.create(c)
    .text(title)
    .textSizeRes(R.dimen.my_text_size)
    .withLayout()
    .grow(1f)
```

你可以查看完整的[Yoga]()文档来获取所有框架开放出的布局特性.


### Mount Specs
一个Mount Spec定义了一个可以渲染Views或者drawables的component.

Mount Spec只有在你需要把自己的view/drawable集成到Component 框架中的时候才应当被创建.这里的Mount的意思是指布局树中所有的component执行的操作,用于提取它们的渲染状态(一个View或者一个Drawable)以供显示.

Mount spec类应该使用@MountSpec去注释,并且至少实现一个@OnCreateMountContent方法.下方其他列出的方法是可选择实现的.

mount spec component的生命周期如下:
- 在布局计算之前,运行@OnPrepare一次
- 在布局计算过程中,可选择的运行OnMeasure.
- 在布局计算之后,运行@OnBoundsDefined一次.
- 在component添加到托管视图之前,运行@OnCreateMountContent
- 在component添加到托管视图之前,运行@OnMount
- 在component添加到托管视图之后,运行@OnBind
- 在从托管视图移除component之前,运行@OnUnBind
- 在从托管视图移除component之前,可选择的运行@OnUnmount

##### 挂载
让我们从一个简单的ColorComponent开始,它有一个prop表示颜色名,并且装载它自己的ColorDrawable.
``` java
@MountSpec
public class ColorComponentSpec {

  @OnCreateMountContent
  static ColorDrawable onCreateMountContent(ComponentContext c) {
    return new ColorDrawable();
  }

  @OnMount
  static void onMount(
      ComponentContext context,
      ColorDrawable colorDrawable,
      @Prop String colorName) {
    colorDrawable.setColor(Color.parseColor(colorName));
  }
}
```
- 挂载操作的API与Android的RecyclerView Adapter非常相似.它有一个onCreateMountContent方法在回收池为空的时候创建和初始化View/Drawable内容,以及一个onMount方法能够根据当前的信息更新回收内容.
- onCreateMountContent的返回类型应该始终和onMount的第二个参数的类型相一致。它必须为View或Drawable的子类。这在编译时由注释处理器去验证。
- 挂载总是发生在主线程中因为它可能需要处理Android Views(它们被绑定在主线程中).
- onCreateMountContent不能使用@Prop或任何其他带注释的参数。
- 鉴于@OnMount方法始终在UI线程中运行，因此不应执行耗时的操作。

##### 阶段之间的输入和输出
你可以通过把重操作(耗时操作)移动到@OnPrepare方法中,来减轻UI线程的压力.这个方法只会在布局计算前执行一次,并且可以在后台线程中执行.

假设现在我们需要将在UI线程之外解析得到的颜色名称应用到ColorComponent中.为了做到这一点,我们需要一种将@OnPrepare方法中生成的值传递给@OnMount方法的途径.Component框架提供了阶段间的输入和输出,使你能够做到这一点.

让我们看看ColorComponent的@OnPrepare方法:
``` java
@MountSpec
public class ColorComponentSpec {

  @OnPrepare
  static void onPrepare(
      Context context,
      @Prop String colorName,
      Output<Integer> color) {
    color.set(Color.parseColor(colorName));
  }

  @OnCreateMountContent
  static ColorDrawable onCreateMountContent(ComponentContext c) {
    return new ColorDrawable();
  }

  @OnMount
  static void onMount(
      ComponentContext context,
      ColorDrawable colorDrawable,
      @FromPrepare int color) {
    convertDrawable.setColor(color);
  }
}
```
在@MountSpec方法中使用Output<?>会自动的创建一个输入在下一个阶段中.在这种情况下,一个@OnPrepare的输出就会在@OnMount中创建一个输入.

在编译期间,注释处理器将会确保阶段间的不变性,例如你不能在@OnPrepare中使用@OnMeasure的输出,因为@OnPrepare总是在@OnMeasure之前执行.

##### 测量
如果你需要在布局计算阶段定义如何测量你的component,那么你就需要实现@OnMeasure方法.

现在,让我们假设我们需要我们的ColorComponent有一个默认的宽度,并且当它的高度未定义的时候,能够强制执行一定的宽高比.
``` java
@OnMeasure
static void onMeasure(
    ComponentContext context,
    ComponentLayout layout,
    int widthSpec,
    int heightSpec,
    Size size) {

  // If width is undefined, set default size.
  if (SizeSpec.getMode(widthSpec) == SizeSpec.UNSPECIFIED) {
    size.width = 40;
  } else {
    size.width = SizeSpec.getSize(widthSpec);
  }

  // If height is undefined, use 1.5 aspect ratio.
  if (SizeSpec.getMode(heightSpec) == SizeSpec.UNSPECIFIED) {
    size.height = width * 1.5;
  } else {
    size.height = SizeSpec.getSize(heightSpec);
  }
}
```
在@OnMeasure方法中,你可以像以前一样使用@Prop注释访问Component props.SizeSpec的API类似于Android中的[MeasureSpec]().

就像@OnPrepare一样,@OnMeasure方法也能生成阶段间的输出(能够使用@FromMeasure注释的参数来访问),并且可以在后台线程中执行.

##### ShouldUpdate

Mount Spec可以使用@ShouldUpdate注释定义一个方法来避免在更新时进行重新测试和重新挂载。
@ShouldUpdate的调用的前提是component是"纯渲染函数"。一个组件如果是纯渲染函数,那么它的渲染结果只取决于它的prop和状态.这意味着在@OnMount期间，组件不应该访问任何可变的全局变量。
一个@MountSpec可以通过使用@MountSpec注释的pureRender参数来定自己为"纯渲染的"。只有纯渲染的Component可以假设当prop不更改时就不需要重新挂载。@ShouldUpdate函数可以定义如下：

``` java
@ShouldUpdate(onMount = true)
public boolean shouldUpdate(Diff<String> someStringProp) {
  return !someStringProp.getPrevious().equals(someStringProp.getNext());
}
```

shouldUpdate中的参数是prop或状态的对比差异。Diff是一个包含旧Component层级结构中@Prop或@State的值以及新Component层级结构中相同的@Prop或@State值的类。在这个示例中，我们将someStringProp定义为一个String类型的@Prop。shouldUpdate方法将收到一个Diff<String>，以便能够比较此@Prop的旧值和新值。
shouldUpdate必须考虑在@OnMount时使用的任何prop和状态。它可以安全地忽略仅在“@OnMount/@OnUnbind”时间使用的prop和状态，因为这两个方法无论如何都会被执行。

@ShouldUpdate注释上的onMount属性可以控制是否在挂载时进行shouldUpdate检查。默认情况下，Litho将尝试在layout的时候执行检查，但是在检查布局差异功能被关闭的时候,作为替代方案,将onMount设置为true,在挂载时执行此检查就变得很有用了。默认情况下，onMount属性被设置为false，因为相等检查本身可能很耗时,这会使挂载性能变得更差。

@ShouldUpdate注释方法目前仅支持在@MountSpec中使用。我们计划在未来在更复杂的布局中也支持它，但目前在@LayoutSpec中用@ShouldUpdate注释的方法将不起作用。