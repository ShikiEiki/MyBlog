---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-RTL
date: 2017-05-09 11:08:42
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.
英文原文文档地址: [Litho-doc](http://fblitho.com/docs/rtl)
## 兼容性
### RTL

</p>
</p>

Litho中对RTL(从右到左)布局的支持与Android的RTL支持相同.为了使你的Component支持RTL,你只需要简单的在margin和padding参数中使用START和END代替原来的LEFT和RIGHT就可以了.所有其他的工作都会由布局系统自动的完成.

举例来说,这是一个布局:
``` java
Column.create(c)
    .paddingDip(START, 10)
    .marginDip(END, 5)
    .child(...)
    .child(...)
    .build();
```

布局系统将会自动的遵循Android资源系统定义的布局方向.你还可以使用类似的start/end变量到位置参数中来是绝对位置支持RTL.

``` java
Image.create(c)
    .srcRes(R.drawable.my_image)
    .withLayout()
    .positionType(ABSOLUTE)
    .positionDip(START, 10)
    .build();
```

在上面的示例中,当RTL被激活时,image Component将会自动的调整到距离它的父级的右边缘10像素的位置上.

</p>
</p>
</p>
</p>

##### [回到导航页](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%80%BB%E8%A7%88%E5%92%8C%E5%AF%BC%E8%88%AA/)
</p>
</p>
</p>
</p>
