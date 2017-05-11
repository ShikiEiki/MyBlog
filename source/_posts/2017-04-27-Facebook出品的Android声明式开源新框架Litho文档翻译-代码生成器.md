---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-代码生成器
date: 2017-04-27 10:09:23
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.
英文原文文档地址: [Litho-doc](http://fblitho.com/docs/codegen)
## 架构
### 代码生成器

</p>
</p>

正如在[编写Conponent](https://shikieiki.github.io/2017/04/27/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E7%BC%96%E5%86%99Component/)中写道的，Litho依赖代码生成器来从ComponentSpec生成Component.这个过程需要用到一个不可变的java对象——[SpecModel](http://fblitho.com/javadoc/com/facebook/litho/specmodels/model/SpecModel)，作为中间件。

代码生成过程分为三个步骤：
- 从ComponentSpec中生成一个SpecModel。
- 验证SpecModel的合法性
- 从SpecModel中生成Component。

</p>
</p>

#### 创建SpecModel
---
SpecModel是在编译时，由注解处理器(一个扫描和处理注解的javac工具)生成的.Litho的注解处理器将会扫描你的ComponentSpec中的方法,变量和注释,为每一个ComponentSpec创建SpecModel.
未来,我们将会支持使用其他方法创建SpecModel.举个例子:我们考虑支持直接在Android Studio/Intellij中创建SpecModel,这样可以允许我们不用build源码就可以生成Component.

</p>
</p>

#### SpecModel验证
---
SpecModel有一个方法叫做validate(),它返回一个包含[SpecModelValidationError(SpecModel验证错误)](http://fblitho.com/javadoc/com/facebook/litho/specmodels/model/SpecModelValidationError)的list.如果这个list为空,说明这个Spec是格式合法的,可以用它来创建一个合法的component.如果不是,这个list则会包含一系列需要在Component生成前修复的错误.

</p>
</p>

#### Component生成
---
如果SpecModel验证步骤成功了,接着generate方法将会被调用,它将会生成一个[Javapoet](https://github.com/square/javapoet) TypeSpec(用它能够很容易的创建一个Component的class 文件).

</p>
</p>

#### 给你的工程设置代码生成器
---
如果你根据[准备工作](https://shikieiki.github.io/2017/04/27/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E5%87%86%E5%A4%87%E5%B7%A5%E4%BD%9C/)中的说明设置了你的工程代码，那么代码生成器就被自动设置好了。

</p>
</p>
</p>
</p>

[回到导航页](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%80%BB%E8%A7%88%E5%92%8C%E5%AF%BC%E8%88%AA/)

</p>
</p>
</p>
</p>