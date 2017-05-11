---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-回收机制
date: 2017-04-27 10:09:23
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.
英文原文文档地址: [Litho-doc](http://fblitho.com/docs/recycling)
## 架构
### 回收机制

</p>
</p>

设备屏幕通常以每秒60帧的速率刷新。为了能提供流畅的UI效果，应用程序需要能够每16ms一帧的速度持续渲染我们的UI。如果达不到这个时间标准，就会导致丢帧和不佳的用户体验。随着UI变得越来越复杂，在限制的时间内越来越难以完成所有的渲染工作。特别是在动态滚动的界面上,因为新的UI块的不断被添加到屏幕上，完成渲染就变得尤其有挑战性。
Android使用[RecyclerView](https://developer.android.com/guide/topics/ui/layout/recyclerview.html)解决了这个问题，RecyclerView是一个动态UI容器，可以通过创建足够的视图来填充屏幕，然后在UI滚动中回收和重用它们，以显示大量数据中的元素。

![](/image/20170426171157.gif)

RecyclerView支持异构的内容的显示。为此，它会根据item的不同的类型将其保留在不同的池中。虽然这个方法在简单情况下工作正常，但是对于具有许多不同item类型的UI，这种方法已经被证明是有问题的。在具有许多item类型的场景中，如果一个item随着滚动事件进入视窗,则它这种类型的item是RecyclerView第一次显示可能性非常大。如果发生这种情况，RecyclerView必须为这个item进行内存分配。在短短的16ms中,RecyclerView必须为新的item进行绑定，测量和布局工作。

![](/image/20170426172051.gif)

</p>
</p>

#### Litho增量式回收
---
我们希望为Litho建立一个更具可扩展性和更高效率的回收系统，同时我们希望从API中消除item类型的复杂性。在Litho中，布局的描述与实际用于在屏幕上呈现UI的Views和Drawable完全不相关。这意味着当我们需要在屏幕上放置一个新的RecyclerView item时，我们已经完全知道了该item的内容和该item与其余UI的相对位置。这允许Litho完全摆脱item类型的概念。在recyclerview滚动时,比起重复利用view来表示item,我们选择了增量式的利用例如Text或者Image这样的构建块.这种做法在传统的Android view上是不可能的，因为在传统的Android中,我们需要在完整的view tree上进行布局计算,当我们算出所有view的位置时，一切都已经被实例化了。

![](/image/20170426172130.gif)

回收利用像Text这样的"原始item"能够大大增加应用程序的内存效率，比如你可以回收list中的任何一个Text的给其他需要显示的Text来用。最重要的是，由于我们的布局是提前计算的，所以我们能够确切地知道一个新的item什么时候会变得可见，这意味着，我们可以在屏幕上引入非常少量的"原始item"来显示每一帧,而不是在一帧中绑定并且绘制一个大的view tree。

</p>
</p>
</p>
</p>

[回到导航页](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%80%BB%E8%A7%88%E5%92%8C%E5%AF%BC%E8%88%AA/)

</p>
</p>
</p>
</p>