---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-Recycler
date: 2017-05-05 14:45:20
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.
英文原文文档地址: [Litho-doc](http://fblitho.com/docs/recycler-component)
## 参考
### Recycler

</p>
</p>

[RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)是一个Android系统中构建一个可滚动列表所需的基本的构建快.而[Recycler](http://fblitho.com/javadoc/com/facebook/litho/widget/Recycler) component提供了与RecyclerView非常相似的功能,同时实现了例如后台布局和增量式挂载这样的功能.

</p>
</p>

#### 创建一个Recycler Component
---
你可以像使用其他任何Litho框架中的Component一样——先构建,再把它添加到你的布局中——这样来使用Recycler。
``` java
@OnCreateLayout
static ComponentLayout onCreateLayout(
    final ComponentContext c,
    @Prop RecyclerBinder recyclerBinder) {
    
  return Recycler.create(c)
      .binder(recyclerBinder)
      .buildWithLayout();
}
```

以上的代码渲染了一个Recycler Component,它将显示recyclerBinder中的内容.

</p>
</p>

#### RecyclerBinder
---
[RecyclerBinder](http://fblitho.com/javadoc/com/facebook/litho/widget/RecyclerBinder)是使用Component操作列表式UI的切入点.它保存了列表中包含的所有Item，并且当用户滚动列表时，它会计算将会在屏幕中显示的item的布局。

RecyclerBinder作为Litho的一部分:
- 作用类似于RecyclerView的Adapter
- 定义在RecyclerView中使用的布局(如Linear或Grid等)
- 会提前在后台线程中处理负责的布局计算.

![](/image/20170505150546.gif)

让我们开始创建一个RecyclerBinder:
``` java
final RecyclerBinder recyclerBinder = new RecyclerBinder(c);
```

这样会创建一个最简单的RecyclerBinder,它会把Recycler中的内容以竖直列表的方式展现.

如果想要让Recycler使用GridLayout(表格布局),我们可以改用这个构造函数:
``` java
final RecyclerBinder recyclerBinder = new RecyclerBinder(c, new GridLayoutInfo(c, spanCount));
```
RecyclerBinder暴露了一系列API来操作将在Recycler中显示的item.

最常使用的有:
``` java
recyclerBinder.insertItemAt(position, component);
recyclerBinder.updateItemAt(position, component);
recyclerBinder.removeItemAt(position);
recyclerBinder.moveItem(fromPosition, toPosition);
```

RecyclerBinder的API中直接使用到Component,因为一个Component仅仅是一系列Prop的集合,我们可以提前建立任何的Component然后把布局管理工作交给RecyclerBinder.

RecyclerBinder也支持接收规定Component该如何布局的额外的信息.这些额外的信息可以通过一个ComponentInfo传递.代码如下所示:
``` java
recyclerBinder.insertItemAt(
  position,
  ComponentInfo.create()
      .component(component)
      .isSticky(true)
      .build());
```

</p>
</p>

#### 使用RecyclerBinder与DiffUtil协同工作
---
RecyclerBinder提供了与[DiffUtil](https://developer.android.com/reference/android/support/v7/util/DiffUtil.html)协同工作的方式.Litho定义了[RecyclerBinderUpdateCallback](http://fblitho.com/javadoc/com/facebook/litho/widget/RecyclerBinderUpdateCallback.html)这个API,它实现了ListUpdateCallback,因此可以用来发送DiffResult到RecyclerBinder.

下面是一个如何使用Litho与DiffUtil协同工作的例子:
``` java
private final ComponentRenderer<Data> mComponentRenderer = new ComponentRenderer<> {
    ComponentInfo render(Data data, int idx) {
      return ComponentInfo.create()
          .component(
          	DataComponent.create(mComponentContext)
          	    .data(data))
          	    .build();
    }
  }

  public void onNewData(List<Data> newData) {
    final DiffUtil.DiffResult diffResult = DiffUtil.calculateDiff(new MyDataDiffCallback(mCurrentData, newData));
    final RecyclerBinderUpdateCallback callback = RecyclerBinderUpdateCallback.acquire(
      mCurrentData.size(),
      newData,
      mComponentRenderer,
      mRecyclerBinder)

    diffResult.dispatchUpdatesTo(callback);
    callback.applyChangeset();
    RecyclerBinderUpdateCallback.release(callback);
}
```
每当列表中需要创建新的Component或者列表的模型需要被更新的时候,ComponentRenderer都会被调用.

</p>
</p>
</p>
</p>

##### [回到导航页](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%80%BB%E8%A7%88%E5%92%8C%E5%AF%BC%E8%88%AA/)
</p>
</p>
</p>
</p>




