---
title: Facebook出品的Android声明式开源新框架Litho文档翻译-单元测试
date: 2017-05-09 11:26:59
tags: [Android,实用库,Litho]
category: Android知识
---

欢迎转载,转载请标明出处.
英文原文文档地址: [Litho-doc](http://fblitho.com/docs/unit-testing)
## 测试
### 单元测试

</p>
</p>

Litho通过流AssertJ方法提供了测试helper.可用的有:
- [ComponentAssert](http://fblitho.com/javadoc/com/facebook/litho/testing/assertj/ComponentAssert)用于那些针对Component builder和Component的断言.
- [LithoViewAssert](http://fblitho.com/javadoc/com/facebook/litho/testing/assertj/LithoViewAssert)用于针对已挂载Component的断言.

为了使用这些测试功能,你需要在编译的时候包含可选的litho-testing包.可用的包是:com.facebook.litho:litho-testing:+

为了演示这些类的用法,下面展示一个示例,它是一个包含了图标和简单描述文字的Component.

``` java
/**
 * Displays who liked the post.
 *
 * 1 - 3 likers => Comma separated names (e.g. Jane, Mike, Doug)
 * > 3 likers => Comma separated number denoting the like count
 */
@LayoutSpec
class LikersComponentSpec {

  @OnCreateLayout
  protected static ComponentLayout onCreateLayout(
      ComponentContext c,
      @Prop List<User> likers) {
      
    return Row.create(c)
        .alignItems(FLEX_START)
        .child(
            Image.create(c)
                 .srcRes(R.drawable.like))
        .child(
            Text.create(c)
                .text(formatLikers(likers))
                .textSizeSp(12)
                .ellipsize(TruncateAt.END))
        .build();
  }

  private static String formatLikers(List<User> likers) {
    ...
  }
}
```

在我们的测试中,我们想要验证文字和图标的渲染效果.

</p>
</p>

#### 安装
---
Component的测试框架提供了一个Junit @Rule,它覆写了[Styleables](https://developer.android.com/reference/android/R.styleable.html),并且允许轻松的访问ComponentContext.

``` java
/**
 * Tests {@link LikersComponent}
 */
@RunWith(RobolectricTestRunner.class)
public class LikersComponentTest {
  @Rule
  public ComponentsRule mComponentsRule = new ComponentsRule();
```

</p>
</p>

#### 测试组件的渲染
---
Component框架包括了一组AssertJ风格的helper类来验证你Component的属性.在后台,它们将为你挂载Component.

你也可以在ComponentContext和Context组上,或者ComponentBuilder被build()消耗之前使用断言.

``` java
  @Test
  public void testTwoLikers() {
    ComponentContext c = mComponentsRule.getContext();
    ImmutableList<User> likers =
       ImmutableList.of(new User("Jane"), new User("Mike"));

    Component<LikersComponent> component =
       LikersComponent
           .create(c)
           .likers(likers)
           .build();

    assertThat(c, component).hasText("Jane, Mike");
  }

  @Test
  public void testLikeIcon() {
    ComponentContext c = mComponentsRule.getContext();
    Drawable likeIcon = c.getResources().getDrawable(R.drawable.like);

    ImmutableList<User> likers =
        ImmutableList.of(new User("Jane"), new User("Mike"));

    LikersComponent.Builder componentBuilder =
        LikersComponent
            .create(c)
            .likers(likers);

    assertThat(componentBuilder).hasDrawable(likeIcon);
  }
```

</p>
</p>

#### 测试子Component的渲染
---

比起在你的Component的内容渲染上使用断言,可能一种更有效的方式是测试子Component的渲染情况.[SubComponent](http://fblitho.com/javadoc/com/facebook/litho/testing/SubComponent)是一个方便的类,可以更简单的比较Component的类型.你依然可以使用AssertJ来验证子Component是否存在.

``` java
public class StoryTest {
  ...

  @Test
  public void testStoryLayout() {
    ComponentContext c = mComponentsRule.getContext();
    Story story = ...

    Component<StoryComponent> component =
        StoryComponent.create(c)
            .story(story);

    assertThat(subComponents).hasSubComponents(
        SubComponent.of(HeaderComponent.class),
        SubComponent.of(MessageComponent.class),
        SubComponent.of(LikersComponent.class),
        SubComponent.of(FeedbackComponent.class));
  }

  @Test
  public void testStoryWithZeroLikes() {
    ComponentContext c = mComponentsRule.getContext();
    Story storyWithZeroLikes = ...;

    Component<StoryComponent> component = StoryComponent.create(c)
        .story(storyWithZeroLikes)
        .build();

    assertThat(component)
        .doesNotContainSubComponent(SubComponent.of(LikersComponent.class));
  }
}
```

</p>
</p>

#### 额外的断言
---
还有一些断言是可用于Component和LithoView的.他们都会在你的Component创建的树上进行操作.因此,断言一个你的Component上的Drawable的存在将会从提供的开始点遍历view的层级结构.

</p>
</p>

#### 注意事项
---
在进行Litho单元测试时,请注意,Yoga的本地库必须要被加载,这可能会由于你的构建系统的选择,而产生一些问题.比如,使用Gradle和Robolectric时,你可能会遇到问题,因为Robolectric对于每一个测试组件都使用了一个新的拥有不同设置的[ClassLoader](https://docs.oracle.com/javase/7/docs/api/java/lang/ClassLoader.html).对于PowerMock也是一样,对于每一个基本组件,它都提供了一个ClassLoader,并且规定他们都为不可重用状态.

JVM有两个非常重要的相关限制:
1. 一个共享的库在一个进程中只能被加载一次.
2. ClassLoader不共享加载的库的信息

因为这些,在测试运行中使用多个ClassLoader是非常有问题的,因为每一个实例都会尝试加载Yoga,但是除了第一个会成功之外,其它的都会报libyoga.so already loaded in another classloader(libyoga.so已经在另一个classloader被加载过了)的异常.

避免这个的唯一方法是避免使用多个ClassLoader,如果必须使用新的ClassLoader,则使用fork进程的方式.

Gradle允许你限制一个进程在作废之前可以执行的测试类的数量.如果你设置这个值为1,我们就避免了ClassLoader的重用:
``` java
android {
    [...]

    testOptions {
        unitTests.all {
            forkEvery = 1
            maxParallelForks = Math.ceil(Runtime.runtime.availableProcessors() * 1.5)
        }
    }
}
```

使用BUCK,可以通过指定每一个测试目标的名称为会导致并行进程加速的名称来打到这个效果.或者,你也可以设置fork_mode为per_test,具体请参照[这里](https://buckbuild.com/rule/java_test.html#fork_mode)的描述.

最后,根据你的构建系统和你的项目中已经存在的限制,你可能需要调整你的测试运行器使用ClassLoader的方式.然而,这不是Litho独有的问题,而是在Android项目中混合使用native代码和java代码所导致的不幸后果.

</p>
</p>
</p>
</p>

##### [回到导航页](https://shikieiki.github.io/2017/05/04/Facebook%E5%87%BA%E5%93%81%E7%9A%84Android%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%BC%80%E6%BA%90%E6%96%B0%E6%A1%86%E6%9E%B6Litho%E6%96%87%E6%A1%A3%E7%BF%BB%E8%AF%91-%E6%80%BB%E8%A7%88%E5%92%8C%E5%AF%BC%E8%88%AA/)
</p>
</p>
</p>
</p>





