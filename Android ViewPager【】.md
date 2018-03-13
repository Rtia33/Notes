<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [ViewPager 超详解：玩出十八般花样](#viewpager-%E8%B6%85%E8%AF%A6%E8%A7%A3%E7%8E%A9%E5%87%BA%E5%8D%81%E5%85%AB%E8%88%AC%E8%8A%B1%E6%A0%B7)
  - [ViewPager 的基础使用](#viewpager-%E7%9A%84%E5%9F%BA%E7%A1%80%E4%BD%BF%E7%94%A8)
    - [PagerAdapter](#pageradapter)
  - [ViewPager + TabLayout + Fragment 的结合使用](#viewpager--tablayout--fragment-%E7%9A%84%E7%BB%93%E5%90%88%E4%BD%BF%E7%94%A8)
  - [ViewPager 轮播图的使用](#viewpager-%E8%BD%AE%E6%92%AD%E5%9B%BE%E7%9A%84%E4%BD%BF%E7%94%A8)
  - [自定义 ViewPager 的切换效果](#%E8%87%AA%E5%AE%9A%E4%B9%89-viewpager-%E7%9A%84%E5%88%87%E6%8D%A2%E6%95%88%E6%9E%9C)
  - [ViewPager 切换效果进阶](#viewpager-%E5%88%87%E6%8D%A2%E6%95%88%E6%9E%9C%E8%BF%9B%E9%98%B6)
  - [结尾](#%E7%BB%93%E5%B0%BE)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# ViewPager 超详解：玩出十八般花样

http://blog.csdn.net/shedoor/article/details/78957852

> 授权声明：本文已授权微信公众号：鸿洋（hongyangAndroid）原创首发。其他转载请再咨询作者许可！

![img](http://upload-images.jianshu.io/upload_images/2625875-ad56b6b0150bd32b.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

虽然没有 RecyclerView 这种列表控件常用些，但是在开发中你ViewPager 肯定也是不可或缺的控件，引导页、轮播图、卡片画廊等效果总是缺少不了 ViewPager 的身影。 
相信每一位朋友对 ViewPager 的基础使用都已经很熟练了，今天在这里就从简至繁将 ViewPager 的每个用法都梳理一边。

主要包括以下内容：

- ViewPager 基本使用（简介、适配器）
- ViewPager + TabLayout + Fragment 的使用
- ViewPager 轮播图的使用（指示器、标题、自动轮播、首尾循环）
- ViewPager 的切换效果（PageTransformer）
- ViewPager 切换效果进阶

## ViewPager 的基础使用

对于 ViewPager ，官方的描述大概是这样的：页面允许左右滑动的布局管理器，而不同页面带有不同的数据。

> 这里简单归结如下：
>
> - ViewPager 是 v4 包中的一个类。
> - ViewPager 类直接继承了 ViewGroup 类，它是一个容器类，可以在其中添加其他的 view 。
> - 类似于 ListView，也有自己的适配器，用来填充数据页面。

关于 ViewPager 在布局文件中的声明，这里就不再说了。其实是没什么好说的，并没有什么可以直接声明的特殊属性，由于继承于 ViewGroup 有的也都是些 ViewGroup 的属性。

这里值得介绍的也就是几个可以动态设置方法了，常用的有以下几个：

- `setAdapter(PagerAdapter adapter)` 设置适配器
- `setOffscreenPageLimit(int limit)` 设置缓存的页面个数,默认是 1
- `setCurrentItem(int item)` 跳转到特定的页面
- `setOnPageChangeListener(..)` 设置页面滑动时的监听器（现在API中建议使用 `addOnPageChangeListener(..)`）
- `setPageTransformer(..PageTransformer)` 设置页面切换时的动画效果
- `setPageMargin(int marginPixels)` 设置不同页面之间的间隔
- `setPageMarginDrawable(..)` 设置不同页面间隔之间的装饰图也就是 divide ，要想显示设置的图片，需要同时设置 `setPageMargin()`

> **谨记上面这几个方法，玩转 ViewPager 其实都是围绕它们进行的，能不能玩出花样，就看你把它们运用的怎么样了。**

上面的方面大多一看说明就明白了，这里值得一提的就是 ViewPager 的适配器了。

### PagerAdapter

PagerAdapter 是抽象的类，所以使用时只能使用它的子类，实现子类必须要实现以下四个方法:

- `getCount();` 是获取当前窗体界面数，也就是数据的个数。
- `isViewFromObject(View view, Object object);` 这个方法用于判断是否由对象生成界面，官方建议直接返回 `return view == object;`。
- `instantiateItem(View container, int position);` 要显示的页面或需要缓存的页面，会调用这个方法进行布局的初始化。
- `destroyItem(ViewGroup container, int position, Object object);` 如果页面不是当前显示的页面也不是要缓存的页面，会调用这个方法，将页面销毁。

相信大家对上面这些方法的实现并不陌生，这里就不详细介绍了。另外我们知道官方给我们提供的还有 PagerAdapter 的两个直接子类 FragmentPagerAdapter 和 FragmentStatePagerAdapter 。而我们常常会在 ViewPager 和 Fragment 结合使用的时候来使用这两个适配器。具体的用法和它们之间的区别，我们在下个章节讲。

## ViewPager + TabLayout + Fragment 的结合使用

在引导页中我们常常用 ViewPager 和 Fragment 结合使用，而像新闻分类的页面我们会再加上一个 TabLayout 三者联动使用。而此时，我们不会再使用 PagerAdapter 了，而是直接使用官方提供的专门用于与 Fragment 结合使用的 FragmentPagerAdapter。

FragmentPagerAdapter 它将每一个页面表示为一个 Fragment，并且每一个 Fragment 都将会保存到 FragmentManager 当中。而且，当用户没可能再次回到页面的时候，FragmentManager 才会将这个 Fragment 销毁。

使用 FragmentPagerAdapter 需要实现两个方法：

- `public Fragment getItem(int position)` 返回的是对应的 Fragment 实例，一般我们在使用时，会通过构造传入一个要显示的 Fragment 的集合，我们只要在这里把对应的 Fragment 返回就行了。
- `public int getCount()` 这个上面介绍过了返回的是页面的个数，我们只要返回传入集合的长度就行了。

使用起来是非常简单的，FragmentStatePagerAdapter 的使用也和上面一样，那两者到底有什么区别呢？

区别如下：

- **FragmentPagerAdapter**：对于不再需要的 fragment，选择调用 onDetach() 方法，仅销毁视图，并不会销毁 fragment 实例。
- **FragmentStatePagerAdapter**：会销毁不再需要的 fragment，当当前事务提交以后，会彻底的将 fragmeng 从当前 Activity 的FragmentManager 中移除，state 标明，销毁时，会将其 `onSaveInstanceState(Bundle outState)` 中的 bundle 信息保存下来，当用户切换回来，可以通过该 bundle 恢复生成新的 fragment，也就是说，你可以在 `onSaveInstanceState(Bundle outState)` 方法中保存一些数据，在 onCreate 中进行恢复创建。

> 由上总结： 
> 使用 FragmentStatePagerAdapter 更省内存，但是销毁后新建也是需要时间的。一般情况下，如果你是制作主页面，就 3、4 个 Tab，那么可以选择使用 FragmentPagerAdapter，如果你是用于 ViewPager 展示数量特别多的条目时，那么建议使用 FragmentStatePagerAdapter。

那 Tablayout 如何和 Viewpager 联动呢？由于我们这里主要是讲解 ViewPager 的，所谓 “术业有专攻” 所以关于 TabLayout 的使用我们就不再掺和了。 
第一步，初始化 TabLayout 和 ViewPager 后只要通过调用 TabLayout 的 `tabLayout.setupWithViewPager(viewPager)` 方法就将两者绑定在一起了。 
第二步，重写 PagerAdapter 的 `public CharSequence getPageTitle(int position)` 方法，而 TabLayout 也正是通过 `setupWithViewPager()` 方法底部会调用 PagerAdapter 中的`getPageTitle()` 方法来实现联动的。

## ViewPager 轮播图的使用

关于此章本想给大家细细到来，才写上面两章都这么多篇幅了，我们还有给下面两章重点讲的部分留点空间呢。如果非得想看还不嫌我啰嗦，那我有时间再把这段给不出来。

这章就这样结束，当然没有，虽然不负责任，但是也不能撩完妹子就闪人啊！这里还是要基本原理给大家论道论道的。

![Banner 元素组成图](http://upload-images.jianshu.io/upload_images/2625875-01a8f9ee5ebfc2f0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从上图我们可以知道，一般我们使用 ViewPager 做 Banner 时主要有以上几个元素：

**标题 & 指示器**： 
我们可以把标题和指示器直接写在我们 Banner 的 item 的布局中，这样通过在 PageAdapter 的 `instantiateItem()` 方法初始化页面时，直接设置。但是一般我们不会这样做（如果标题没有阴影的话，可以如上面说的那样），因为这样在页面滑动的时候，会显得特别生硬，尤其是指示器。 
那该如何呢？一般我们会在 ViewPager 所在的布局文件中，声明指示器和标题布局，如下：

```
<FrameLayout
     ...>

    <android.support.v4.view.ViewPager
        android:id="@+id/viewPager"
        ..."/>

    <LinearLayout
        android:layout_gravity="bottom"
        ...>
        <!--指示器布局，因为不知道 item 的个数，所以会动态的把指示器的View添加到这里-->
        <LinearLayout
            android:id="@+id/bannerIndicators"
            .../>
        <!--标题-->
        <TextView
            android:id="@+id/bannerTitle"
            .../>
    </LinearLayout>

</FrameLayout>

```

那如何才能实现当页面滑动时，标题和指示器伴随改变呢？还记不记得，一开始介绍 ViewPager 时，它有一个可以设置监听页面改变的方法 `addOnPageChangeListener()`，在 OnPageChangeListener 监听器中有一个页面滑动结束时的回调方法 `onPageSelected(int position)` ,我们只需要在这个方法中，来设置标题和指示器跟随变化就行了。

**自动轮播**： 
实现自动轮播的原理其实更简单，只要我们每隔一定时间发送一个切换页面的事件就行了。实现这个功能有很多种方法，相信作为 Android 开发者，你最快想到的就是 `Handler.sendEmptyMessageDelayed(int what, long delayMillis)` 了吧。

```
Handler mHandler = new Handler() {
        @Override
        public void handleMessage(Message msg) {
            if (mAutoPlay) {
                //mViewPager.setCurrentItem(mViewPager.getCurrentItem() + 1);//无限轮播时
                mViewPager.setCurrentItem((mViewPager.getCurrentItem()+1) % mViewPagerItemCount)
                this.sendEmptyMessageDelayed(MSG_WHAT, delayMillis);
            }
        }
    };

```

当然不要忘了在外部，初始化完成后调用一次`mHandler.sendEmptyMessageDelayed(MSG_WHAT, delayMillis);`。

**首尾循环无限轮播**： 
当然，我们在设置自动轮播时，已经做到了首尾循环无限轮播了呀。

其实这里所说的无限轮播是指：当我们手动滑到最后一个页面时，依然可以向后伴随手指滑动，并跳转显示的是第一个页面；反之滑到首个页面也是一样。 
旁边那位脑子灵光的大兄弟又说了，那还不简单，在页面监听器 OnPageChangeListener 里，通过 position 分辨滑动的是不是首页或者最后一页，然后通过 setCurrentItem() 设置一下不就行了，还那么麻烦。 
大胸弟，你且消消气！这里并不使用这种方式是因为，这种方式的跳转是十分生硬的，同时是不能实现“伴随手指滑动”这个条件的。

那究竟如何如何才能实现呢？目前江湖流传的有一下两种方法：

1. 使 adapter 的 getCount() 返回 Integer.MAX_VALUE，再在初始化时设置当前页面为几千页(如：`ViewPager.setCurrentItem(1000*data.size)`),其实就是障眼法，大爷心情好的话，向前滑动几千页也不是不可能的;
2. 通过监听 viewpager 的滑动来设置页面。如当前有数据 123，则设置页面为 31231，当页面滑动到第一个 3 时，设置当前页面为第二个 3，那么左右都可以滑动，当其滑动到第二个 1 时同理。

关于第一种是目前流行最广的方法，如果大家想查看详细的说明可以参考下面这篇文章(网上随便找的，对可靠性不作担保啊)： 
[ViewPager真正的无限轮播](http://blog.csdn.net/zhiyuan0932/article/details/52673169)

关于第二种方式，严格意义上分析是会在滑动过程中产生生硬的跳动的。不过有位江湖义士声称已经解决了这种不和谐情况的发生，附上文章地址（可靠性更不作担保啊）： 
[打造真正的无限循环viewpager](http://blog.csdn.net/anyfive/article/details/52525262) (不负责的我真的没有测试这个可靠性，大家闲的测试下，如果效果不好的话，告诉我，我赶快把这个链接删除~~)

## 自定义 ViewPager 的切换效果

本来最近封装一个了 ViewPager 十八般花样、样样都有的 PageTransformer 动效库，想着前面少啰嗦点，然后把这章作为重点来讲的。没想到前面还是啰里啰嗦这么多（恍然间，我好像找到自己一直撩妹不成功单身的原因了~），好了，步入正题。

关于 ViewPager 的切换动画，官方提供了一个内部接口 ViewPager.PageTransformer 来供我们实现自定义切换动效。这个接口里只提供了一个方法 `public void transformPage(View view, float position)`,但是千万不要小看了这两个方法，这里面的道道有很多呢。

transformPage 方法两个参数，一个是 View ，这个好理解就是当前要设置动效的页面。这个页面并不单单是指当前显示的页面，即将滑出的页面、即将滑入的页面、已经隐藏的页面，也就是说这个 View 是指所有的页面。那如何分辨 View 到底是指哪个页面呢，这个需要根据第二个参数 position 来辨别。

你千万不要把 position 理解成了 ViewPager 页面的下标，一定要看仔细，这个 position 可是 float 类型，下标怎么可能是浮点型呢！

从 doc 注释来看，当前选中的 item 的 position 永远是 0 ，被选中 item 的前一个为 -1，被选中 item 的后一个为 1。

> 其实这里文档的描述并不是完全正确的，前后 item position 为 -1 和 1 的前提是你没有给 ViewPager 设置 pageMargin。 
> 如果你设置了 pageMargin，前后 item 的 position 需要分别加上（或减去，前减后加）一个偏移量（偏移量的计算方式为 pageMargin / pageWidth）。

在用户滑动界面的时候，position 是动态变化的，下面以左滑为例（以向左为正方向）:

- 选中 item 的 position：`从 0 渐至 -1 - offset (pageMargin / pageWidth)`
- 前一个 item 的 position：`从 -1 渐至 -1 - offset (pageMargin / pageWidth)`
- 前两个 item 的 position：`从 -2 渐至 -2 - offset (pageMargin / pageWidth)`，再往前就以此类推
- 后一个 item 的 position：`从 1 + offset (pageMargin / pageWidth) 渐至 0`，再往后就以此类推

每一次滑动，每个 View 对应的 position 是一个在一个区间范围内动态渐变的过程，所以我们可以将 position 的值应用于 setAlpha(), setTranslationX(), 或者 setScaleY() 等等方法，从而实现自定义的切换动画有一个渐变的效果。

这里给大家举一个视差切换动效的实现方式，我们先来看一下效果：

!

其实实现起来很简单，就是滑动时给页面再设置一个页面横向滑动的动画，让页面实现滑动的速度慢于手指滑动的速度，这样就会有种视差的效果：

```
@Override
public void transformPage(View page, float position) {
    int width = page.getWidth();
    //我们给不同状态的页面设置不同的效果
    //通过position的值来分辨页面所处于的状态
    if (position < -1) {//滑出的页面
        page.setScrollX((int) (width * 0.75 * -1));
    } else if (position <= 1) {//[-1,1]
        if (position < 0) {//[-1,0]
            page.setScrollX((int) (width * 0.75 * position));
        } else {//[0,1]
            page.setScrollX((int) (width * 0.75 * position));
        }
    } else {//即将滑入的页面
        page.setScrollX((int) (width * 0.75));
    }
}

```

其实这里处于大于1或者小于-1的状态的页面都好理解。需要详细讲解的就是 [-1,1] 这个区间状态的页面。大家可以想象一下：

![像左滑动](http://upload-images.jianshu.io/upload_images/2625875-baad38875b79d28e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

由上图可以看出，当滑动时，（如果没有偏移量）界面上最多出现两个 item，一个即将滑出即将隐藏的页面（postion变化为：从0渐到-1），一个滑入即将完全显示的页面（postion变化为：从1渐到0）。

这样给每个不同状态的页面设置不同的动效就达到我们想要的目的了。回到上面例子中的代码，刚才那位大兄弟又说话了，你的代码明明 [0 -> -1] 和 [1 -> 0] 两个状态的 item 设置的是一样的动效啊。这里只是代码一样，动效实际上是不一样的，因为一个position 是大于 0 的，一个 position 是小于 0 的，滑动的方向自然是相反的。难道你非得让我写成 `page.setScrollX((int) (width * -0.75 * -position))` 这样吗？

想实现更多炫酷的动效，可以查看为大家封装好的 [PageTransformerHelp](https://github.com/OCNYang/PageTransformerHelp) 库，GitHub地址：<https://github.com/OCNYang/PageTransformerHelp>

## ViewPager 切换效果进阶

其实上面已经把该讲的自定义的方面都讲的很清楚了；整个梳理下来，上面一开始给大家列举的着重强调的几个 ViewPager 的动态设置的方法就剩 `setPageMargin(int marginPixels)` 没有说了，那么这个方法又会给我们带来什么样的神奇效果呢？

![界面能够同时看到多个 item](http://upload-images.jianshu.io/upload_images/2625875-2943a568192d49d7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

那是如何实现上面这种效果呢，可以肯定的是：两边两个 item 被缩小且透明度变低，是通过设置上面所说的自定义动效完成的。那如何在一个界面能够同时看到 3 个 item 呢？那么就通过下图的分析和解释告诉实现的原理：

![卡片式轮播效果实现原理分析](http://upload-images.jianshu.io/upload_images/2625875-1adab76ec6530b6a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

看了上面的解释有没有一种恍然大悟，迫不及待想试试的冲动？如果通过上图仍然略有疑惑可以看看鸿洋大神的这篇文章，可以说是很全面了： 
[巧用ViewPager 打造不一样的广告轮播切换效果](http://blog.csdn.net/lmj623565791/article/details/51339751)

## 结尾

到此，ViewPager 的基本使用方式已经讲的差不多了。想查看更多 切换动画 的效果，可以到本文的源码地址进行查看。

**源码地址**： 
<https://github.com/OCNYang/PageTransformerHelp>

参考文章： 
<http://blog.csdn.net/lmj623565791/article/details/51339751> 
<http://blog.csdn.net/qq_30716173/article/details/51589251>