<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [【Android 视图 状态栏】](#android-%E8%A7%86%E5%9B%BE-%E7%8A%B6%E6%80%81%E6%A0%8F)
    - [全屏，不保留状态栏文字(Splash页面，欢迎页面)](#%E5%85%A8%E5%B1%8F%E4%B8%8D%E4%BF%9D%E7%95%99%E7%8A%B6%E6%80%81%E6%A0%8F%E6%96%87%E5%AD%97splash%E9%A1%B5%E9%9D%A2%E6%AC%A2%E8%BF%8E%E9%A1%B5%E9%9D%A2)
    - [全屏保留状态栏文字(页面上部有Banner图)](#%E5%85%A8%E5%B1%8F%E4%BF%9D%E7%95%99%E7%8A%B6%E6%80%81%E6%A0%8F%E6%96%87%E5%AD%97%E9%A1%B5%E9%9D%A2%E4%B8%8A%E9%83%A8%E6%9C%89banner%E5%9B%BE)
    - [标题栏与状态栏颜色一致 xml中配置](#%E6%A0%87%E9%A2%98%E6%A0%8F%E4%B8%8E%E7%8A%B6%E6%80%81%E6%A0%8F%E9%A2%9C%E8%89%B2%E4%B8%80%E8%87%B4-xml%E4%B8%AD%E9%85%8D%E7%BD%AE)
    - [不同Fragment中对StatusBar的处理不一样](#%E4%B8%8D%E5%90%8Cfragment%E4%B8%AD%E5%AF%B9statusbar%E7%9A%84%E5%A4%84%E7%90%86%E4%B8%8D%E4%B8%80%E6%A0%B7)
    - [设置状态栏文字的颜色](#%E8%AE%BE%E7%BD%AE%E7%8A%B6%E6%80%81%E6%A0%8F%E6%96%87%E5%AD%97%E7%9A%84%E9%A2%9C%E8%89%B2)
    - [切换fragment时，toolBar和statusbar显示与否、statusBar颜色、status文字颜色](#%E5%88%87%E6%8D%A2fragment%E6%97%B6toolbar%E5%92%8Cstatusbar%E6%98%BE%E7%A4%BA%E4%B8%8E%E5%90%A6statusbar%E9%A2%9C%E8%89%B2status%E6%96%87%E5%AD%97%E9%A2%9C%E8%89%B2)
  - [扩展](#%E6%89%A9%E5%B1%95)
    - [Activity中window是怎么回事？里面有什么View/ViewGroup](#activity%E4%B8%ADwindow%E6%98%AF%E6%80%8E%E4%B9%88%E5%9B%9E%E4%BA%8B%E9%87%8C%E9%9D%A2%E6%9C%89%E4%BB%80%E4%B9%88viewviewgroup)
    - [setFitsSystemWindows()是什么](#setfitssystemwindows%E6%98%AF%E4%BB%80%E4%B9%88)
- [引用：](#%E5%BC%95%E7%94%A8)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->



# 【Android 视图 状态栏】
[\[Digging\] Android Translucent Status Bar](https://blog.kyleduo.com/2017/05/02/digging-translucentstatusbar/)
[Android状态栏微技巧，带你真正理解沉浸式模式](https://blog.csdn.net/guolin_blog/article/details/51763825)
[Android 状态栏着色实践](https://www.jianshu.com/p/bae25b5eb867)





[演示代码传送门](https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2FFengJ24%2FStatusBarDemo)



### 全屏，不保留状态栏文字(Splash页面，欢迎页面)

这个效果大家脑补下，就不贴图了
首先在style.xml中设置为noActionBar的主题，这是必须的

```
<style name="fullScreen" parent="Theme.AppCompat.DayNight.NoActionBar">
</style>
```

方式有三种

```java
@Override
protected void onCreate(@Nullable Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_fullscreen_no_text);
    //方式一
    //getWindow().addFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN);
    //方式二
    //getWindow().getDecorView().setSystemUiVisibility(View.SYSTEM_UI_FLAG_FULLSCREEN);
    //方式三 style.xml中配置
    //<style name="fullScreen" parent="Theme.AppCompat.DayNight.NoActionBar">
    //        <item name="android:windowFullscreen">true</item>
    //</style>
}
```

### 全屏保留状态栏文字(页面上部有Banner图)
![](https://upload-images.jianshu.io/upload_images/9028834-81d5062c646cf6bc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在项目，大部分向下支持到19，所以先不考虑太低版本的情况

```java
Window window = getWindow();
//默认API 最低19 
if (Build.VERSION.SDK_INT > Build.VERSION_CODES.JELLY_BEAN_MR2) {
    window.addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
    ViewGroup contentView = window.getDecorView().findViewById(Window.ID_ANDROID_CONTENT);
    contentView.getChildAt(0).setFitsSystemWindows(false);
}
```

### 标题栏与状态栏颜色一致 xml中配置

```
<style name="status_toolbar_same_color" parent="Theme.AppCompat.Light.DarkActionBar">
    <!-- Customize your theme here. -->
    <item name="colorPrimary">@color/status_toolBar_same_color</item>
    <item name="colorPrimaryDark">@color/status_toolBar_same_color</item>
    <item name="colorAccent">@color/colorAccent</item>
</style>
```
![](https://upload-images.jianshu.io/upload_images/9028834-de1d6fd39ce6efca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们能看到这种处理方式,是可以解决一些业务场景,但是如果在低于21版本手机上就不管用了,那怎么办呢？请接着往下看

```java
Window window = getWindow();
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
        window.addFlags(WindowManager.LayoutParams.FLAG_DRAWS_SYSTEM_BAR_BACKGROUNDS);
        window.setStatusBarColor(getResources().getColor(R.color.status_toolBar_same_color));
    } else {
        window.addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
        ViewGroup systemContent = findViewById(android.R.id.content);

        View statusBarView = new View(this);
        ViewGroup.LayoutParams lp = new ViewGroup.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, getStatusBarHeight());
        statusBarView.setBackgroundColor(getResources().getColor(R.color.status_toolBar_same_color));

        systemContent.getChildAt(0).setFitsSystemWindows(true);

        systemContent.addView(statusBarView, 0, lp);

    }
```

适配后的结果：

![](https://upload-images.jianshu.io/upload_images/9028834-a59e2317a1a64225.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 不同Fragment中对StatusBar的处理不一样

[![不同Fragment.gif](https://upload-images.jianshu.io/upload_images/9028834-6266e541130d1dcf.gif?imageMogr2/auto-orient/strip)](https://upload-images.jianshu.io/upload_images/9028834-6266e541130d1dcf.gif?imageMogr2/auto-orient/strip)

```xml
<android.support.v7.widget.Toolbar
    android:id="@+id/base_toolbar"
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    android:background="@android:color/holo_blue_dark">

    <TextView
        android:id="@+id/base_title"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:textColor="@android:color/black" />
</android.support.v7.widget.Toolbar>

<FrameLayout
    android:id="@+id/base_container"
    android:layout_width="match_parent"
    android:layout_height="0dp"
    android:layout_weight="1">

</FrameLayout>
```

上述代码是两个Fragment所依附的Activity对应的部分layout

```java
private void addStatusBar() {
    //条件状态栏透明，要不然不会起作用
    getWindow().addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
    if (mStatusBarView == null) {
        mStatusBarView = new View(FragmentStatusAndActionBarActivity.this);
        int screenWidth = getResources().getDisplayMetrics().widthPixels;
        int statusBarHeight = getStatusBarHeight();
        ViewGroup.LayoutParams params = new ViewGroup.LayoutParams(screenWidth, statusBarHeight);
        mStatusBarView.setLayoutParams(params);
        mStatusBarView.requestLayout();

        //获取根布局
        ViewGroup systemContent = findViewById(android.R.id.content);
        ViewGroup userContent = (ViewGroup) systemContent.getChildAt(0);
        userContent.setFitsSystemWindows(false);
        userContent.addView(mStatusBarView, 0);
    }
}
```

上面是对应Activity中的布局，意思就是不使用系统提供的ActionBar，使用ToolBar来代替(网上一大推代替的方法)，下面的代码中设置，状态栏透明，并且设置了sitFitSystemWindow(false)，通过这些操作，我们相当于把系统的StatusBar，ActionBar，都干掉了，那么接下来，我们就可以模拟创建出StatusBar`userContent.addView(mStatusBarView, 0);`那么现在我们就可以自己控制statusBar和ActionBar，显示什么颜色？消失还是隐藏？

ToolBar显示的Fragment：

```java
 @Override
public void onHiddenChanged(boolean hidden) {
    super.onHiddenChanged(hidden);
    mActivity.mToolbar.setVisibility(View.VISIBLE);//设置ToolBar显示
    //设置statusBar的颜色
    mActivity.mStatusBarView.setBackgroundColor(getResources().getColor(android.R.color.holo_blue_bright));
}
```

ToolBar隐藏的Fragment

```java
@Override
public void onHiddenChanged(boolean hidden) {
    super.onHiddenChanged(hidden);
    mActivity.mToolbar.setVisibility(View.GONE);//设置ToolBar消失
    //设置statusBar的颜色
    mActivity.mStatusBarView.setBackgroundColor(getResources().getColor(android.R.color.holo_orange_light));
}
复制代码
```

### 设置状态栏文字的颜色

![](https://upload-images.jianshu.io/upload_images/9028834-7553b4fb1187e37c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
 //设置白底黑字
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
    getWindow().getDecorView().setSystemUiVisibility(View.SYSTEM_UI_FLAG_LIGHT_STATUS_BAR);
}
```

但是需要注意的是：目前只有android原生6.0以上支持修改状态栏字体
除此国内厂商小米、魅族也开放了修改状态栏字体的方式

[小米 MIUI6](https://link.juejin.im?target=https%3A%2F%2Fdev.mi.com%2Fdoc%2Fp%3D4769%2Findex.html)
[魅族 Flyme](https://link.juejin.im?target=http%3A%2F%2Fopen-wiki.flyme.cn%2Findex.php%3Ftitle%3D%25E7%258A%25B6%25E6%2580%2581%25E6%25A0%258F%25E5%258F%2598%25E8%2589%25B2)

### 切换fragment时，toolBar和statusbar显示与否、statusBar颜色、status文字颜色

评论区，有同学提出能否"不同Fragment中切换状态栏颜色和状态栏文字的颜色，甚至同时切换风格（纯色状态栏变成banner往上顶的状态栏）的情况"，这种情况肯定是没有问题的，也不难，现在状态栏和标题栏都是我们自己，我们想让它怎么样，它不得乖乖听话，对不~

先上图：

[![gif](https://upload-images.jianshu.io/upload_images/9028834-3b79bff190846447.gif?imageMogr2/auto-orient/strip)](https://upload-images.jianshu.io/upload_images/9028834-3b79bff190846447.gif?imageMogr2/auto-orient/strip)

其实调整的不多，这里我只贴下关键代码，gitub代码仓库已更新，大家可以clone看完成代码

这是只有Banner的fragment：

```java
 @Override
    public void onHiddenChanged(boolean hidden) {
        super.onHiddenChanged(hidden);
        //设置ToolBar隐藏
        mActivity.mToolbar.setVisibility(View.GONE);
        //设置statusBar的隐藏
        mActivity.mStatusBarView.setVisibility(View.GONE);
        //恢复默认statusBar文字颜色
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
            mActivity.getWindow().getDecorView().setSystemUiVisibility(View.VISIBLE);
        mActivity.mStatusBarView.setVisibility(View.GONE);
    }
```

改变statusBar字体颜色

```java
@Override
public void onHiddenChanged(boolean hidden) {
    super.onHiddenChanged(hidden);
    //设置ToolBar显示
    mActivity.mToolbar.setVisibility(View.VISIBLE);
    //设置ToolBar的颜色
    mActivity.mToolbar.setBackgroundColor(getResources().getColor(R.color.colorAccent));
    //设置statusBar的颜色
    mActivity.mStatusBarView.setBackgroundColor(getResources().getColor(R.color.colorAccent));
    //设置statusBar显示
    mActivity.mStatusBarView.setVisibility(View.VISIBLE);
    //设置statusBar字体颜色
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
        mActivity.getWindow().getDecorView().setSystemUiVisibility(View.SYSTEM_UI_FLAG_LIGHT_STATUS_BAR);
}
```

## 扩展
### Activity中window是怎么回事？里面有什么View/ViewGroup

写了个方法，将整个Window内的View都打印出来了

```java
private void printChildView(ViewGroup viewGroup) {
    Log.i("printView-ViewGroup", viewGroup.getClass().getSimpleName() + "的子View和数量:" + viewGroup.getChildCount());
    for (int i = 0; i < viewGroup.getChildCount(); i++) {
        String simpleName = viewGroup.getChildAt(i).getClass().getSimpleName();
        Log.i("printView-ChildView", simpleName);
    }
    for (int i = 0; i < viewGroup.getChildCount(); i++) {
        if (viewGroup.getChildAt(i) instanceof ViewGroup) {
            printChildView((ViewGroup) viewGroup.getChildAt(i));
        }
    }
}
```

这是结果

```
printView-ViewGroup: DecorView的子View和数量:1
printView-ChildView: LinearLayout
printView-ViewGroup: LinearLayout的子View和数量:2
printView-ChildView: ViewStub
printView-ChildView: FrameLayout
printView-ViewGroup: FrameLayout的子View和数量:1
printView-ChildView: ActionBarOverlayLayout
printView-ViewGroup: ActionBarOverlayLayout的子View和数量:2
printView-ChildView: ContentFrameLayout
printView-ChildView: ActionBarContainer
printView-ViewGroup: ContentFrameLayout的子View和数量:2
printView-ChildView: View
printView-ChildView: ConstraintLayout
printView-ViewGroup: ConstraintLayout的子View和数量:1
printView-ChildView: AppCompatTextView
printView-ViewGroup: ActionBarContainer的子View和数量:2
printView-ChildView: Toolbar
printView-ChildView: ActionBarContextView
printView-ViewGroup: Toolbar的子View和数量:1
printView-ChildView: AppCompatTextView
printView-ViewGroup: ActionBarContextView的子View和数量:0
复制代码
```

我们根据结果画一个分布图
![](https://upload-images.jianshu.io/upload_images/9028834-e85454ea76df50b2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


上述这个ContentFrameLayout就是我们Activity中通过setContentView(View)添加的，至于其中的View是我们自己设备的statusbar，把这个图画出来，希望能起一个抛砖引玉的作用，有想法的可以继续往下研究，我这里就不研究了，有想法的可以评论。

### setFitsSystemWindows()是什么

fitsSystemWindows代表的是:当设置SystemBar(包含StatusBar&NavigationBar)透明之后,通过添加flag的方式 将fitsSystemWindows至为true,则还是为SystemBar预留空间,当设置为false的时候,就是不为SystemBar预留空间,比如我们设置状态栏和标题栏的时候，如果不设置fitSystemWindows为true的话，就变成了

![](https://upload-images.jianshu.io/upload_images/9028834-adaf0dbf2b590d7a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这肯定不是我们想要的效果，但是为什么会这样呢？
我们思考一中，发现ToolBar和我们的开发者通过setContentView是在一个ActionBarOverlayLayout中，那我就去看看这个View



![](https://upload-images.jianshu.io/upload_images/9028834-4cd73429b06e8b19.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在系统的 frameworks\support\v7\appcompat\res\layout\abc_screen_toolbar.xml下我们看到了ActionBarOverlayLayout的布局

```
<android.support.v7.internal.widget.ActionBarOverlayLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:id="@+id/decor_content_parent"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:fitsSystemWindows="true">

    <include layout="@layout/abc_screen_content_include"/>

    <android.support.v7.internal.widget.ActionBarContainer
            android:id="@+id/action_bar_container"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_alignParentTop="true"
            style="?attr/actionBarStyle"
            android:touchscreenBlocksFocus="true"
            android:gravity="top">

        <android.support.v7.widget.Toolbar
                android:id="@+id/action_bar"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                app:navigationContentDescription="@string/abc_action_bar_up_description"
                style="?attr/toolbarStyle"/>

        <android.support.v7.internal.widget.ActionBarContextView
                android:id="@+id/action_context_bar"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:visibility="gone"
                android:theme="?attr/actionBarTheme"
                style="?attr/actionModeStyle"/>

    </android.support.v7.internal.widget.ActionBarContainer>

</android.support.v7.internal.widget.ActionBarOverlayLayout>
复制代码
```

通过includy引入的ContentView abc_screen_content_include.xml

```
<merge xmlns:android="http://schemas.android.com/apk/res/android">

    <android.support.v7.internal.widget.ContentFrameLayout
            android:id="@id/action_bar_activity_content"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:foregroundGravity="fill_horizontal|top"
            android:foreground="?android:attr/windowContentOverlay" />

</merge>
复制代码
```

layout布局很普通，没有什么特别之处，我看到这时候，猜想：当我们设置了fitSystemwindow(false)，是不是在这个`ActionBarOverlayLayout`的`onLyout()`过程会对相应的布局做调整。然后穷就去他的`onLayout()`里看：

```
@Override
protected void onLayout(boolean changed, int left, int top, int right, int bottom) {
    final int count = getChildCount();

    final int parentLeft = getPaddingLeft();
    final int parentRight = right - left - getPaddingRight();

    final int parentTop = getPaddingTop();
    final int parentBottom = bottom - top - getPaddingBottom();

    for (int i = 0; i < count; i++) {
        final View child = getChildAt(i);
        if (child.getVisibility() != GONE) {
            final LayoutParams lp = (LayoutParams) child.getLayoutParams();

            final int width = child.getMeasuredWidth();
            final int height = child.getMeasuredHeight();

            int childLeft = parentLeft + lp.leftMargin;
            int childTop = parentTop + lp.topMargin;

            child.layout(childLeft, childTop, childLeft + width, childTop + height);
        }
    }
}
复制代码
```

然而毛都没有。。。懵逼了,layout的参数都是来自布局文件里的，后来我跟着`setFitSystemWindow()`看到一个方法，就是这个`fitSystemWindows(Rect insets)`它的注释说明里有`The content insets tell you the space that the status bar`,应该是调用这个方法进行设置的，但是怎么调用的，目前我还没有找到，希望懂得同学指点迷津，万分感谢！

```
 /**
     * Called by the view hierarchy when the content insets for a window have
     * changed, to allow it to adjust its content to fit within those windows.
     * The content insets tell you the space that the status bar, input method,
     * and other system windows infringe on the application's window.
     ...
    protected boolean fitSystemWindows(Rect insets) {
        if ((mPrivateFlags3 & PFLAG3_APPLYING_INSETS) == 0) {
            if (insets == null) {
                // Null insets by definition have already been consumed.
                // This call cannot apply insets since there are none to apply,
                // so return false.
                return false;
            }
            // If we're not in the process of dispatching the newer apply insets call,
            // that means we're not in the compatibility path. Dispatch into the newer
            // apply insets path and take things from there.
            try {
                mPrivateFlags3 |= PFLAG3_FITTING_SYSTEM_WINDOWS;
                return dispatchApplyWindowInsets(new WindowInsets(insets)).isConsumed();
            } finally {
                mPrivateFlags3 &= ~PFLAG3_FITTING_SYSTEM_WINDOWS;
            }
        } else {
            // We're being called from the newer apply insets path.
            // Perform the standard fallback behavior.
            return fitSystemWindowsInt(insets);
        }
    }
复制代码
```

# 引用：
[Android 状态栏关于开发的几件事](https://juejin.im/post/5a52023b6fb9a01c9c1ed937)

