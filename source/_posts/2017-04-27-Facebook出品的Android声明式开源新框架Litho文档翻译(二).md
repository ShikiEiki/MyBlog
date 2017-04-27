---
title: Facebook出品的Android声明式开源新框架Litho文档翻译(二)
date: 2017-04-27 10:09:23
tags: [Android,实用库,Litho]
category: Android知识
---

# 架构

### 代码生成器
正如在【编写Conponent】中写道的，Litho依赖代码生成器来从ComponentSpec生成Component.这个过程需要用到一个不可变的java对象——SpecModel，作为中间件。

代码生成过程分为三个步骤：
- 从ComponentSpec中生成一个SpecModel。
- 验证SpecModel的合法性
- 从SpecModel中生成Component。

##### 创建SpecModel
SpecModel是在编译时，由注解处理器(一个扫描和处理注解的javac工具)生成的.Litho的注解处理器将会扫描你的ComponentSpec中的方法,变量和注释,为每一个ComponentSpec创建SpecModel.
未来,我们将会支持使用其他方法创建SpecModel.举个例子:我们考虑支持直接在Android Studio/Intellij中创建SpecModel,这样可以允许我们不用build源码就可以生成Component.

##### SpecModel验证
SpecModel有一个方法叫做validate(),它返回一个包含SpecModelValidationError(SpecModel验证错误)的list.如果这个list为空,说明这个Spec是格式合法的,可以用它来创建一个合法的component.如果不是,这个list则会包含一系列需要在Component生成前修复的错误.

##### Component生成
如果SpecModel验证步骤成功了,接着generate方法将会被调用,它将会生成一个Javapoet TypeSpec(用它能够很容易的创建一个Component的class 文件).

##### 给你的工程设置代码生成器
如果你根据【开始工作】中的说明设置了你的工程代码，那么代码生成器就被自动设置好了。

### 异步布局(layout)

##### 不可变性和线程安全
大多数线程安全的问题都是由对可变对象的并发读写造成的.在Java中,一个典型的此类问题是这样的:
``` java
 public class SomeExampleClass {
    private int mCounter;

    public String getThisOrThat() {
      if (mCounter > 10) {
        return "this":
      } else {
        mCounter++;
        return "that";
      }
    }
  }
```
如果有多个线程都调用一个共享的SomeExampleClass中的的getThisOrThat方法,这就构成了一个最经典的竞争情况.当第二个线程进入这个方法尝试获取mCounter时,第一个线程可能正在执行mCounter++,因此我们不能确定第二个线程从mCounter中读到的值是什么.出现这个问题的原因是在我们的代码中存在一个可变的状态变量(mCounter),并且有多个线程尝试去读写它.竞争情况是我们在使用多线程编程去处理任务时最常遇到的问题.

而这就是为什么传统上在多线程上运行UI代码都会变得极其复杂的原因.Android中的view是有状态的和可变的.比如一个TextView,它必须保持追踪现在显示的文字,并且暴露一个setText()方法给开发者去修改文字.这就意味着Android UI框架如果决定要分流一些工作(例如layout计算)到第二个线程中去做,它就必须解决用户在其他线程中调用setText()而改变了当前正在布局计算中的文字的问题.

让我们回到我们刚才的示例代码.我们说主要的问题就在我们的getThisOrThat()方法中的可变状态变量mCounter的存取.有没有一种方法能够做到功能上和它一致但是却不用依赖这种可变的状态变量呢?让我们想象一种情况:所有对象在创建后都不能改变其自身的内容.如果没有内容改变,我们也就不会有线程间尝试存取同一个状态变量的竞争问题了.我们可以重新改写一下我们的示例代码:
``` java
public static class Result {
    public final int mCounter;
    public final String mValue;

    public Result(int counter, int value) {
      mCounter = counter;
      mValue = value;
    }
  }

  public class SomeExampleClass {
    public static Result getThisOrThat(int counterValue) {
      if (counterValue > 10) {
        return new Result(counterValue, "this"):
      } else {
        return new Result(counterValue + 1, "that");
      }
    }
  }

```
我们的方法现在完全是线程安全的了,因为它从来没有改动过任何SomeExampleClass中的内部状态变量.在这个例子中,getThisOrThat()方法我们称之为"纯净方法(pure function)",因为它的输出结果只取决于输入值并且这么做没有任何副作用.

在Litho中我们尝试应用这种概念到布局计算中.Component是一个包含了所有提供给布局方法的输入值的不可变的类.而这些输入值以@Prop和@State的格式提供.这也解释了为什么我们需要@Prop和@State为不可变的.因为如果它们是可变的,我们获取布局的函数就失去了作为"纯净方法"的性质.

在Java中,不可变性在中通常意味着需要花费时间做更多的内存分配动作.即使在我们的简单的示例中,我们每次调用我们的方法时都需要分配一个新的Result对象内存.而Litho使用池的概念和代码生成的概念来自动优化对象内存分配,使得内存分配花费降到最小.

##### 同步和异步操作
Litho同时提供了同步的和异步的API用来做布局计算.两种API都是线程安全的并且都可以在任意线程中调用.最终的布局总是呈现最后使用setRoot()或者setRootAsync()设置的Component.

同步的布局计算能够确保一旦在ComponentTree中调用了setRoot,布局计算的结果就能立刻准备好,以供挂载到LithoView上.

