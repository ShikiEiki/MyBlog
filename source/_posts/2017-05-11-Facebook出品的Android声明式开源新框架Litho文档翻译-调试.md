---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-调试
date: 2017-05-11 14:21:44
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.
英文原文文档地址: [Litho-doc](http://fblitho.com/docs/debugging)
## 工具
### 调试

</p>
</p>

#### Stetho
---
[Stetho](http://facebook.github.io/stetho/)是一个非常好的Android调试工具,我们已经确保了它能够与Litho一起使用.为了在Stetho中启用Litho调试,需要在你的Application实现中的onCreate()方法里添加下列的代码:
``` java
public class SampleApplication extends Application {
  @Override
  public void onCreate() {
    super.onCreate();
    SoLoader.init(this, false);
    
    Stetho.initialize(
        Stetho.newInitializerBuilder(this)
              .enableWebKitInspector(new LithoWebKitInspector(this))
              .build());
  }
}
```

这会使Litho完全集成进stetho中.启用了Litho支持之后,你只用打开你的app然后再你的浏览器中打开chrome://inspect

![](/image/20170511142823.png)

点击你想查看的程序的查看链接(我们使用的是Litho sample app).这将会打开一个UI查看器,你可以在其中查看你程序的View和Component的层级结构.

![](/image/20170511143232.png)

当查看一个Litho Component的时候,你也可以直接在查看器中更改你的UI中的内容!这样就可以在不重新编译和运行你的程序的情况下,通过调整margin,padding或者其他属性来实现快速设计迭代.你也可以使用它来快速测试你的UI是否能处理不同长度的文本.
![](/image/20170511143319.png)

</p>
</p>
</p>
</p>

##### [回到导航页](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%80%BB%E8%A7%88%E5%92%8C%E5%AF%BC%E8%88%AA/)
</p>
</p>
</p>
</p>