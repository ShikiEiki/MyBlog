---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-State
date: 2017-05-04 14:06:23
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.
英文原文文档地址: [Litho-doc](http://fblitho.com/docs/state)
## 参考
### State(状态)

</p>
</p>

Litho Component可以包含两种类型的data:
- prop:从父级继承并且在Component的生命周期内不能被改变.
- state:封装实现的细节,由Component管理,并且对父级是透明的.

一个常见的需要使用State的例子是:渲染一个Checkbox(复选框).Component需要根据选中还是未选中来渲染不同的drawable,但是这是一个Component内部的信息,而他的父级不需要去关心这个状态.

</p>
</p>

#### 声明一个Component的状态
--- 
你可以使用@State注释在spec的生命周期方法中定义一个Component的State,这与你定义Prop的方法是一样的.

定义State元素在Layout Spec和Mount Spec的生命周期方法中可用:
``` java
@LayoutSpec		
public class CheckboxSpec {		

  @OnCreateLayout
  static ComponentLayout onCreateLayout(
      ComponentContext c,
      @State boolean isChecked) {
      
    return Column.create(c)
        .child(Image.create(c)
            .srcRes(isChecked
                ? R.drawable.is_checked
                : R.drawable.is_unchecked))
        .child(Text.create(c)
            .text("Submit")
            .clickHandler(Checkbox.onClickedText(c))
        .build;
  }

  @OnEvent(ClickEvent.class)
  static void onClickedText(
      ComponentContext c,
      @State boolean isChecked) {
    ...
  }
}
```
</p>
</p>
#### 初始化State的值
---
为了给State设置一个初始值,你需要在你的Spec中使用@OnCreateInitialState注释来编写一个方法.

关于编写@OnCreateInitialState方法,你需要知道以下几点:
- 第一个参数必须是ComponentContext类型.
- @Prop参数在这里也是可用的.
- 剩下的参数的名称必须和其他生命周期方法中的@State参数保持一致,并且这些剩下参数的类型必须为[StateValue](http://fblitho.com/javadoc/com/facebook/litho/StateValue),其中泛型的类型与对应的@State一致.
- @OnCreateInitialState 方法不是必须的.如果你不定义或者没有定义所有state的初始值,那么那些没有被定义初始值的state将会使用java的默认值.
- 每一个Component的@OnCreateInitialState方法只会在Component第一次被添加到Component树的时候被调用一次.如果Component的key没有改变,那么后续对Component树布局的重新计算不会重新调用@OnCreateInitialState方法.
- 你永远不需要自己调用@OnCreateInitialState方法.

下面是例子是如何使用父级传下来的值来初始化一个复选框的state值.

``` java
@LayoutSpec		
public class CheckboxSpec {

  @OnCreateInitialState
  static void createInitialState(
      ComponentContext c,
      StateValue<Boolean> isChecked,
      @Prop boolean initChecked) {

    isChecked.set(initChecked);
  }
}
```
</p>
</p>
#### 定义State的更新
---
你可以使用@OnUpdateState在Spec中声明一个方法来定义Component的State更新的方式.

你可以根据你想更新的state或者你的state依赖的参数的值来定义任意多的你需要的@OnUpdateState方法.

每次对@OnUpdateState方法的调用都会触发一次新的对它的Component树的布局计算.为了获得更好的性能,如果在某种情况下会触发对多个State的更新,那么你就应该定义一个@OnUpdateMethod方法来更新所有这些state的值.合并这些更新操作可以减少新的布局计算的次数,从而提升性能.

关于编写@OnUpdateState方法,你需要知道以下几点:
- 代表State的参数名必须和其他方法中使用@State定义的参数名一样,并且类型必须为StateValue,泛型类型也必须和对应的@State参数的类型一致.
- @Param参数也是可以使用的.如果你的State的值需要依赖于Prop,你可以在@OnupdateState函数的参数中使用@Param声明,这样就可以在更新被触发的时候传递prop的值进来了.
- 所有其他的参数都必须在其他生命周期方法中有一个对应的@State参数,并且类型必须为StateValue,泛型的类型也必须和@State参数一致.

下面是如何给checkbox定义一个state方法:
``` java
@LayoutSpec		
public class CheckboxSpec {		

  @OnUpdateState
  static void updateCheckboxState(StateValue<Boolean> isChecked) {
    isChecked.set(!isChecked.get());
  }
}
```
如果你想要合并多个state更新到一个方法中,你只需要把所有这些state都添加到一个@OnUpdateState方法中:
``` java
@OnUpdateState
static void updateMultipleStates(
    StateValue<Boolean> stateOne,
    StateValue<String> stateTwo,
    @Param int someParam) {

  final boolean thresholdReached = someParam > 100;
  stateOne.set(thresholdReached);
  stateTwo.set(thresholdReached ? "reached" : "not reached");
}

```
</p>
</p>
#### 调用State更新
---
对你的Spec中的每一个@OnUpdateState方法,自动生成的Component中都会包含两个方法,他们在后台都会委托给@OnUpdateState方法处理.
- 一个和@OnUpdateState同名的静态方法,它将同步的应用state的更新.
- 一个和@OnUpdateState同名并且加上Async后缀的静态方法,它将异步的触发State的更新.这两个方法都使用ComponentContext作为第一个参数,后面的参数与你在@OnUpdateState方法中使用@Param定义的参数相同.

下面展示了当用户点击的时候如何调用State更新方法来更新你的复选框:
``` java
@LayoutSpec		
public class CheckboxSpec {		

  @OnCreateLayout
  static ComponentLayout onCreateLayout(
      ComponentContext c,
      @State boolean isChecked) {
      
    return Column.create(c)
        .child(Image.create(c)
        .srcRes(isChecked
            ? R.drawable.is_checked
            : R.drawable.is_unchecked))
        .clickHandler(Checkbox.onCheckboxClicked(c)))
    .build;
  }

  @OnUpdateState
  static void updateCheckbox(StateValue<Boolean> isChecked) {
    isChecked.set(!isChecked.get());
  }

  @OnEvent(ClickEvent.class)
  static void onCheckboxClicked(ComponentContext c) {
    Checkbox.updateCheckboxAsync(c);
    // Checkbox.updateCheckbox(c); for a sync update
  }
}
```

当你调用State更新方法的时候,你需要记住下面几点:
- 当调用一个State更新方法的时候,作为第一个参数的ComponentContext必须总是为更新发生时的生命周期方法中传递进来的那个ComponentContext.这个context包含了现在已知的State的值等重要信息,在布局计算时,这些信息对把旧的Component转换成新的Component非常重要.
- 在LayoutSpec中,你应该避免在onCreateLayout中调用State更新方法,除非你完全确定它只会发生很少并且确定的的次数。每一次调用State更新方法都会引起Component树的一次新的布局计算，而计算又会反过来调用它其中所有的Component的onCreateLayout方法，因此，这很容易引起死循环。你应该考虑是否使用懒汉式State更新(下面会说到)才是更加适用于你的情况的方法.
- 在MountSpec中,永远也不要在mount和bind方法中调用State更新方法.如果你需要在这类方法中更新State的值,你应该使用下面会讲到的懒汉式State更新来替代.
</p>
</p>

#### Key和识别Component
---
Litho框架将会为每一个Component设置一个key值,这个值取决于它的类型和它的父级的key值.你可以使用这个key值来确定在State更新的时候哪一个Component才是我们需要更新的,或者使用这个key值在遍历Component树的时候查找Component.

具有相同父级的同类型Component将会被设定相同的key值.因此我们需要一种方法来唯一的识别它们.

此外,当组件的State或者Prop被更新并且Component树重新被创建的时候,会出现一些Component被移除,添加或者在树中重新安排位置的情况.因为Component可能是动态的,因此我们需要一种方法,能在即使Component树改变的情况下还能追踪到应该是哪个Component去进行状态更新.

Component.Builder类会暴露一个.key()方法,你可以使用它来在创建Component的时候制定一个唯一的key给它,以便将来能够识别它.

当你在一个父控件下拥有多个相同类型的Component时,或者你期望的布局内容是动态的时候,你就应该设置这个key值.

一种最常见的你需要手动给你的Component设置key的情况是,你在一个循环中创建和添加子Component:
``` java
@OnCreateLayout
static ComponentLayout onCreateLayout(
    ComponentContext c,
    @State boolean isChecked) {

  final ComponentLayout.Builder parent = Column.create(c);
  for (int i = 0; i < 10; i++) {
    parent.child(Text.create(c).key("key" +i));
  }
  return parent.build();
}
```
</p>
</p>

#### 懒汉式State更新
---
当你想要更新State的值但是又不想立刻触发一次性的布局计算的时候,你可以使用懒汉式State更新.当你调用懒汉式State更新之后,Component将会保持现有的State值直到下一次布局计算被别的机制(例如收到一个新的prop或者定期的State更新)触发,此时State的值才会被更新.在不需要立刻进行布局计算的情况下,懒汉式State更新对想要更新内部Component信息并且在Component树的重新布局中保持这些信息是非常实用的.

为了使用懒汉式State更新,你需要在@State注释中把canUpdateLazily参数设置为true.

如果我们把一个名叫foo的State标记为canUpdateLazily,Litho框架将会自动生成一个静态State更新方法,名叫LazyUpdateFoo,它将带有一个参数,用于设置foo的新值.

State被标记为canUpdateLazily后依然可以使用常规的State更新.

让我们看下面的例子:

``` java
@OnCreateLayout
static ComponentLayout onCreateLayout(
    final ComponentContext c,
    @State(canUpdateLazily = true) String foo) {

  FooComponent.lazyUpdateFoo(context, "updated foo");
  return Column.create(c)
      .child(
          Text.create(c)
              .text(foo))
      .build();
}

@OnCreateInitialState
static void onCreateInitialState(StateValue<String> foo) {
  foo.set("first foo");
}
```

第一次FooComponent被渲染的时候,即使它的State foo已经被懒汉式更新成另一个值了,它的子Text Component也会显示"first foo".当一个常规的State更新被触发,或者收到一个新的prop的时候,就会触发布局计算,懒汉式更新将会生效,Text将会被渲染成"update foo".
</p>
</p>

#### 不可变性
---
由于[后台布局](https://shikieiki.github.io/2017/04/27/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E5%BC%82%E6%AD%A5%E5%B8%83%E5%B1%80/)机制,State可以在任何时候被多个线程访问.为了确保线程安全,State对象应该是不可变的(即使有极少数情况下不能实现这一点,那么也应该确保State对象是线程安全的).最简单的解决方案是基于原语来表述你的State,因为根据定义,原语就是不可变的.
</p>
</p>
</p>
</p>

##### [回到导航页](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%80%BB%E8%A7%88%E5%92%8C%E5%AF%BC%E8%88%AA/)
</p>
</p>
</p>
</p>
