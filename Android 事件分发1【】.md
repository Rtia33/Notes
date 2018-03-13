<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [前言](#%E5%89%8D%E8%A8%80)
- [目录](#%E7%9B%AE%E5%BD%95)
- [1\. 基础认知](#1%5C-%E5%9F%BA%E7%A1%80%E8%AE%A4%E7%9F%A5)
    - [1.1 事件分发的对象是谁？](#11-%E4%BA%8B%E4%BB%B6%E5%88%86%E5%8F%91%E7%9A%84%E5%AF%B9%E8%B1%A1%E6%98%AF%E8%B0%81)
    - [1.2 事件分发的本质](#12-%E4%BA%8B%E4%BB%B6%E5%88%86%E5%8F%91%E7%9A%84%E6%9C%AC%E8%B4%A8)
    - [1.3 事件在哪些对象之间进行传递？](#13-%E4%BA%8B%E4%BB%B6%E5%9C%A8%E5%93%AA%E4%BA%9B%E5%AF%B9%E8%B1%A1%E4%B9%8B%E9%97%B4%E8%BF%9B%E8%A1%8C%E4%BC%A0%E9%80%92)
    - [1.4 事件分发的顺序](#14-%E4%BA%8B%E4%BB%B6%E5%88%86%E5%8F%91%E7%9A%84%E9%A1%BA%E5%BA%8F)
    - [1.5 事件分发过程由哪些方法协作完成？](#15-%E4%BA%8B%E4%BB%B6%E5%88%86%E5%8F%91%E8%BF%87%E7%A8%8B%E7%94%B1%E5%93%AA%E4%BA%9B%E6%96%B9%E6%B3%95%E5%8D%8F%E4%BD%9C%E5%AE%8C%E6%88%90)
    - [1.6 总结](#16-%E6%80%BB%E7%BB%93)
- [2\. 事件分发机制 源码分析](#2%5C-%E4%BA%8B%E4%BB%B6%E5%88%86%E5%8F%91%E6%9C%BA%E5%88%B6-%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90)
    - [2.1 Activity的事件分发机制](#21-activity%E7%9A%84%E4%BA%8B%E4%BB%B6%E5%88%86%E5%8F%91%E6%9C%BA%E5%88%B6)
    - [2.1.1 源码分析](#211-%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90)
    - [2.1.2 总结](#212-%E6%80%BB%E7%BB%93)
- [2.2 ViewGroup事件的分发机制](#22-viewgroup%E4%BA%8B%E4%BB%B6%E7%9A%84%E5%88%86%E5%8F%91%E6%9C%BA%E5%88%B6)
    - [2.2.1 源码分析](#221-%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90)
    - [2.2.2 总结](#222-%E6%80%BB%E7%BB%93)
    - [2.2.3 Demo讲解](#223-demo%E8%AE%B2%E8%A7%A3)
- [2.3 View事件的分发机制](#23-view%E4%BA%8B%E4%BB%B6%E7%9A%84%E5%88%86%E5%8F%91%E6%9C%BA%E5%88%B6)
    - [2.3.1 源码分析](#231-%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90)
    - [2.3.2 总结](#232-%E6%80%BB%E7%BB%93)
    - [2.3.3 Demo讲解](#233-demo%E8%AE%B2%E8%A7%A3)
- [2.4 总结](#24-%E6%80%BB%E7%BB%93)
    - [若您已经看到此处，那么恭喜你，你已经能非常熟悉掌握Android的事件分发机制了](#%E8%8B%A5%E6%82%A8%E5%B7%B2%E7%BB%8F%E7%9C%8B%E5%88%B0%E6%AD%A4%E5%A4%84%E9%82%A3%E4%B9%88%E6%81%AD%E5%96%9C%E4%BD%A0%E4%BD%A0%E5%B7%B2%E7%BB%8F%E8%83%BD%E9%9D%9E%E5%B8%B8%E7%86%9F%E6%82%89%E6%8E%8C%E6%8F%A1android%E7%9A%84%E4%BA%8B%E4%BB%B6%E5%88%86%E5%8F%91%E6%9C%BA%E5%88%B6%E4%BA%86)
- [3\. 工作流程 总结](#3%5C-%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B-%E6%80%BB%E7%BB%93)
- [4\. 核心方法总结](#4%5C-%E6%A0%B8%E5%BF%83%E6%96%B9%E6%B3%95%E6%80%BB%E7%BB%93)
    - [4.1 dispatchTouchEvent()](#41-dispatchtouchevent)
    - [4.2 onInterceptTouchEvent()](#42-onintercepttouchevent)
    - [4.3 onTouchEvent()](#43-ontouchevent)
    - [4.4 三者关系](#44-%E4%B8%89%E8%80%85%E5%85%B3%E7%B3%BB)
- [5\. 常见的事件分发场景](#5%5C-%E5%B8%B8%E8%A7%81%E7%9A%84%E4%BA%8B%E4%BB%B6%E5%88%86%E5%8F%91%E5%9C%BA%E6%99%AF)
    - [5.1 背景描述](#51-%E8%83%8C%E6%99%AF%E6%8F%8F%E8%BF%B0)
    - [5.2 一般的事件传递情况](#52-%E4%B8%80%E8%88%AC%E7%9A%84%E4%BA%8B%E4%BB%B6%E4%BC%A0%E9%80%92%E6%83%85%E5%86%B5)
    - [场景1：默认](#%E5%9C%BA%E6%99%AF1%E9%BB%98%E8%AE%A4)
    - [场景2：处理事件](#%E5%9C%BA%E6%99%AF2%E5%A4%84%E7%90%86%E4%BA%8B%E4%BB%B6)
    - [场景3：拦截DOWN事件](#%E5%9C%BA%E6%99%AF3%E6%8B%A6%E6%88%AAdown%E4%BA%8B%E4%BB%B6)
    - [场景4：拦截DOWN的后续事件](#%E5%9C%BA%E6%99%AF4%E6%8B%A6%E6%88%AAdown%E7%9A%84%E5%90%8E%E7%BB%AD%E4%BA%8B%E4%BB%B6)
- [6\. 额外知识](#6%5C-%E9%A2%9D%E5%A4%96%E7%9F%A5%E8%AF%86)
    - [6.1 Touch事件的后续事件（MOVE、UP）层级传递](#61-touch%E4%BA%8B%E4%BB%B6%E7%9A%84%E5%90%8E%E7%BB%AD%E4%BA%8B%E4%BB%B6moveup%E5%B1%82%E7%BA%A7%E4%BC%A0%E9%80%92)
    - [6.2 onTouch()和onTouchEvent()的区别](#62-ontouch%E5%92%8Contouchevent%E7%9A%84%E5%8C%BA%E5%88%AB)
- [7\. 总结](#7%5C-%E6%80%BB%E7%BB%93)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# 前言

*   `Android`事件分发机制是`Android`开发者必须了解的基础
*   网上有大量关于`Android`事件分发机制的文章，但存在一些问题：**内容不全、思路不清晰、无源码分析、简单问题复杂化等等**
*   今天，我将全面总结`Android`的事件分发机制，我能保证这是**市面上的最全面、最清晰、最易懂的** 

    > 1.  本文秉着“结论先行、详细分析在后”的原则，即先让大家感性认识，再通过理性分析从而理解问题； 
    >
    > 2.  所以，请各位读者先记住结论，再往下继续看分析；
    > 3.  文章较长，阅读需要较长时间，建议收藏等充足时间再进行阅读

* * *

# 目录

![示意图](http://upload-images.jianshu.io/upload_images/9028834-8bdf1a04782de5b7?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* * *

# 1\. 基础认知

### 1.1 事件分发的对象是谁？

**答：点击事件（`Touch`事件）**

*   定义 
    当用户触摸屏幕时（`View` 或 `ViewGroup`派生的控件），将产生点击事件（`Touch`事件）

    > `Touch`事件的相关细节（发生触摸的位置、时间等）被封装成`MotionEvent`对象

*   事件类型（4种）

| 事件类型                      |      具体动作       |
| ------------------------- | :-------------: |
| MotionEvent.ACTION_DOWN   | 按下View（所有事件的开始） |
| MotionEvent.ACTION_UP     | 抬起View（与DOWN对应） |
| MotionEvent.ACTION_MOVE   |     滑动View      |
| MotionEvent.ACTION_CANCEL |   结束事件（非人为原因）   |

*   特别说明：事件列

从手指接触屏幕 至 手指离开屏幕，这个过程产生的一系列事件

> 注：一般情况下，事件列都是以`DOWN`事件开始、`UP`事件结束，中间有无数的MOVE事件，如下图： 
> ![事件列](http://upload-images.jianshu.io/upload_images/9028834-2a50529517b76fbb?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

即当一个点击事件（`MotionEvent` ）产生后，系统需把这个事件传递给一个具体的 `View` 去处理。

### 1.2 事件分发的本质

**答：将点击事件（MotionEvent）传递到某个具体的`View` & 处理的整个过程**

> 即 事件传递的过程 = 分发过程。

### 1.3 事件在哪些对象之间进行传递？

**答：Activity、ViewGroup、View**

*   `Android`的`UI`界面由`Activity`、`ViewGroup`、`View` 及其派生类组成 
    ![UI界面](http://upload-images.jianshu.io/upload_images/9028834-0f6adb4783287fcc?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![示意图](http://upload-images.jianshu.io/upload_images/9028834-302b53c174da085a?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 1.4 事件分发的顺序

即 事件传递的顺序：`Activity` -> `ViewGroup` -> `View`

> 即：1个点击事件发生后，事件先传到`Activity`、再传到`ViewGroup`、最终再传到 `View`

![示意图](http://upload-images.jianshu.io/upload_images/9028834-112311d20f470a15?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 1.5 事件分发过程由哪些方法协作完成？

**答：dispatchTouchEvent() 、onInterceptTouchEvent()和onTouchEvent()**

![示意图](http://upload-images.jianshu.io/upload_images/9028834-9d67dc5032c7fc6e?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 下文会对这3个方法进行详细介绍

### 1.6 总结

![示意图](http://upload-images.jianshu.io/upload_images/9028834-6ad55282d3d610b1?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   至此，相信大家已经对 `Android`的事件分发有了感性的认知
*   下面，我将详细介绍`Android`事件分发机制

*   * *

# 2\. 事件分发机制 源码分析

*   请谨记：`Android`事件分发流程 = **Activity -> ViewGroup -> View** 

    > 即：1个点击事件发生后，事件先传到`Activity`、再传到`ViewGroup`、最终再传到 `View`

![示意图](http://upload-images.jianshu.io/upload_images/9028834-6d042116eec1e4f4?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   从上可知，要想充分理解Android分发机制，本质上是要理解： 

    1.  `Activity`对点击事件的分发机制
    2.  `ViewGroup`对点击事件的分发机制
    3.  `View`对点击事件的分发机制
*   下面，我将通过源码，全面解析 **事件分发机制** 

    > 即按顺序讲解：`Activity`事件分发机制、`ViewGroup`事件分发机制、`View`事件分发机制

### 2.1 Activity的事件分发机制

当一个点击事件发生时，事件最先传到`Activity`的`dispatchTouchEvent()`进行事件分发

### 2.1.1 源码分析

```
/**
  * 源码分析：Activity.dispatchTouchEvent（）
  */ 
    public boolean dispatchTouchEvent(MotionEvent ev) {

            // 一般事件列开始都是DOWN事件 = 按下事件，故此处基本是true
            if (ev.getAction() == MotionEvent.ACTION_DOWN) {

                onUserInteraction();
                // ->>分析1

            }

            // ->>分析2
            if (getWindow().superDispatchTouchEvent(ev)) {

                return true;
                // 若getWindow().superDispatchTouchEvent(ev)的返回true
                // 则Activity.dispatchTouchEvent（）就返回true，则方法结束。即 ：该点击事件停止往下传递 & 事件传递过程结束
                // 否则：继续往下调用Activity.onTouchEvent

            }
            // ->>分析4
            return onTouchEvent(ev);
        }

/**
  * 分析1：onUserInteraction()
  * 作用：实现屏保功能
  * 注：
  *    a. 该方法为空方法
  *    b. 当此activity在栈顶时，触屏点击按home，back，menu键等都会触发此方法
  */
      public void onUserInteraction() { 

      }
      // 回到最初的调用原处

/**
  * 分析2：getWindow().superDispatchTouchEvent(ev)
  * 说明：
  *     a. getWindow() = 获取Window类的对象
  *     b. Window类是抽象类，其唯一实现类 = PhoneWindow类；即此处的Window类对象 = PhoneWindow类对象
  *     c. Window类的superDispatchTouchEvent() = 1个抽象方法，由子类PhoneWindow类实现
  */
    @Override
    public boolean superDispatchTouchEvent(MotionEvent event) {

        return mDecor.superDispatchTouchEvent(event);
        // mDecor = 顶层View（DecorView）的实例对象
        // ->> 分析3
    }

/**
  * 分析3：mDecor.superDispatchTouchEvent(event)
  * 定义：属于顶层View（DecorView）
  * 说明：
  *     a. DecorView类是PhoneWindow类的一个内部类
  *     b. DecorView继承自FrameLayout，是所有界面的父类
  *     c. FrameLayout是ViewGroup的子类，故DecorView的间接父类 = ViewGroup
  */
    public boolean superDispatchTouchEvent(MotionEvent event) {

        return super.dispatchTouchEvent(event);
        // 调用父类的方法 = ViewGroup的dispatchTouchEvent()
        // 即 将事件传递到ViewGroup去处理，详细请看ViewGroup的事件分发机制

    }
    // 回到最初的调用原处

/**
  * 分析4：Activity.onTouchEvent（）
  * 定义：属于顶层View（DecorView）
  * 说明：
  *     a. DecorView类是PhoneWindow类的一个内部类
  *     b. DecorView继承自FrameLayout，是所有界面的父类
  *     c. FrameLayout是ViewGroup的子类，故DecorView的间接父类 = ViewGroup
  */
  public boolean onTouchEvent(MotionEvent event) {

        // 当一个点击事件未被Activity下任何一个View接收 / 处理时
        // 应用场景：处理发生在Window边界外的触摸事件
        // ->> 分析5
        if (mWindow.shouldCloseOnTouch(this, event)) {
            finish();
            return true;
        }

        return false;
        // 即 只有在点击事件在Window边界外才会返回true，一般情况都返回false，分析完毕
    }

/**
  * 分析5：mWindow.shouldCloseOnTouch(this, event)
  */
    public boolean shouldCloseOnTouch(Context context, MotionEvent event) {
    // 主要是对于处理边界外点击事件的判断：是否是DOWN事件，event的坐标是否在边界内等
    if (mCloseOnTouchOutside && event.getAction() == MotionEvent.ACTION_DOWN
            && isOutOfBounds(context, event) && peekDecorView() != null) {
        return true;
    }
    return false;
    // 返回true：说明事件在边界外，即 消费事件
    // 返回false：未消费（默认）
}
// 回到分析4调用原处
```

### 2.1.2 总结

*   当一个点击事件发生时，从`Activity`的事件分发开始（`Activity.dispatchTouchEvent()`）

![示意图](http://upload-images.jianshu.io/upload_images/9028834-ae8c653114b7164a?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   方法总结

![示意图](http://upload-images.jianshu.io/upload_images/9028834-5c420ef674b0e3d3?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

那么，`ViewGroup`的`dispatchTouchEvent()`什么时候返回`true` / `false`？请继续往下看**ViewGroup事件的分发机制**

* * *

# 2.2 ViewGroup事件的分发机制

从上面`Activity`事件分发机制可知，`ViewGroup`事件分发机制从`dispatchTouchEvent()`开始

### 2.2.1 源码分析

> 1.  `Android 5.0`后，`ViewGroup.dispatchTouchEvent()`的源码发生了变化（更加复杂），但原理相同；
> 2.  本文为了让读者容易理解，故采用`Android 5.0`前的版本

```
/**
  * 源码分析：ViewGroup.dispatchTouchEvent（）
  */ 
    public boolean dispatchTouchEvent(MotionEvent ev) { 

    ... // 仅贴出关键代码

        // 重点分析1：ViewGroup每次事件分发时，都需调用onInterceptTouchEvent()询问是否拦截事件
            if (disallowIntercept || !onInterceptTouchEvent(ev)) {  

            // 判断值1：disallowIntercept = 是否禁用事件拦截的功能(默认是false)，可通过调用requestDisallowInterceptTouchEvent（）修改
            // 判断值2： !onInterceptTouchEvent(ev) = 对onInterceptTouchEvent()返回值取反
                    // a. 若在onInterceptTouchEvent()中返回false（即不拦截事件），就会让第二个值为true，从而进入到条件判断的内部
                    // b. 若在onInterceptTouchEvent()中返回true（即拦截事件），就会让第二个值为false，从而跳出了这个条件判断
                    // c. 关于onInterceptTouchEvent() ->>分析1

                ev.setAction(MotionEvent.ACTION_DOWN);  
                final int scrolledXInt = (int) scrolledXFloat;  
                final int scrolledYInt = (int) scrolledYFloat;  
                final View[] children = mChildren;  
                final int count = mChildrenCount;  

        // 重点分析2
            // 通过for循环，遍历了当前ViewGroup下的所有子View
            for (int i = count - 1; i >= 0; i--) {  
                final View child = children[i];  
                if ((child.mViewFlags & VISIBILITY_MASK) == VISIBLE  
                        || child.getAnimation() != null) {  
                    child.getHitRect(frame);  

                    // 判断当前遍历的View是不是正在点击的View，从而找到当前被点击的View
                    // 若是，则进入条件判断内部
                    if (frame.contains(scrolledXInt, scrolledYInt)) {  
                        final float xc = scrolledXFloat - child.mLeft;  
                        final float yc = scrolledYFloat - child.mTop;  
                        ev.setLocation(xc, yc);  
                        child.mPrivateFlags &= ~CANCEL_NEXT_UP_EVENT;  

                        // 条件判断的内部调用了该View的dispatchTouchEvent()
                        // 即 实现了点击事件从ViewGroup到子View的传递（具体请看下面的View事件分发机制）
                        if (child.dispatchTouchEvent(ev))  { 

                        mMotionTarget = child;  
                        return true; 
                        // 调用子View的dispatchTouchEvent后是有返回值的
                        // 若该控件可点击，那么点击时，dispatchTouchEvent的返回值必定是true，因此会导致条件判断成立
                        // 于是给ViewGroup的dispatchTouchEvent（）直接返回了true，即直接跳出
                        // 即把ViewGroup的点击事件拦截掉

                                }  
                            }  
                        }  
                    }  
                }  
            }  
            boolean isUpOrCancel = (action == MotionEvent.ACTION_UP) ||  
                    (action == MotionEvent.ACTION_CANCEL);  
            if (isUpOrCancel) {  
                mGroupFlags &= ~FLAG_DISALLOW_INTERCEPT;  
            }  
            final View target = mMotionTarget;  

        // 重点分析3
        // 若点击的是空白处（即无任何View接收事件） / 拦截事件（手动复写onInterceptTouchEvent（），从而让其返回true）
        if (target == null) {  
            ev.setLocation(xf, yf);  
            if ((mPrivateFlags & CANCEL_NEXT_UP_EVENT) != 0) {  
                ev.setAction(MotionEvent.ACTION_CANCEL);  
                mPrivateFlags &= ~CANCEL_NEXT_UP_EVENT;  
            }  

            return super.dispatchTouchEvent(ev);
            // 调用ViewGroup父类的dispatchTouchEvent()，即View.dispatchTouchEvent()
            // 因此会执行ViewGroup的onTouch() ->> onTouchEvent() ->> performClick（） ->> onClick()，即自己处理该事件，事件不会往下传递（具体请参考View事件的分发机制中的View.dispatchTouchEvent（））
            // 此处需与上面区别：子View的dispatchTouchEvent（）
        } 

        ... 

}
/**
  * 分析1：ViewGroup.onInterceptTouchEvent()
  * 作用：是否拦截事件
  * 说明：
  *     a. 返回true = 拦截，即事件停止往下传递（需手动设置，即复写onInterceptTouchEvent（），从而让其返回true）
  *     b. 返回false = 不拦截（默认）
  */
  public boolean onInterceptTouchEvent(MotionEvent ev) {  

    return false;

  } 
  // 回到调用原处
```

### 2.2.2 总结

*   结论：`Android`事件分发总是先传递到`ViewGroup`、再传递到`View`
*   过程：当点击了某个控件时

![示意图](http://upload-images.jianshu.io/upload_images/9028834-366699918077ec0c?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   核心方法总结

![示意图](http://upload-images.jianshu.io/upload_images/9028834-bb76795475abf3b1?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 2.2.3 Demo讲解

*   布局如下 
    ![布局层次](http://upload-images.jianshu.io/upload_images/9028834-c0e9ee35307b307e?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   测试代码

布局文件：*activity_main.xml*

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/my_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:focusableInTouchMode="true"
    android:orientation="vertical">

    <Button
        android:id="@+id/button1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="按钮1" />

    <Button
        android:id="@+id/button2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="按钮2" />

</LinearLayout>

```

核心代码：*MainActivity.java*

```
/**
  * ViewGroup布局（myLayout）中有2个子View = 2个按钮
  */
    public class MainActivity extends AppCompatActivity {

    Button button1,button2;
    ViewGroup myLayout;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        button1 = (Button)findViewById(R.id.button1);
        button2 = (Button)findViewById(R.id.button2);
        myLayout = (LinearLayout)findViewById(R.id.my_layout);

        // 1.为ViewGroup布局设置监听事件
        myLayout.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Log.d("TAG", "点击了ViewGroup");
            }
        });

        // 2\. 为按钮1设置监听事件
        button1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Log.d("TAG", "点击了button1");
            }
        });

        // 3\. 为按钮2设置监听事件
        button2.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Log.d("TAG", "点击了button2");
            }
        });

    }
}
```

*   结果测试 
    ![示意图](http://upload-images.jianshu.io/upload_images/9028834-e2ff8f2aca45fd5d?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从上面的测试结果发现：

*   点击`Button`时，执行`Button.onClick()`，但`ViewGroupLayout`注册的`onTouch（）`不会执行
*   只有点击空白区域时，才会执行`ViewGroupLayout`的`onTouch（）`
*   结论：`Button`的`onClick()`将事件消费掉了，因此事件不会再继续向下传递。

*   * *

# 2.3 View事件的分发机制

从上面`ViewGroup`事件分发机制知道，`View`事件分发机制从`dispatchTouchEvent()`开始

### 2.3.1 源码分析

```
/**
  * 源码分析：View.dispatchTouchEvent（）
  */
  public boolean dispatchTouchEvent(MotionEvent event) {  

        if (mOnTouchListener != null && (mViewFlags & ENABLED_MASK) == ENABLED &&  
                mOnTouchListener.onTouch(this, event)) {  
            return true;  
        } 
        return onTouchEvent(event);  
  }
  // 说明：只有以下3个条件都为真，dispatchTouchEvent()才返回true；否则执行onTouchEvent()
  //     1\. mOnTouchListener != null
  //     2\. (mViewFlags & ENABLED_MASK) == ENABLED
  //     3\. mOnTouchListener.onTouch(this, event)
  // 下面对这3个条件逐个分析

/**
  * 条件1：mOnTouchListener != null
  * 说明：mOnTouchListener变量在View.setOnTouchListener（）方法里赋值
  */
  public void setOnTouchListener(OnTouchListener l) { 

    mOnTouchListener = l;  
    // 即只要我们给控件注册了Touch事件，mOnTouchListener就一定被赋值（不为空）

} 

/**
  * 条件2：(mViewFlags & ENABLED_MASK) == ENABLED
  * 说明：
  *     a. 该条件是判断当前点击的控件是否enable
  *     b. 由于很多View默认enable，故该条件恒定为true
  */

/**
  * 条件3：mOnTouchListener.onTouch(this, event)
  * 说明：即 回调控件注册Touch事件时的onTouch（）；需手动复写设置，具体如下（以按钮Button为例）
  */
    button.setOnTouchListener(new OnTouchListener() {  
        @Override  
        public boolean onTouch(View v, MotionEvent event) {  

            return false;  
        }  
    });
    // 若在onTouch（）返回true，就会让上述三个条件全部成立，从而使得View.dispatchTouchEvent（）直接返回true，事件分发结束
    // 若在onTouch（）返回false，就会使得上述三个条件不全部成立，从而使得View.dispatchTouchEvent（）中跳出If，执行onTouchEvent(event)
```

接下来，我们继续看：**onTouchEvent(event)**的源码分析

> 1.  详情请看注释
> 2.  `Android 5.0`后 `View.onTouchEvent()`源码发生了变化（更加复杂），但原理相同；
> 3.  本文为了让读者更好理解，所以采用`Android 5.0`前的版本

```
/**
  * 源码分析：View.onTouchEvent（）
  */
  public boolean onTouchEvent(MotionEvent event) {  
    final int viewFlags = mViewFlags;  

    if ((viewFlags & ENABLED_MASK) == DISABLED) {  

        return (((viewFlags & CLICKABLE) == CLICKABLE ||  
                (viewFlags & LONG_CLICKABLE) == LONG_CLICKABLE));  
    }  
    if (mTouchDelegate != null) {  
        if (mTouchDelegate.onTouchEvent(event)) {  
            return true;  
        }  
    }  

    // 若该控件可点击，则进入switch判断中
    if (((viewFlags & CLICKABLE) == CLICKABLE ||  
            (viewFlags & LONG_CLICKABLE) == LONG_CLICKABLE)) {  

                switch (event.getAction()) { 

                    // a. 若当前的事件 = 抬起View（主要分析）
                    case MotionEvent.ACTION_UP:  
                        boolean prepressed = (mPrivateFlags & PREPRESSED) != 0;  

                            ...// 经过种种判断，此处省略

                            // 执行performClick() ->>分析1
                            performClick();  
                            break;  

                    // b. 若当前的事件 = 按下View
                    case MotionEvent.ACTION_DOWN:  
                        if (mPendingCheckForTap == null) {  
                            mPendingCheckForTap = new CheckForTap();  
                        }  
                        mPrivateFlags |= PREPRESSED;  
                        mHasPerformedLongPress = false;  
                        postDelayed(mPendingCheckForTap, ViewConfiguration.getTapTimeout());  
                        break;  

                    // c. 若当前的事件 = 结束事件（非人为原因）
                    case MotionEvent.ACTION_CANCEL:  
                        mPrivateFlags &= ~PRESSED;  
                        refreshDrawableState();  
                        removeTapCallback();  
                        break;

                    // d. 若当前的事件 = 滑动View
                    case MotionEvent.ACTION_MOVE:  
                        final int x = (int) event.getX();  
                        final int y = (int) event.getY();  

                        int slop = mTouchSlop;  
                        if ((x < 0 - slop) || (x >= getWidth() + slop) ||  
                                (y < 0 - slop) || (y >= getHeight() + slop)) {  
                            // Outside button  
                            removeTapCallback();  
                            if ((mPrivateFlags & PRESSED) != 0) {  
                                // Remove any future long press/tap checks  
                                removeLongPressCallback();  
                                // Need to switch from pressed to not pressed  
                                mPrivateFlags &= ~PRESSED;  
                                refreshDrawableState();  
                            }  
                        }  
                        break;  
                }  
                // 若该控件可点击，就一定返回true
                return true;  
            }  
             // 若该控件不可点击，就一定返回false
            return false;  
        }

/**
  * 分析1：performClick（）
  */  
    public boolean performClick() {  

        if (mOnClickListener != null) {  
            playSoundEffect(SoundEffectConstants.CLICK);  
            mOnClickListener.onClick(this);  
            return true;  
            // 只要我们通过setOnClickListener（）为控件View注册1个点击事件
            // 那么就会给mOnClickListener变量赋值（即不为空）
            // 则会往下回调onClick（） & performClick（）返回true
        }  
        return false;  
    }  
```

### 2.3.2 总结

*   每当控件被点击时：

![示意图](http://upload-images.jianshu.io/upload_images/9028834-d8c7445f8c831b22?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 注：`onTouch（）`的执行 先于 `onClick（）`

*   核心方法总结

![示意图](http://upload-images.jianshu.io/upload_images/9028834-b6455bb7cb4baeb5?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 2.3.3 Demo讲解

下面我将用`Demo`验证上述的结论

```
/**
  * 结论验证1：在回调onTouch()里返回false
  */
   // 1\. 通过OnTouchListener()复写onTouch()，从而手动设置返回false
   button.setOnTouchListener(new View.OnTouchListener() {

            @Override
            public boolean onTouch(View v, MotionEvent event) {
                System.out.println("执行了onTouch(), 动作是:" + event.getAction());

                return false;
            }
        });

    // 2\. 通过 OnClickListener（）为控件设置点击事件，为mOnClickListener变量赋值（即不为空），从而往下回调onClick（）
    button.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View v) {
                System.out.println("执行了onClick()");
            }

        });

/**
  * 结论验证2：在回调onTouch()里返回true
  */
   // 1\. 通过OnTouchListener()复写onTouch()，从而手动设置返回true
   button.setOnTouchListener(new View.OnTouchListener() {

            @Override
            public boolean onTouch(View v, MotionEvent event) {
                System.out.println("执行了onTouch(), 动作是:" + event.getAction());

                return true;
            }
        });

    // 2\. 通过 OnClickListener（）为控件设置点击事件，为mOnClickListener变量赋值（即不为空）
    // 但由于dispatchTouchEvent（）返回true，即事件不再向下传递，故不调用onClick()）
    button.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View v) {
                System.out.println("执行了onClick()");
            }

        });

```

*   测试结果 
    ![示意图](http://upload-images.jianshu.io/upload_images/9028834-87cf37b070d32e25?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 2.4 总结

![示意图](http://upload-images.jianshu.io/upload_images/9028834-28ec533ba44b8c9f?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 若您已经看到此处，那么恭喜你，你已经能非常熟悉掌握Android的事件分发机制了

> 即：`Activity`、`ViewGroup`、`View` 的事件分发机制

* * *

# 3\. 工作流程 总结

*   在本节中，我将结合源码，梳理出1个事件分发的工作流程总结，具体如下：

![示意图](http://upload-images.jianshu.io/upload_images/9028834-6d0d586d5cd206ad?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 左侧虚线：具备相关性 & 逐层返回

*   以角色为核心的图解说明

![示意图](http://upload-images.jianshu.io/upload_images/9028834-8a92f564b69d7ced?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   以方法为核心的图解说明

![示意图](http://upload-images.jianshu.io/upload_images/9028834-288ffff4af84522c?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* * *

# 4\. 核心方法总结

*   已知事件分发过程的核心方法为：`dispatchTouchEvent()`、`onInterceptTouchEvent()` 和 `onTouchEvent()` 
    ![示意图](http://upload-images.jianshu.io/upload_images/9028834-2d4ff07818a313b8?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   下面，我将结合总结的工作流程，再次详细讲解该3个方法

### 4.1 dispatchTouchEvent()

*   简介

![示意图](http://upload-images.jianshu.io/upload_images/9028834-f7459d7313d737cd?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![示意图](http://upload-images.jianshu.io/upload_images/9028834-9e4c4e23415cea84?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   返回情况说明

**情况1：默认** 
![示意图](http://upload-images.jianshu.io/upload_images/9028834-c200253e4c51c019?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![示意图](http://upload-images.jianshu.io/upload_images/9028834-c4b3b9245ec1ebc8?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**情况2：返回true** 
![示意图](http://upload-images.jianshu.io/upload_images/9028834-0230e3d697cfadb7?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![示意图](http://upload-images.jianshu.io/upload_images/9028834-c11932dd27112ab8?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**情况3：返回false** 
![示意图](http://upload-images.jianshu.io/upload_images/9028834-3d48898a0c132f4c?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![示意图](http://upload-images.jianshu.io/upload_images/9028834-548c960317e082de?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* * *

### 4.2 onInterceptTouchEvent()

*   简介

![示意图](http://upload-images.jianshu.io/upload_images/9028834-55ffcb949ab75d71?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 注：`Activity`、`View`都无该方法

![示意图](http://upload-images.jianshu.io/upload_images/9028834-c03e1c39acd90ff8?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   返回情况说明

**情况1：true**

![示意图](http://upload-images.jianshu.io/upload_images/9028834-1151af513f2154bc?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![示意图](http://upload-images.jianshu.io/upload_images/9028834-e57d4cc3d2805f7f?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**情况2：false（默认）**

![示意图](http://upload-images.jianshu.io/upload_images/9028834-eb1a974d3685330e?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![示意图](http://upload-images.jianshu.io/upload_images/9028834-93dace95ab8506da?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* * *

### 4.3 onTouchEvent()

*   简介

![示意图](http://upload-images.jianshu.io/upload_images/9028834-8622ef8e624871a9?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![示意图](http://upload-images.jianshu.io/upload_images/9028834-7063f4057fdf42cf?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   返回情况说明

**情况1：返回true**

![示意图](http://upload-images.jianshu.io/upload_images/9028834-b74a978bf0b12234?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![示意图](http://upload-images.jianshu.io/upload_images/9028834-8b0a0bfcff16e1f6?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**情况2：返回false（default）** 
![示意图](http://upload-images.jianshu.io/upload_images/9028834-eac0937c20c8e8c7?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![示意图](http://upload-images.jianshu.io/upload_images/9028834-06adcb9fd14d01b6?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 4.4 三者关系

下面，我用一段伪代码来阐述上述3个方法的关系 & 事件传递规则

```
/**
  * 点击事件产生后
  */ 
  // 步骤1：调用dispatchTouchEvent（）
  public boolean dispatchTouchEvent(MotionEvent ev) {

    boolean consume = false; //代表 是否会消费事件

    // 步骤2：判断是否拦截事件
    if (onInterceptTouchEvent(ev)) {
      // a. 若拦截，则将该事件交给当前View进行处理
      // 即调用onTouchEvent (）方法去处理点击事件
        consume = onTouchEvent (ev) ;

    } else {

      // b. 若不拦截，则将该事件传递到下层
      // 即 下层元素的dispatchTouchEvent（）就会被调用，重复上述过程
      // 直到点击事件被最终处理为止
      consume = child.dispatchTouchEvent (ev) ;
    }

    // 步骤3：最终返回通知 该事件是否被消费（接收 & 处理）
    return consume;

   }
```

* * *

# 5\. 常见的事件分发场景

下面，我将通过实例说明**常见的事件传递情况 & 流程**

### 5.1 背景描述

*   讨论的布局如下：

![示意图](http://upload-images.jianshu.io/upload_images/9028834-a0e2c8c37a890abd?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   情景 

    1.  用户先触摸到屏幕上`View C`上的某个点（图中黄区） 

        > `Action_DOWN`事件在此处产生

    2.  用户移动手指
    3.  最后离开屏幕

### 5.2 一般的事件传递情况

一般的事件传递场景有：

*   默认情况
*   处理事件
*   拦截`DOWN`事件
*   拦截后续事件（`MOVE`、`UP`）

### 场景1：默认

*   即不对控件里的方法（`dispatchTouchEvent()`、`onTouchEvent()`、`onInterceptTouchEvent()`）进行重写 或 更改返回值
*   那么调用的是这3个方法的默认实现：调用下层的方法 & 逐层返回
*   事件传递情况：（呈`U`型） 

    1.  从上往下调用dispatchTouchEvent() 

        > Activity A ->> ViewGroup B ->> View C

    2.  从下往上调用onTouchEvent() 
        View C ->> ViewGroup B ->> Activity A

![示意图](http://upload-images.jianshu.io/upload_images/9028834-6de4a9df80e7fd51?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注：虽然`ViewGroup B`的`onInterceptTouchEvent`（）对`DOWN`事件返回了`false`，但后续的事件`（MOVE、UP）`依然会传递给它的`onInterceptTouchEvent()` 
这一点与`onTouchEvent（）`的行为是不一样的：不再传递 & 接收该事件列的其他事件

### 场景2：处理事件

设`View C`希望处理该点击事件，即：设置`View C`为可点击的`（Clickable）` 或 复写其`onTouchEvent（）`返回`true`

> 最常见的：设置`Button`按钮来响应点击事件

事件传递情况：（如下图）

*   `DOWN`事件被传递给C的`onTouchEvent`方法，该方法返回`true`，表示处理该事件
*   因为`View C`正在处理该事件，那么`DOWN`事件将不再往上传递给ViewGroup B 和 `Activity A`的`onTouchEvent()`；
*   该事件列的其他事件`（Move、Up）`也将传递给`View C`的`onTouchEvent()`

![示意图](http://upload-images.jianshu.io/upload_images/9028834-22ee24fbdba81398?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 会逐层往`dispatchTouchEvent()` 返回，最终事件分发结束

### 场景3：拦截DOWN事件

假设`ViewGroup B`希望处理该点击事件，即`ViewGroup B`复写了`onInterceptTouchEvent()`返回`true`、`onTouchEvent()`返回`true` 
事件传递情况：（如下图）

*   `DOWN`事件被传递给`ViewGroup B`的`onInterceptTouchEvent()`，该方法返回`true`，表示拦截该事件，即自己处理该事件（事件不再往下传递）
*   调用自身的`onTouchEvent()`处理事件（`DOWN`事件将不再往上传递给`Activity A`的`onTouchEvent()`）

*   该事件列的其他事件`（Move、Up）`将直接传递给`ViewGroup B`的`onTouchEvent()`

    > 注：
    >
    > 1.  该事件列的其他事件`（Move、Up）`将不会再传递给`ViewGroup B`的`onInterceptTouchEvent`（）；因：该方法一旦返回一次`true`，就再也不会被调用
    > 2.  逐层往`dispatchTouchEvent()` 返回，最终事件分发结束

![示意图](http://upload-images.jianshu.io/upload_images/9028834-4f6acd9490d56d8f?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 场景4：拦截DOWN的后续事件

**结论**

*   若 `ViewGroup` 拦截了一个半路的事件（如`MOVE`），该事件将会被系统变成一个`CANCEL`事件 & 传递给之前处理该事件的子`View`；
*   该事件不会再传递给`ViewGroup` 的`onTouchEvent()`
*   只有再到来的事件才会传递到`ViewGroup`的`onTouchEvent()`

**场景描述** 
`ViewGroup B` 无拦截`DOWN`事件（还是`View C`来处理`DOWN`事件），但它拦截了接下来的`MOVE`事件

> 即 `DOWN`事件传递到`View C`的`onTouchEvent（）`，返回了`true`

**实例讲解**

*   在后续到来的MOVE事件，`ViewGroup B` 的`onInterceptTouchEvent（）`返回`true`拦截该`MOVE`事件，但该事件并没有传递给`ViewGroup B` ；这个`MOVE`事件将会被系统变成一个`CANCEL`事件传递给`View C`的`onTouchEvent（）`
*   后续又来了一个`MOVE`事件，该`MOVE`事件才会直接传递给`ViewGroup B` 的`onTouchEvent()`

    > 后续事件将直接传递给`ViewGroup B` 的`onTouchEvent()`处理，而不会再传递给`ViewGroup B` 的`onInterceptTouchEvent（）`，因该方法一旦返回一次true，就再也不会被调用了。

*   `View C`再也不会收到该事件列产生的后续事件

![示意图](http://upload-images.jianshu.io/upload_images/9028834-68b3a67e8613b366?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

至此，关于`Android`常见的事件传递情况 & 流程已经讲解完毕。

* * *

# 6\. 额外知识

### 6.1 Touch事件的后续事件（MOVE、UP）层级传递

*   若给控件注册了`Touch`事件，每次点击都会触发一系列`action`事件（ACTION_DOWN，ACTION_MOVE，ACTION_UP等）
*   当`dispatchTouchEvent（）`事件分发时，只有前一个事件（如ACTION_DOWN）返回true，才会收到后一个事件（ACTION_MOVE和ACTION_UP） 

> 即如果在执行ACTION_DOWN时返回false，后面一系列的ACTION_MOVE、ACTION_UP事件都不会执行 
> 从上面对事件分发机制分析知：

*   dispatchTouchEvent()、 onTouchEvent() 消费事件、终结事件传递（返回true）
*   而onInterceptTouchEvent 并不能消费事件，它相当于是一个分叉口起到分流导流的作用，对后续的ACTION_MOVE和ACTION_UP事件接收起到非常大的作用 

> 请记住：接收了ACTION_DOWN事件的函数不一定能收到后续事件（ACTION_MOVE、ACTION_UP） 
> **这里给出ACTION_MOVE和ACTION_UP事件的传递结论**：

*   结论1 
    若对象（Activity、ViewGroup、View）的dispatchTouchEvent()分发事件后消费了事件（返回true），那么收到ACTION_DOWN的函数也能收到ACTION_MOVE和ACTION_UP 

> 黑线：ACTION_DOWN事件传递方向 
> 红线：ACTION_MOVE 、 ACTION_UP事件传递方向 
> ![流程讲解](http://upload-images.jianshu.io/upload_images/9028834-45743a1d1517f339?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   结论2 
    若对象（Activity、ViewGroup、View）的onTouchEvent()处理了事件（返回true），那么ACTION_MOVE、ACTION_UP的事件从上往下传到该`View`后就不再往下传递，而是直接传给自己的`onTouchEvent()`& 结束本次事件传递过程。 

> 黑线：ACTION_DOWN事件传递方向 
> 红线：ACTION_MOVE、ACTION_UP事件传递方向 
> ![流程讲解](http://upload-images.jianshu.io/upload_images/9028834-6c154df6f4a5b468?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 6.2 onTouch()和onTouchEvent()的区别

*   该2个方法都是在`View.dispatchTouchEvent（）`中调用
*   但`onTouch（）`优先于`onTouchEvent`执行；若手动复写在`onTouch（）`中返回`true`（即 将事件消费掉），将不会再执行`onTouchEvent（）`

> 注：若1个控件不可点击（即非`enable`），那么给它注册`onTouch`事件将永远得不到执行，具体原因看如下代码

```
// &&为短路与，即如果前面条件为false，将不再往下执行
//  故：onTouch（）能够得到执行需2个前提条件：
     // 1\. mOnTouchListener的值不能为空
     // 2\. 当前点击的控件必须是enable的
mOnTouchListener != null && (mViewFlags & ENABLED_MASK) == ENABLED &&  
            mOnTouchListener.onTouch(this, event)

// 对于该类控件，若需监听它的touch事件，就必须通过在该控件中重写onTouchEvent（）来实现
```

* * *

# 7\. 总结

*   通过阅读本文，相信您已经可以全面了解`Android`事件分发机制
*   与`Android`事件分发最相关的知识：**自定义View**系列文章 
    [自定义View基础 - 最易懂的自定义View原理系列（1）](http://blog.csdn.net/carson_ho/article/details/56009827) 
    [自定义View Measure过程 - 最易懂的自定义View原理系列（2）](http://blog.csdn.net/carson_ho/article/details/56011064) 
    [自定义View Layout过程 - 最易懂的自定义View原理系列（3）](http://blog.csdn.net/carson_ho/article/details/56011112) 
    [自定义View Draw过程- 最易懂的自定义View原理系列（4）](http://blog.csdn.net/carson_ho/article/details/56011153)

*   接下来我将继续介绍与`Android`事件分发最相关的知识：**自定义View**，有兴趣可以继续关注[Carson_Ho的安卓开发笔记](http://blog.csdn.net/carson_ho)