而它的主要的缺陷是由于它的布局计算工作发生在调用setRoot()的线程中,因此不建议在主线程中调用它.而在另一方面,在一些情况下,你不能在展示一些东西在屏幕上之前就等待后台进程去计算布局,比如说你要展示的item已经在视窗口了.这种情况下调用setRoot()是最好的选择.使用同步的操作也能是集成Litho至已存在的线程体系变得更加简单.如果你的程序已经拥有了一个复杂并且结构化的线程设计,你可能会不想依赖Litho的内建线程去完成布局计算.

异步的布局计算将会使用Litho的"布局线程"来计算布局,这意味着当调用布局计算时,布局工作将会立刻进入另一个独立线程的工作队列中而不会立即向它的调用线程返回结果.异步的布局操作被广泛的应用于RecyclerBinder的示例中.

### 增量式挂载
虽然Component提供了扁平化的view层级结构和主线程外的布局计算功能,但是装载操作(包括对view和drawable的创建,回收和附加工作)在复杂的component中依然花费主线程的大量资源,尤其是那些包含很多view的component.

Litho可以把装载component的花费平均分配至每一个UI帧来避免卡顿,并且对开发者来说是透明的.

使用增量式挂载(默认支持),LithoView将只会挂载与当前可见区域大小相适应的内容并且卸载(并且回收)那些已经看不见了的component.

![](/image/20170426150321.png)
如果你使用Litho的异步RecyclerView支持,框架将会无缝的支持增量式挂载.

### View的扁平化
让我们看看下方的例子.它包含一个图片一个标题和一个副标题.在传统的Android View 系统里,你可以使用几个view来包含这些元素,再用一个viewGroup来包裹它们.

![](/image/20170426150749.png)

Litho会自动减少最终UI层级结构所包含的View数量。布局计算这一步产生的布局树只是你的UI的蓝图，与Android的View没有直接的耦合。这允许框架在挂载组件之前处理布局树以获得最佳渲染性能。

我们使用两种途径来实现:
首先，Litho能在布局计算后完全忽略容器类，因为它们在挂载步骤中不会被用到。拿我们的例子来说，在挂载时，不会有单独包含标题和子标题的View。
第二，Litho可以选择挂载一个view或者挂载一个drawable。事实上，对于Litho框架里的大多数核心组件，如Text和Image，挂载的是drawable，而不是view。

这些优化的结果是,示例中UI的组件实际上将被渲染为一个独立,扁平的视图。你可以在下面的屏幕截图中看到这一点(启用了开发者选项中的显示布局边界).

![](/image/20170426150806.png)

虽然扁平化的view对于减少内存使用和缩短绘制时间有相当大的好处，但它并不是一颗银弹(译者注:即它不是一个对所有情况都能用的万用方案)。当我们希望依赖Android view的特定功能时（例如触摸事件处理，可访问性或局部无效化），Litho提供一个非常通用的系统来自动对挂载Component的视图层次结构"去扁平化"。例如，如果要在示例中启用单击图像或文本，如果设置了点击处理器,框架将自动把文字或图像包装在view一个新的view中。

### 回收机制

设备屏幕通常以每秒60帧的速率刷新。为了能提供流畅的UI效果，应用程序需要能够每16ms一帧的速度持续渲染我们的UI。如果达不到这个时间标准，就会导致丢帧和不佳的用户体验。随着UI变得越来越复杂，在限制的时间内越来越难以完成所有的渲染工作。特别是在动态滚动的界面上,因为新的UI块的不断被添加到屏幕上，完成渲染就变得尤其有挑战性。
Android使用RecyclerView解决了这个问题，RecyclerView是一个动态UI容器，可以通过创建足够的视图来填充屏幕，然后在UI滚动中回收和重用它们，以显示大量数据中的元素。

![](/image/20170426171157.gif)

RecyclerView支持异构的内容的显示。为此，它会根据item的不同的类型将其保留在不同的池中。虽然这个方法在简单情况下工作正常，但是对于具有许多不同item类型的UI，这种方法已经被证明是有问题的。在具有许多item类型的场景中，如果一个item随着滚动事件进入视窗,则它这种类型的item是RecyclerView第一次显示可能性非常大。如果发生这种情况，RecyclerView必须为这个item进行内存分配。在短短的16ms中,RecyclerView必须为新的item进行绑定，测量和布局工作。

![](/image/20170426172051.gif)

##### Litho增量式回收

我们希望为Litho建立一个更具可扩展性和更高效率的回收系统，同时我们希望从API中消除item类型的复杂性。在Litho中，布局的描述与实际用于在屏幕上呈现UI的Views和Drawable完全不相关。这意味着当我们需要在屏幕上放置一个新的RecyclerView item时，我们已经完全知道了该item的内容和该item与其余UI的相对位置。这允许Litho完全摆脱item类型的概念。在recyclerview滚动时,比起重复利用view来表示item,我们选择了增量式的利用例如Text或者Image这样的构建块.这种做法在传统的Android view上是不可能的，因为在传统的Android中,我们需要在完整的view tree上进行布局计算,当我们算出所有view的位置时，一切都已经被实例化了。

![](/image/20170426172130.gif)

回收利用像Text这样的"原始item"能够大大增加应用程序的内存效率，比如你可以回收list中的任何一个Text的给其他需要显示的Text来用。最重要的是，由于我们的布局是提前计算的，所以我们能够确切地知道一个新的item什么时候会变得可见，这意味着，我们可以在屏幕上引入非常少量的"原始item"来显示每一帧,而不是在一帧中绑定并且绘制一个大的view tree。