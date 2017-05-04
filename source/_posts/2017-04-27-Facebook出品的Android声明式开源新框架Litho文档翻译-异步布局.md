---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-异步布局
date: 2017-04-27 10:09:23
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.

# 架构

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


[回到导航页](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%80%BB%E8%A7%88%E5%92%8C%E5%AF%BC%E8%88%AA/)