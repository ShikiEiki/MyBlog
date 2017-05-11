---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-无障碍环境
date: 2017-05-09 09:14:45
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.
英文原文文档地址: [Litho-doc](http://fblitho.com/docs/accessibility)
## 兼容性
### 无障碍环境

</p>
</p>

#### 内容描述
---
所有的Component都默认支持内容描述.这意味着所有的布局builder都拥有一个CharSequence类型的prop叫做contentDescription.

在任何component上设置内容描述都非常的简单:

``` java
Image.create(c)
    .imageRes(R.drawable.some_image)
    .withLayout()
    .contentDescription("This is an image")
    .build())
```

在这里设置的内容描述与在Android view上设置的内容描述拥有同等的效果.

</p>
</p>

#### 自定义
---
在Mount Spec可以通过实现一个@OnPopulateAccessibilityNode方法来实现自定义的无障碍支持.这个方法接受一个AccessibilityNodeInfoCompat参数,也接受任何在其他spec方法中指定的prop参数.

举例来说,Text的无障碍功能是使用下列方法指定的:
``` java
@OnPopulateAccessibilityNode
static void onPopulateAccessibilityNode(
    AccessibilityNodeInfoCompat accessibilityNode,
    @Prop CharSequence text) {
  accessibilityNode.setText(text);
}
```

这仅适用于挂载drawable的Component,因为如果Component挂载一个view,则无障碍支持就是内置的了.

</p>
</p>

#### 额外的无障碍节点
---
如果是在更复杂的需要暴露更多额外的node给无障碍框架的mount spec中,你就必须使用以下的注释实现3个额外的方法:
- GetExtraAccessibilityNodesCount:返回Component暴露出的外的无障碍节点的个数.
- OnpopulateExtraAccessibilityNode:使用给定的边界填充额外的无障碍节点.
- GetExtraVirtualViewAt:返回Component中指定位置的额外无障碍节点的索引.
- 
</p>
</p>

#### 无障碍的处理
---
所有的Component都支持一系列与[AccessibilityDelegateCompat](https://developer.android.com/reference/android/support/v4/view/AccessibilityDelegateCompat.html)方法相一致的事件.
这些事件拥有它们对应的AccessibilityDelegateCompat方法的参数,和一个额外的名叫superDelegate的AccessibilityDelegateCompat参数,它允许你在必要的时候显式的调用View的无障碍方法的默认实现.

以下是支持的事件的总览:

| 事件 | ACCESSIBILITYDELEGATE方法 |
| --- | ---- |
| DispatchPopulateAccessibilityEventEvent | [dispatchPopulateAccessibilityEvent](https://developer.android.com/reference/android/support/v4/view/AccessibilityDelegateCompat.html#dispatchPopulateAccessibilityEvent(android.view.View, android.view.accessibility.AccessibilityEvent) |
| OnInitializeAccessibilityEventEvent | [onInitializeAccessibilityEvent](https://developer.android.com/reference/android/support/v4/view/AccessibilityDelegateCompat.html#onInitializeAccessibilityEvent(android.view.View, android.view.accessibility.AccessibilityEvent) |
| OnInitializeAccessibilityNodeInfoEvent | [onInitializeAccessibilityNodeInfo](https://developer.android.com/reference/android/support/v4/view/AccessibilityDelegateCompat.html#onInitializeAccessibilityNodeInfo(android.view.View, android.support.v4.view.accessibility.AccessibilityNodeInfoCompat) |
| OnPopulateAccessibilityEventEvent | [onPopulateAccessibilityEvent](https://developer.android.com/reference/android/support/v4/view/AccessibilityDelegateCompat.html#onPopulateAccessibilityEvent(android.view.View, android.view.accessibility.AccessibilityEvent) |
| OnRequestSendAccessibilityEventEvent | [onRequestSendAccessibilityEvent](https://developer.android.com/reference/android/support/v4/view/AccessibilityDelegateCompat.html#onRequestSendAccessibilityEvent(android.view.ViewGroup, android.view.View, android.view.accessibility.AccessibilityEvent) |
| PerformAccessibilityActionEvent | [performAccessibilityAction](https://developer.android.com/reference/android/support/v4/view/AccessibilityDelegateCompat.html#performAccessibilityAction(android.view.View, int, android.os.Bundle) |
| SendAccessibilityEventEvent | [sendAccessibilityEvent](https://developer.android.com/reference/android/support/v4/view/AccessibilityDelegateCompat.html#sendAccessibilityEvent(android.view.View, int) |
| SendAccessibilityUncheckedEvent | [sendAccessibilityEventUnchecked](https://developer.android.com/reference/android/support/v4/view/AccessibilityDelegateCompat.html#sendAccessibilityEventUnchecked(android.view.View, android.view.accessibility.AccessibilityEvent) |

设置任何这些事件的处理程序将会导致在挂载的View中设置AccessibilityDelegate，当调用相应的方法时，它将调用您的事件处理程序。

如果你没有提供处理程序的方法被调用,delegate将会交由ANdroid view的默认实现去处理(类似于调用了super或者superDelegate的实现).

举例来说,下面是覆写Component中onInitializeAccessibilityNodeInfo的步骤:
1.实现一个事件处理器:
``` java
@OnEvent(OnInitializeAccessiblityNodeInfoEvent.class)
static void onInitializeAccessibilityNodeInfoEvent(
    @FromEvent AccessibilityDelegateCompat superDelegate,
    @FromEvent View view,
    @FromEvent AccessibilityNodeInfoCompat node) {
  // Equivalent to calling super on a regular AccessibilityDelegate, not required
  superDelegate.onInitializeAccessibilityNodeInfo(view, node);
  // My implementation
}
```

2.把时间处理器设置到component中.
``` java
Text.create(c)
    .text(title)
    .withLayout()
    .onInitializeAccessiblityNodeInfoHandler(MyComponent.onInitializeAccessibilityNodeInfoEvent(c))
```

AccessibilityDelegates的一个最好的特性之一就是它的甚至可以跨越视图的可重用性.而在Litho中,这也可以通过在Component中引入一个包含我们需要的事件处理器的包装spec来实现.例如.假设我们需要一个Component,它添加了"please"到了每一个它注释的AccessibilityEvent方法中.

``` java
@LayoutSpec
class PoliteComponentWrapper {

  @OnCreateLayout
  static ComponentLayout onCreateLayout(
      ComponentContext c,
      @Prop Component<?> content) {
      
    return Layout.create(c, content)
        .onPopulateAccessibilityEventHandler(
            PoliteComponentWrapper.onPopulateAccessibilityEvent(c))
        .build();
  }

  @OnEvent(OnPopulateAccessibilityEvent.class)
  static void onPopulateAccessibilityEvent(
      ComponentContext c,
      @FromEvent AccessibilityDelegateCompat superDelegate,
      @FromEvent View view
      @FromEvent AccessibilityEvent event) {
    superDelegate.onPopulateAccessibilityEvent(view, event);
    event.getText().add("please");
  }
}
```

现在你可以使用PoliteComponentWrapper来替代任何你原来使用你的Component的地方了.

``` java
@OnCreateLayout
static ComponentLayout onCreateLayout(
    ComponentContext c,
    @Prop CharSequence text) {
    
  return PoliteComponentWrapper.create(c)
      .content(
           Text.create(c)
               .text(text))
      .build();
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