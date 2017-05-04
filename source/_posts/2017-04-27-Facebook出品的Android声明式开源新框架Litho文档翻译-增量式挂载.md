---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-增量式挂载
date: 2017-04-27 10:09:23
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.

# 架构

### 增量式挂载
虽然Component提供了扁平化的view层级结构和主线程外的布局计算功能,但是装载操作(包括对view和drawable的创建,回收和附加工作)在复杂的component中依然花费主线程的大量资源,尤其是那些包含很多view的component.

Litho可以把装载component的花费平均分配至每一个UI帧来避免卡顿,并且对开发者来说是透明的.

使用增量式挂载(默认支持),LithoView将只会挂载与当前可见区域大小相适应的内容并且卸载(并且回收)那些已经看不见了的component.

![](/image/20170426150321.png)
如果你使用Litho的异步RecyclerView支持,框架将会无缝的支持增量式挂载.

[回到导航页](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%80%BB%E8%A7%88%E5%92%8C%E5%AF%BC%E8%88%AA/)