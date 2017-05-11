---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-仓库架构
date: 2017-05-11 16:17:41
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.
英文原文文档地址: [Litho-doc](http://fblitho.com/docs/repo-structure)
## 参与
### 仓库架构

</p>
</p>

这是一个描述仓库中什么东西在哪儿的快速细分文档.
 
/docs/

---
这个目录存放了你现在看到的这些GitHub页的Jekyll文件.

</p>
</p>

/lib/

---
在这个子文件夹中可以找到很多扩展库.他们大致可以被分为两个类别.
- **可拉取的库:**这些库都存放在jCenter上./lib/中相应的子文件夹中只包含了一个写有拉取这个库的命令的BUCK文件.
- **打包的库:**这些库都被完整的存放在文件夹里.这是[正确的buck方式](https://buckbuild.com/command/fetch).然而,他们极大的增加了仓库的大小,因此它们只在绝对必要的时候才被包含进来.

</p>
</p>

/sample-barebones/

---
在这里可以找到[准系统教程](http://fblitho.com/tutorial/)的成品源码.如果你修改了这个教程,你必须在这里更新源码.

</p>
</p>

/sample/

---
在这个文件夹下可以找到Litho示例程序的代码.它包含了playground,你必须使用它来进行所有的测试/调试报告.

</p>
</p>

/litho-*/

---
Litho被切分成为了几个子工程,所以终端用户可以选择框架中他们需要的部分来使用.可用的工程如下:
- litho-annotation是一个纯净的,包含了用户使用注释处理器所必须的注释的java库.
- litho-core包含了核心框架代码
- litho-fresco包含了用户使用Fresco图形库所需的Component.
- litho-it包含了对框架的集成测试.引入这个子工程是非常必要的,因为它可以避免循环引用.
- litho-it-powermock包含了框架使用PowerMock的集成测试.查看文件夹下的README可以了解更多.
- litho-processor包含了单例的注释处理器
- litho-stetho包含了Stetho的集成,它可以让你更轻松的开发和调试.
- litho-stubs包含了一些为了显示列表的魔法效果所需的Android框架类.
- litho-testing包含了测试Litho Component的工具.
- litho-widget包含了几个常用的Android控件的mount spec.

</p>
</p>

/COMPONENTS_DEFS和/BUCK

---
这些文件定义了如何构建Litho.BUCK文件是[buck](https://buckbuild.com/)的输入,/COMPONENTS_DEFS文件包含了一些buck在仓库中查找目标所需的常量.它会被导入到/BUCK.

</p>
</p>
</p>
</p>

##### [回到导航页](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%80%BB%E8%A7%88%E5%92%8C%E5%AF%BC%E8%88%AA/)
</p>
</p>
</p>
</p>
