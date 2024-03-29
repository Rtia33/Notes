

# 【Android 滑动冲突】

### ***本篇文章已授权微信公众号 guolin_blog （郭霖）独家发布***

这篇博客主要讲解一下几个问题

*   粗略地介绍一下View的事件分发机制
*   解决事件滑动冲突的思路及方法
*   ScrollView 里面嵌套ViewPager导致的滑动冲突
*   ViewPager里面嵌套ViewPager 导致的滑动冲突
*   轮播图的几种实现方式

**[文章首发地址CSDN：](http://blog.csdn.net/gdutxiaoxu/article/details/52939127)[http://blog.csdn.net/gdutxiaoxu/article/details/52939127](http://blog.csdn.net/gdutxiaoxu/article/details/52939127)**

## 先看一下效果图

### ScrollView里面嵌套ViewPager

![image](http://upload-images.jianshu.io/upload_images/9028834-c3064bf25ffefc27..gif?imageMogr2/auto-orient/strip)

### ViewPager里面嵌套ViewPager

![image](http://upload-images.jianshu.io/upload_images/9028834-dc2cae89bb294886..gif?imageMogr2/auto-orient/strip)

* * *

## View的 事件分发机制

这篇博客不打算详细讲解View的事件分发机制，因为网上已经出现了一系列的好 文章，我自己的水平也有限，目前肯定写得不咋的。

先啰嗦一下，View 的事件分发机制主要涉及到一下三个 方法

*   dispatchTouchEvent ，这个方法主要是用来分发事件的
*   onInterceptTouchEvent，这个方法主要是用来拦截事件的（需要注意的是ViewGroup才有这个方法，View没有onInterceptTouchEvent这个方法
*   onTouchEvent 这个方法主要是用来处理事件的
*   requestDisallowInterceptTouchEvent(true)，这个方法能够影响父View是否拦截事件，true 表示父 View 不拦截事件，false 表示父 View 拦截事件

### 下面引用[图解 Android 事件分发机制](http://www.jianshu.com/p/e99b5e8bd67b)这一篇博客的内容

![image](http://upload-images.jianshu.io/upload_images/9028834-1f20257ace0a1e37?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> *   仔细看的话，图分为3层，从上往下依次是Activity、ViewGroup、View
> *   事件从左上角那个白色箭头开始，由Activity的dispatchTouchEvent做分发
> *   箭头的上面字代表方法返回值，（return true、return false、return super.xxxxx(),super 的意思是调用父类实现。
> *   dispatchTouchEvent和 onTouchEvent的框里有个【true—->消费】的字，表示的意思是如果方法返回true，那么代表事件就此消费，不会继续往别的地方传了，事件终止。
> *   目前所有的图的事件是针对ACTION_DOWN的，对于ACTION_MOVE和ACTION_UP我们最后做分析。
> *   之前图中的Activity 的dispatchTouchEvent 有误（图已修复），只有return super.dispatchTouchEvent(ev) 才是往下走，返回true 或者 false 事件就被消费了（终止传递）。

### 总结

当TouchEvent发生时，首先Activity将TouchEvent传递给最顶层的View，TouchEvent最先到达最顶层 view 的 dispatchTouchEvent ，然后由 dispatchTouchEvent 方法进行分发，

*   如果dispatchTouchEvent返回true 消费事件，事件终结。
*   如果dispatchTouchEvent返回 false ，则回传给父View的onTouchEvent事件处理；

    > onTouchEvent事件返回true，事件终结，返回false，交给父View的OnTouchEvent方法处理

*   如果dispatchTouchEvent返回super的话，默认会调用自己的onInterceptTouchEvent方法

    > 默认的情况下interceptTouchEvent回调用super方法，super方法默认返回false，所以会交给子View的onDispatchTouchEvent方法处理
    >
    > 如果 interceptTouchEvent 返回 true ，也就是拦截掉了，则交给它的 onTouchEvent 来处理，
    >
    > 如果 interceptTouchEvent 返回 false ，那么就传递给子 view ，由子 view 的 dispatchTouchEvent 再来开始这个事件的分发。

关于更多详细分析，请查看原博客[图解 Android 事件分发机制](http://www.jianshu.com/p/e99b5e8bd67b)，真心推荐，写得很好。

* * *

## 解决事件滑动冲突的思路及方法

### 常见的三种情况

第一种情况，滑动方向不同

![image](http://upload-images.jianshu.io/upload_images/9028834-0977e1d17ed32a0b..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第二种情况，滑动方向相同

![image](http://upload-images.jianshu.io/upload_images/9028834-6bca8f14f47c1009..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第三种情况，上述两种情况的嵌套

![image](http://upload-images.jianshu.io/upload_images/9028834-06bb3d43c6d7b179..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 解决思路

看了上面三种情况，我们知道他们的共同特点是父View 和子View都想争着响应我们的触摸事件，但遗憾的是我们的触摸事件 同一时刻只能被某一个View或者ViewGroup拦截消费，所以就产生了滑动冲突？那既然同一时刻只能由某一个View或者ViewGroup消费拦截，那我们就只需要 决定在某个时刻由这个 View 或者 ViewGroup 拦截事件，另外的 某个时刻由 另外一个 View 或者 ViewGroup 拦截事件，不就OK了吗？综上，正如 在 ***《Android开发艺术》*** 一书提出的，总共 有两种解决方案

以下解决思路来自于 ***《Android开发艺术》*** 书籍

**下面的两种方法针对第一种情况（滑动方向不同），父View是上下滑动，子View是左右滑动的情况。**

### 外部解决法

从父View着手，重写onInterceptTouchEvent方法，在父View需要拦截的时候拦截，不要的时候返回false，为代码大概 如下

```
@Override
public boolean onInterceptTouchEvent(MotionEvent ev) {
    final float x = ev.getX();
    final float y = ev.getY();

    final int action = ev.getAction();
    switch (action) {
        case MotionEvent.ACTION_DOWN:
            mDownPosX = x;
            mDownPosY = y;

            break;
        case MotionEvent.ACTION_MOVE:
            final float deltaX = Math.abs(x - mDownPosX);
            final float deltaY = Math.abs(y - mDownPosY);
            // 这里是够拦截的判断依据是左右滑动，读者可根据自己的逻辑进行是否拦截
            if (deltaX > deltaY) {
                return false;
            }
    }

    return super.onInterceptTouchEvent(ev);
}

```

### 内部解决法

从子View着手，父View先不要拦截任何事件，所有的事件传递给 子View，如果子View需要此事件就消费掉，不需要此事件的话就交给 父View处理。

实现思路 如下，重写子 View的dispatchTouchEvent方法，在Action_down 动作中通过方法 requestDisallowInterceptTouchEvent（true） 先请求 父 View不要拦截事件，这样保证子 View 能够接受到 Action_move 事件，再在 Action_move 动作中根据自己的逻辑是否要拦截事件，不需要拦截事件的话再交给 父 View 处理。

```
@Override
public boolean dispatchTouchEvent(MotionEvent ev) {
    int x = (int) ev.getRawX();
    int y = (int) ev.getRawY();
    int dealtX = 0;
    int dealtY = 0;

    switch (ev.getAction()) {
        case MotionEvent.ACTION_DOWN:
            dealtX = 0;
            dealtY = 0;
            // 保证子View能够接收到Action_move事件
            getParent().requestDisallowInterceptTouchEvent(true);
            break;
        case MotionEvent.ACTION_MOVE:
            dealtX += Math.abs(x - lastX);
            dealtY += Math.abs(y - lastY);
            Log.i(TAG, "dealtX:=" + dealtX);
            Log.i(TAG, "dealtY:=" + dealtY);
            // 这里是够拦截的判断依据是左右滑动，读者可根据自己的逻辑进行是否拦截
            if (dealtX >= dealtY) {
                getParent().requestDisallowInterceptTouchEvent(true);
            } else {
                getParent().requestDisallowInterceptTouchEvent(false);
            }
            lastX = x;
            lastY = y;
            break;
        case MotionEvent.ACTION_CANCEL:
            break;
        case MotionEvent.ACTION_UP:
            break;

    }
    return super.dispatchTouchEvent(ev);
}

```

* * *

## ScrollView 里面嵌套ViewPager导致的滑动冲突

### 外部解决法

如上面所述，从 父View ScrollView着手，重写 OnInterceptTouchEvent方法，在上下滑动的时候拦截事件，在左右滑动的时候不拦截事件，返回 false，这样确保子View 的dispatchTouchEvent方法会被调用，代码 如下

```
/**
 * @ explain:这个ScrlloView不拦截水平滑动事件，
 * 是用来解决 ScrollView里面嵌套ViewPager使用的
 * @ author：xujun on 2016/10/25 15:28
 * @ email：gdutxiaoxu@163.com
 */
public class VerticalScrollView extends ScrollView {

    public VerticalScrollView(Context context) {
        super(context);
    }

    public VerticalScrollView(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    public VerticalScrollView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    @TargetApi(21)
    public VerticalScrollView(Context context, AttributeSet attrs, int defStyleAttr, int
            defStyleRes) {
        super(context, attrs, defStyleAttr, defStyleRes);
    }

    private float mDownPosX = 0;
    private float mDownPosY = 0;

    @Override
    public boolean onInterceptTouchEvent(MotionEvent ev) {
        final float x = ev.getX();
        final float y = ev.getY();

        final int action = ev.getAction();
        switch (action) {
            case MotionEvent.ACTION_DOWN:
                mDownPosX = x;
                mDownPosY = y;

                break;
            case MotionEvent.ACTION_MOVE:
                final float deltaX = Math.abs(x - mDownPosX);
                final float deltaY = Math.abs(y - mDownPosY);
                // 这里是否拦截的判断依据是左右滑动，读者可根据自己的逻辑进行是否拦截

                if (deltaX > deltaY) {// 左右滑动不拦截
                    return false;
                }
        }

        return super.onInterceptTouchEvent(ev);
    }
}
```

### 内部解决法

如上面上述，通过requestDisallowInterceptTouchEvent(true)方法来影响父View是否拦截事件，我们通过重写ViewPager的 dispatchTouchEvent（）方法，在左右滑动的时候请求父View ScrollView不要拦截事件，其他的时候由子View 拦截事件

```
/**
 * @ explain:这个 ViewPager是用来解决ScrollView里面嵌套ViewPager的 内部解决法的
 * @ author：xujun on 2016/10/25 16:38
 * @ email：gdutxiaoxu@163.com
 */
public class MyViewPager extends ViewPager {

    private static final String TAG = "xujun";

    int lastX = -1;
    int lastY = -1;

    public MyViewPager(Context context) {
        super(context);
    }

    public MyViewPager(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    @Override
    public boolean dispatchTouchEvent(MotionEvent ev) {
        int x = (int) ev.getRawX();
        int y = (int) ev.getRawY();
        int dealtX = 0;
        int dealtY = 0;

        switch (ev.getAction()) {
            case MotionEvent.ACTION_DOWN:
                dealtX = 0;
                dealtY = 0;
                // 保证子View能够接收到Action_move事件
                getParent().requestDisallowInterceptTouchEvent(true);
                break;
            case MotionEvent.ACTION_MOVE:
                dealtX += Math.abs(x - lastX);
                dealtY += Math.abs(y - lastY);
                Log.i(TAG, "dealtX:=" + dealtX);
                Log.i(TAG, "dealtY:=" + dealtY);
                // 这里是否拦截的判断依据是左右滑动，读者可根据自己的逻辑进行是否拦截
                if (dealtX >= dealtY) { // 左右滑动请求父 View 不要拦截
                    getParent().requestDisallowInterceptTouchEvent(true);
                } else {
                    getParent().requestDisallowInterceptTouchEvent(false);
                }
                lastX = x;
                lastY = y;
                break;
            case MotionEvent.ACTION_CANCEL:
                break;
            case MotionEvent.ACTION_UP:
                break;

        }
        return super.dispatchTouchEvent(ev);
    }
}

```

## **注意事项（坑）**

当我们 ScrollView 的最上层的 Layout 里面多多个孩子的时候，当下面一个孩子是 RecyclerView 或者ListView 的时候，往往会自动滑动到 ListView 或者 RecyclerView 的第一个 item，导致进入界面的时候会导致 RecyclerView 上面的 View 被滑动到界面之外，看不见，这时候的用户体验是比较差的

即结构如下面的时候

![image](http://upload-images.jianshu.io/upload_images/9028834-3d1452adf1c02d56..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 在Activity中的相关解决方法

于是我查找了相关的资料，在Activity中完美解决，主要要一下两种方法

第一种方法，重写Activity的onWindowFocusChanged（）方法，在里面调用mNoHorizontalScrollView.scrollTo(0,0);方法，滑动到顶部，因为onWindowFocusChanged是在所有View绘制完毕的时候才会回调的，不熟悉的话建议先回去看一下Activity的生命周期的相关介绍

```

private void scroll() {
    mNoHorizontalScrollView.scrollTo(0,0);
}

@Override
public void onWindowFocusChanged(boolean hasFocus) {
    super.onWindowFocusChanged(hasFocus);
    if(hasFocus  && first){
        first=false;
        scroll();
    }
}

```

第二种解决方法，调用RecyclerView上面的View的一下方法，让其获取焦点

```
view.setFocusable(true);  
view.setFocusableInTouchMode(true);  
view.requestFocus();
```

这段代码在初始化的时候就让该界面的顶部的某一个控件获得焦点，滚动条自然就显示到顶部了。

### 在Fragment中的相关解决方法

同样是调用第二种方法，调用RecyclerView上面的View的一下方法，让其获取焦点

```
view.setFocusable(true);  
view.setFocusableInTouchMode(true);  
view.requestFocus();
```

这段代码在初始化的时候就让该界面的顶部的某一个控件获得焦点，滚动条自然就显示到顶部了。但是该方法存在缺点，就是当我们上面的view如果滑动到一半的时候，切换到下一个Fragment，在切换回来的时候，RecyclerView的第一个item会自动滑动到顶部。目前我还没有找到相对比较好的解决这个问题的方法，大家知道相关解决方法的话也欢迎联系我，可以加我 微信或者在留言区评论，谢谢。

### 网友提供的解决方案

关于 ViewPagerActivity 在Fragment页面切换的时候，RecyclerView抢占焦点的问题已经解决，特别 感谢Jianqiu，他的博客地址：[http://niorgai.github.io/](http://niorgai.github.io/)

在 ViewPagerActivity 里面的 Fragment的 代码中加入以下代码，可以阻止 RecyclerView 的子 View 获得焦点，从而阻止 RecyclerView 抢占位置。

```
// 是为了确保mNoHorizontalScrollView他的子孙不能获得焦点
mNoHorizontalScrollView.setDescendantFocusability(ViewGroup.FOCUS_BLOCK_DESCENDANTS);
```

详细代码见项目中的ListFragement

#### **个人疑点**

借鉴于解决Activity的方法，目前我还没有找到一个方法是在Fragemnt界面完全绘制完毕以后回调的方法，如果大家知道怎样处理的 话，欢迎大家提出来

* * *

## ViewPager里面嵌套ViewPager导致的滑动冲突

### 内部解决法

从子View ViewPager着手，重写 子View的 dispatchTouchEvent方法，在子 View需要拦截的时候进行拦截，否则交给父View处理，代码如下

```
public class ChildViewPager extends ViewPager {

    private static final String TAG = "xujun";
    public ChildViewPager(Context context) {
        super(context);
    }

    public ChildViewPager(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    @Override
    public boolean dispatchTouchEvent(MotionEvent ev) {
        int curPosition;

        switch (ev.getAction()) {
            case MotionEvent.ACTION_DOWN:
                getParent().requestDisallowInterceptTouchEvent(true);
                break;
            case MotionEvent.ACTION_MOVE:
                curPosition = this.getCurrentItem();
                int count = this.getAdapter().getCount();
                Log.i(TAG, "curPosition:=" +curPosition);
                // 当当前页面在最后一页和第0页的时候，由父亲拦截触摸事件
                if (curPosition == count - 1|| curPosition==0) {
                    getParent().requestDisallowInterceptTouchEvent(false);
                } else {//其他情况，由孩子拦截触摸事件
                    getParent().requestDisallowInterceptTouchEvent(true);
                }

        }
        return super.dispatchTouchEvent(ev);
    }
}
```

### 外部解决法

这个如果要采用外部解决法来解决的话想，相对很麻烦，我提一下自己的个人思路，我们可以先测量子View在哪个区域，然后我们在根据我们按下的点是否在区域以内，如果是的话，在根据子View时候需要拦截进行处理

* * *

## 讨论

![image](http://upload-images.jianshu.io/upload_images/9028834-854febaee6f05acc..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对于这种效果，上面是轮播图的，下面是RecyclerView或者ListView的，一般有一下几种实现方式 
- 使用我们上述提高的ScrollView里面嵌套ViewPager和RecyclerView，这种实现方式需要自己解决View滑动事件的冲突，同时还有我在上述提高的在Fragment中存在的问题 
- 使用listView的addHeaderView来实现，或者是通过多种不同的item来实现 
- 使用RecyclerView添加headerView来实现，或者复用多种不同的item来实现。关于RecyclerView如何添加headerView可以参考鸿洋大神的这一篇博客 [Android 优雅的为RecyclerView添加HeaderView和FooterView](http://blog.csdn.net/lmj623565791/article/details/51854533) 
- 使用SupportLibrary中的CoordinatorLayout等控件

其布局文件如下，Activity代码见项目中的SixActivity

```
<?xml version="1.0" encoding="utf-8"?>

<android.support.design.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@android:color/background_light"
    android:fitsSystemWindows="true"
>

    <android.support.design.widget.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="300dp"
        android:fitsSystemWindows="true"
        android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"

    >

        <android.support.design.widget.CollapsingToolbarLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:layout_scrollFlags="scroll|snap">

            <android.support.v4.view.ViewPager
                android:id="@+id/viewPager"
                android:layout_width="match_parent"
                android:layout_height="match_parent"

            >

            </android.support.v4.view.ViewPager>

            <TextView
                android:id="@+id/tv_page"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_gravity="bottom"
                android:gravity="right"
                android:text="1/10"
                android:textColor="#000"/>

        </android.support.design.widget.CollapsingToolbarLayout>

    </android.support.design.widget.AppBarLayout>

    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior">

    </android.support.v7.widget.RecyclerView>

</android.support.design.widget.CoordinatorLayout>

```

关于CoordinatorLayout的更多用法，可以参考我的这一篇博客[使用CoordinatorLayout打造各种炫酷的效果](http://blog.csdn.net/gdutxiaoxu/article/details/52858598)

* * *

## 总结

*   当我们滑动方向不同的时候，采用外部解决法和内部解决法，复杂度差不多。
*   当我们滑动的方向相同的话，建议采用内部解决法来解决，因为采用外部解决法复杂度比较高。而且有时候我们是采用别人的开源控件，这时候去修改别人的源码可能会发生一些意想不到的bug。

## 题外话

*   在这篇博客的最后提高的实现轮播图+list列表的几种实现形式，刚开始是不想写的，后面因为ScrollView里面嵌套ViewPager和RecyclerView在fragment中RecyclerView抢占焦点，在某些情况下用户体验不好，才写出来的，跟这篇博客要讲解的View滑动事件冲突没有多大关系，只是给读者提供多种思路而已
*   至于CoordinatorLayout，是google IO 2015中提出来的，功能很强大，可以说是专门为了解决嵌套导滑动而产生的，极大地方便了开发者，对于初学者，可以暂时不必掌握它，先把其他的基础学好就好
*   同时卖一下广告，欢迎大家到我的github上面star或者fork，谢谢

参考文章：[图解 Android 事件分发机制](http://www.jianshu.com/p/e99b5e8bd67b)

**[文章首发地址CSDN：](http://blog.csdn.net/gdutxiaoxu/article/details/52939127)[http://blog.csdn.net/gdutxiaoxu/article/details/52939127](http://blog.csdn.net/gdutxiaoxu/article/details/52939127)**

**[源码下载地址：](https://github.com/gdutxiaoxu/TouchDemo.git)[https://github.com/gdutxiaoxu/TouchDemo.git](https://github.com/gdutxiaoxu/TouchDemo.git)**

最后的最后，卖一下广告，欢迎大家关注我的微信公众号，扫一扫下方二维码或搜索微信号 stormjun，即可关注。 目前专注于 Android 开发，主要分享 Android开发相关知识和一些相关的优秀文章，包括个人总结，职场经验等。






