---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-准备工作
date: 2017-04-27 16:05:10
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.
英文原文文档地址: [Litho-doc](http://fblitho.com/docs/getting-started)
## 快速开始
### 准备工作

</p>
</p>

#### 集成Litho至你的工程
---

</p>
</p>

##### gradle

</p>
</p>

你可以通过在你的Gradle的build.gradle文件中添加以下代码来把Litho添加至你的Android工程中.
``` gradle
dependencies {
  // ...
  // Litho
  compile 'com.facebook.litho:litho-core:0.2.0'
  compile 'com.facebook.litho:litho-widget:0.2.0'
  provided 'com.facebook.litho:litho-annotations:0.2.0'

  annotationProcessor 'com.facebook.litho:litho-processor:0.2.0'
  
  // SoLoader 
  compile 'com.facebook.soloader:soloader:0.2.0'

  // 调试选项
  debugCompile 'com.facebook.litho:litho-stetho:0.2.0'

  // 集成Fresco的支持
  compile 'com.facebook.litho:litho-fresco:0.2.0'

  // 供测试
  testCompile 'com.facebook.litho:litho-testing:0.2.0'
}
```

</p>
</p>

##### Buck

</p>
</p>

你可以通过在你的Buck的BUCK文件中添加以下代码来把Litho添加至你的Android工程中.
``` buck
android_prebuilt_aar(
    name = "litho",
    aar = ":litho.aar",
    visibility = ["PUBLIC"],
)

remote_file(
    name = "litho-core.aar",
    sha1 = "sha1here",
    url = "mvn:com.facebook.litho:litho-core:aar:0.2.0",
)

prebuilt_jar(
    name = "litho-annotation",
    binary_jar = ":litho-annotation.jar",
    visibility = ["PUBLIC"],
)

remote_file(
    name = "litho-processor.aar",
    sha1 = "sha1here",
    url = "mvn:com.facebook.litho:litho-processor:aar:0.2.0",
)

prebuilt_jar(
    name = "litho-processor",
    binary_jar = ":litho-processor.jar",
    visibility = ["PUBLIC"],
)

remote_file(
    name = "litho-annotation.jar",
    sha1 = "sha1here",
    url = "mvn:com.facebook.litho:litho-annotation:jar:0.2.0",
)

android_prebuilt_aar(
    name = "litho-widget",
    aar = ":litho-widget.aar",
    visibility = ["PUBLIC"],
)

remote_file(
    name = "litho-widget.aar",
    sha1 = "sha1here",
    url = "mvn:com.facebook.litho:litho-widget:aar:0.2.0",
)

litho_android_library(
    ...
    # Your target here
    ...
    annotation_processor_deps = [
        ":litho-annotation",
        ":litho-processor",
    ],
    annotation_processors = [
        "com.facebook.litho.processor.ComponentsProcessor",
    ],
    deps = [
        ":litho",
        ":litho-widget",
        ...
    ]
)
```

</p>
</p>

#### 测试集成情况
---
你可以通过添加一个由Litho创建的view至activity来测试集成情况

首先,初始化SoLoader.Litho依赖[SoLoader](https://github.com/facebook/SoLoader)来加载底层布局引擎[Yoga](https://facebook.github.io/yoga/)所需的本地库.在Application中进行这个操作比较合适.

``` java
[MyApplication.java]
public class MyApplication extends Application {

  @Override
  public void onCreate() {
    super.onCreate();
    
    SoLoader.init(this, false);
  }
}
```

然后,添加一个预定义的Litho Text组件至activity来显示"Hello world".

``` java 
[MyActivity.java]
import com.facebook.litho.ComponentContext;
import com.facebook.litho.LithoView;

public class MyActivity extends Activity {

  @Override
  public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    
    final ComponentContext c = new ComponentContext(this);

    final LithoView lithoView = LithoView.create(
    	this /* context */, 
    	Text.create(c)
            .text("Hello, World!")
            .textSizeDip(50)
            .build());
    	
    setContentView(lithoView);
  }
}

```

现在,当你运行app的时候,你就可以看到"Hello World"显示在屏幕上.

</p>
</p>
</p>
</p>

[回到导航页](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%80%BB%E8%A7%88%E5%92%8C%E5%AF%BC%E8%88%AA/)

</p>
</p>
</p>
</p>