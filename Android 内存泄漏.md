<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [【Android 内存泄漏】](#android-%E5%86%85%E5%AD%98%E6%B3%84%E6%BC%8F)
- [引用：](#%E5%BC%95%E7%94%A8)
- [引言](#%E5%BC%95%E8%A8%80)
  - [java的内存分配：](#java%E7%9A%84%E5%86%85%E5%AD%98%E5%88%86%E9%85%8D)
    - [静态存储区（方法区）](#%E9%9D%99%E6%80%81%E5%AD%98%E5%82%A8%E5%8C%BA%E6%96%B9%E6%B3%95%E5%8C%BA)
    - [栈区](#%E6%A0%88%E5%8C%BA)
    - [堆区](#%E5%A0%86%E5%8C%BA)
    - [栈与堆的区别](#%E6%A0%88%E4%B8%8E%E5%A0%86%E7%9A%84%E5%8C%BA%E5%88%AB)
  - [Java如何管理内存](#java%E5%A6%82%E4%BD%95%E7%AE%A1%E7%90%86%E5%86%85%E5%AD%98)
  - [Java中的内存泄漏](#java%E4%B8%AD%E7%9A%84%E5%86%85%E5%AD%98%E6%B3%84%E6%BC%8F)
  - [四种引用类型的介绍](#%E5%9B%9B%E7%A7%8D%E5%BC%95%E7%94%A8%E7%B1%BB%E5%9E%8B%E7%9A%84%E4%BB%8B%E7%BB%8D)
    - [1. 强引用(StrongReference)](#1-%E5%BC%BA%E5%BC%95%E7%94%A8strongreference)
    - [2. 软引用(SoftReference)](#2-%E8%BD%AF%E5%BC%95%E7%94%A8softreference)
    - [3. 弱引用(WeakReference)](#3-%E5%BC%B1%E5%BC%95%E7%94%A8weakreference)
    - [4. 虚引用(PhantomReference)](#4-%E8%99%9A%E5%BC%95%E7%94%A8phantomreference)
    - [对比](#%E5%AF%B9%E6%AF%94)
  - [内存抖动](#%E5%86%85%E5%AD%98%E6%8A%96%E5%8A%A8)
  - [内存泄漏检测工具](#%E5%86%85%E5%AD%98%E6%B3%84%E6%BC%8F%E6%A3%80%E6%B5%8B%E5%B7%A5%E5%85%B7)
    - [MAT](#mat)
    - [LeakCanary](#leakcanary)
- [Android 内存泄漏](#android-%E5%86%85%E5%AD%98%E6%B3%84%E6%BC%8F)
  - [概述](#%E6%A6%82%E8%BF%B0)
  - [单例造成的内存泄漏](#%E5%8D%95%E4%BE%8B%E9%80%A0%E6%88%90%E7%9A%84%E5%86%85%E5%AD%98%E6%B3%84%E6%BC%8F)
    - [原因：](#%E5%8E%9F%E5%9B%A0)
    - [解决：](#%E8%A7%A3%E5%86%B3)
  - [非静态内部类造成的内存泄漏](#%E9%9D%9E%E9%9D%99%E6%80%81%E5%86%85%E9%83%A8%E7%B1%BB%E9%80%A0%E6%88%90%E7%9A%84%E5%86%85%E5%AD%98%E6%B3%84%E6%BC%8F)
    - [外部类中持有非静态内部类的静态对象](#%E5%A4%96%E9%83%A8%E7%B1%BB%E4%B8%AD%E6%8C%81%E6%9C%89%E9%9D%9E%E9%9D%99%E6%80%81%E5%86%85%E9%83%A8%E7%B1%BB%E7%9A%84%E9%9D%99%E6%80%81%E5%AF%B9%E8%B1%A1)
      - [补充：各种context使用场景](#%E8%A1%A5%E5%85%85%E5%90%84%E7%A7%8Dcontext%E4%BD%BF%E7%94%A8%E5%9C%BA%E6%99%AF)
    - [Handler 或 Runnable 作为非静态内部类](#handler-%E6%88%96-runnable-%E4%BD%9C%E4%B8%BA%E9%9D%9E%E9%9D%99%E6%80%81%E5%86%85%E9%83%A8%E7%B1%BB)
      - [问题1：](#%E9%97%AE%E9%A2%981)
      - [解决1：](#%E8%A7%A3%E5%86%B31)
      - [问题2：](#%E9%97%AE%E9%A2%982)
      - [解决2：](#%E8%A7%A3%E5%86%B32)
    - [匿名类被异步线程所引用](#%E5%8C%BF%E5%90%8D%E7%B1%BB%E8%A2%AB%E5%BC%82%E6%AD%A5%E7%BA%BF%E7%A8%8B%E6%89%80%E5%BC%95%E7%94%A8)
  - [其他的内存泄漏情况](#%E5%85%B6%E4%BB%96%E7%9A%84%E5%86%85%E5%AD%98%E6%B3%84%E6%BC%8F%E6%83%85%E5%86%B5)
    - [集合对象及时清除](#%E9%9B%86%E5%90%88%E5%AF%B9%E8%B1%A1%E5%8F%8A%E6%97%B6%E6%B8%85%E9%99%A4)
    - [webView](#webview)
  - [应用](#%E5%BA%94%E7%94%A8)
    - [MVP](#mvp)
      - [解决：](#%E8%A7%A3%E5%86%B3-1)
  - [补充：占用内存大且生命周期较长的对象](#%E8%A1%A5%E5%85%85%E5%8D%A0%E7%94%A8%E5%86%85%E5%AD%98%E5%A4%A7%E4%B8%94%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E8%BE%83%E9%95%BF%E7%9A%84%E5%AF%B9%E8%B1%A1)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# 【Android 内存泄漏】

# 引用：
★★★ [【知识必备】内存泄漏全解析，从此拒绝ANR，让OOM远离你的身边，跟内存泄漏say byebye](https://www.cnblogs.com/liushilin/p/5900089.html)
★★★★★ [Android 中内存泄漏的原因和解决方案](https://www.jianshu.com/p/abee7c186bfa)

# 引言

对于C++来说，内存泄漏就是new出来的对象没有delete，俗称野指针；而对于java来说，就是new出来的Object放在Heap上无法被GC回收。

## java的内存分配：

### 静态存储区（方法区）

编译时就分配好，在**程序整个运行期间都存在**。它主要**存放静态数据**、**全局 static 数据**和**常量**。

### 栈区

当**方法执行时**，会在栈区内存中创建方法体内部的**局部变量**（其中包括基础数据类型、对象的引用），方法**结束后自动释放**内存。
因为栈内存分配运算内置于处理器的指令集中，效率很高，但是分配的内存容量有限。

### 堆区

又称动态内存分配，通常用来**存放**new出来的**对象**，也就是对象的实例。由java垃圾回收期回收。

### 栈与堆的区别

在方法体内定义的（**局部变量**）一些**基本类型的变量**和**对象的引用变量**都是在方法的**栈内存**中分配的。
当在一段方法块中定义一个变量时，Java 就会在栈中为该变量分配内存空间，当超过该变量的作用域后，该变量也就无效了，分配给它的内存空间也将被释放掉，该内存空间可以被重新使用。

**堆内存**用来存放所有**由 new 创建的对象**（包括该**对象其中的所有成员变量**）和**数组**。
在堆中分配的内存，将由 Java 垃圾回收器来自动管理。
在**堆中产生**了一个**数组**或者**对象**后，还可以在**栈中**定义一个特殊的**变量**，这个变量的取值**等于**数组或者对象在堆内存中的**首地址**，这个特殊的变量就是我们上面说的**引用变量**。我们可以通过这个引用变量来**访问堆中的对象或者数组**。

举个例子：

```java
public class Sample {
    int s1 = 0;
    Sample mSample1 = new Sample();
    public void method() {
        int s2 = 1;
        Sample mSample2 = new Sample();
    }
}
Sample mSample3 = new Sample();
```

Sample 类的局部变量 s2 和引用变量 mSample2 都是存在于栈中，但 mSample2 指向的对象是存在于堆上的。 mSample3 指向的对象s实体存放在堆上，包括这个对象的所有成员变量 s1 和 mSample1，而它自己存在于栈中。

**结论**：

**局部变量**的**基本数据类型**和**引用**存储于**栈**中，引用的**对象实体**存储于**堆**中。—— 因为它们**属于方法**中的变量，**生命周期随方法而结束**。

**成员变量**全部存储与**堆**中（包括基本数据类型，引用和引用的对象实体）—— 因为它们**属于类**，类对象终究是要被new出来使用的。

##  Java如何管理内存

Java的内存管理就是**对象的分配和释放**问题。
在 Java 中，程序员需要通过关键字 **new 为**每个**对象申请内存空间** (基本类型除外)，所有的对象都在**堆** (Heap)中分配空间。
在 Java 中，**内存**的**分配**是由**程序完成**的，而**内存**的**释放**是由 **GC 完成**的，这种收支两条线的方法确实简化了程序员的工作。但同时，它也加重了JVM的工作。这也是 Java 程序运行速度较慢的原因之一。因为，**GC** 为了能够正确释放对象，GC 必须**监控每一个对象的运行状态**，包括对象的申请、引用、被引用、赋值等，GC 都需要进行监控。

监视对象状态是为了更加准确地、及时地释放对象，而**释放对象**的根本**原则**就是**该对象不再被引用**。

为了更好理解 GC 的工作原理，我们可以将对象考虑为有向图的顶点，将引用关系考虑为图的有向边，有向边从引用者指向被引对象。另外，每个线程对象可以作为一个图的起始顶点，例如大多程序从 main 进程开始执行，那么该图就是以 main 进程顶点开始的一棵根树。在这个有向图中，根顶点可达的对象都是有效对象，GC将不回收这些对象。如果某个对象 (连通子图)与这个根顶点不可达(注意，该图为有向图)，那么我们认为这个(这些)对象不再被引用，可以被 GC 回收。 以下，我们举一个例子说明如何用有向图表示内存管理。对于程序的每一个时刻，我们都有一个有向图表示JVM的内存分配情况。以下右图，就是左边程序运行到第6行的示意图。

 

![](https://upload-images.jianshu.io/upload_images/9028834-55b691e8aa72a8ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Java使用有向图的方式进行内存管理，可以消除引用循环的问题，例如有三个对象，相互引用，只要它们和根进程不可达的，那么GC也是可以回收它们的。这种方式的优点是管理内存的精度很高，但是效率较低。另外一种常用的内存管理技术是使用计数器，例如COM模型采用计数器方式管理构件，它与有向图相比，精度行低(很难处理循环引用的问题)，但执行效率很高。

## Java中的内存泄漏

在Java中，内存泄漏就是存在一些被分配的对象，这些对象有下面两个特点：
首先，这些对象是**可达**的，即在有向图中，存在通路可以与其相连；
其次，这些对象是**无用**的，即程序以后不会再使用这些对象。
如果对象满足这两个条件，这些对象就可以判定为Java中的内存泄漏，这些对象不会被GC所回收，然而它却占用内存。 

## 四种引用类型的介绍

### 1. 强引用(StrongReference)

JVM 宁可抛出 OOM ，也**不**会让 GC **回收**具有强引用的对象；

### 2. 软引用(SoftReference)

只有在**内存空间不足**时，**才**会被**回收**的对象；

### 3. 弱引用(WeakReference)

在 GC 时，一旦发现了**只具有弱引用**的对象，不管当前内存空间足够与否，**都**会**回收**它的内存；

### 4. 虚引用(PhantomReference)

**任何时候都**可以被GC**回收**，当垃圾回收器准备回收一个对象时，如果发现它还有虚引用，就会在回收对象的内存之前，把这个虚引用加入到与之关联的引用队列中。程序可以通过判断引用队列中是否存在该对象的虚引用，来了解这个对象是否将要被回收。可以用来作为GC回收Object的标志。

### 对比

![](http://upload-images.jianshu.io/upload_images/9028834-7127d000bf5a2f56.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

资源来自 [https://developer.android.com/topic/performance/graphics/cache-bitmap.html](https://developer.android.com/topic/performance/graphics/cache-bitmap.html)（Google官网，你可能需要梯子）

在 Google 官网介绍中，有这样一段话。

![image](http://upload-images.jianshu.io/upload_images/9028834-0783de967d692a93.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

大意是说，从API 9 开始，垃圾收集器会更积极收集软/弱引用，使软/弱引用相当无效。而且在API 3 之前，位图的备份数据存储在本地内存中，该内存不以可预测的方式释放，可能导致应用程序短暂超出其内存限制和崩溃。

 

## 内存抖动

这样的图很熟悉有木有？当这样的时候，说明你的内存真的在变少了。
[![](http://upload-images.jianshu.io/upload_images/9028834-997fa8a23de229ab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://upload-images.jianshu.io/upload_images/9028834-997fa8a23de229ab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 内存泄漏检测工具

在这里先推荐两种内存检查的方式：

### MAT

MAT(Memory Analyzer Tool)，[点我下载](http://www.eclipse.org/mat/downloads.php)。具体使用这个网上一大篇。

### LeakCanary

强大的开源内存检测工具LeakCanary。

leakcanary是一个开源项目，一个内存泄露自动检测工具，是著名的GitHub开源组织Square贡献的，它的主要优势就在于自动化过早的发觉内存泄露、配置简单、抓取贴心，缺点在于还存在一些bug，不过正常使用百分之九十情况是OK的，其核心原理与MAT工具类似。

配置十分简单，可以看[官方文档](https://github.com/square/leakcanary)，简单直白。

# Android 内存泄漏

## 概述

在 Android 中内存泄漏的原因其实和在 Java 中是一样的，即**某个对象已经不需要再用了**，但是它**却没有被系统所回收**，一直在内存中占用着空间，而导致它无法被回收的原因大多是由于它被一个生命周期更长的对象所引用。其实要分析 Android 中的内存泄漏的原因非常简单，只要理解一句话，那就是**生命周期较长的对象持有生命周期较短的对象的引用。**

举个例子，如果一个 Activity 被一个单例对象所引用，那么当退出这个 Activity 时，由于单例的对象依然存在（单例对象的生命周期跟整个 App 的生命周期一致），而单例对象又持有 Activity 的引用，这就导致了此 Activity 无法被回收，从而造成内存泄漏。

## 单例造成的内存泄漏

​       单例的使用在我们的程序中随处可见，因为使用它可以完美的解决我们在程序中重复创建对象的问题，不过可别小瞧它。由于**单例**的**静态**特性，使得它的**生命周期和应用的生命周期会一样长**，所以一旦使用有误，小心无限制的持有Activity的引用而导致内存泄漏。比如，下面的例子。

```java
public class SingletonBad {
    private static SingletonBad singletonBad;
    private Context context;

    private SingletonBad(Context context){
        this.context = context;
    }

    public static SingletonBad getInstance(Context context){
        if (singletonBad == null){
            singletonBad = new SingletonBad(context);
        }
        return singletonBad;
    }
}
```

### 原因：

这个错误在生活中再普遍不过，很正常的一个单例模式，可就由于传入的是一个Context，而这个Context的生命周期的长短就尤为重要了。**如**果我们**传入**的是**Activity的Context**，当这个Context所对应的**Activity退出**的时候，由于该**Context**的引用**被单例**对象所**持有**，其**生命周期等于整个应用程序**的生命周期，**所以**当前**Activity退出**时它的**内存并不会回收**，这造成的内存泄漏就可想而知了。

### 解决：

正确的方式应该**是把传入的Context换为和应用的生命周期一样长的Application的Context**;

```java
public class SingletonGood {
    private static SingletonGood singletonGood;
    private Context context;

    private SingletonGood(Context context){
        this.context = context.getApplicationContext();//获取Application的context避免内存泄漏
    }

    public static SingletonGood getInstance(Context context){
        if (singletonGood == null){
            singletonGood = new SingletonGood(context);
        }
        return singletonGood;
    }
}
```

当然，你也可以直接连Context都不用传入了。重写application，提供静态的getContext方法

```java
public class DemoApplication extends Application {
    private static DemoApplication demoApplication;

    public static DemoApplication getInstance(){
        return demoApplication;
    }

    public static Context getContext(){
        return demoApplication.getApplicationContext();
    }

    @Override
    public void onCreate() {
        super.onCreate();
        demoApplication = this;
    }
}
```

自然就可以直接不用传入Context

```java
public class SingletonGoodNew {
    private static SingletonGoodNew singletonGoodNew;

    private Context context;
    
    private SingletonGoodNew(){
        this.context = DemoApplication.getContext();
    }

    public static SingletonGoodNew getInstance(){
        if (singletonGoodNew == null){
            singletonGoodNew = new SingletonGoodNew();
        }
        return singletonGoodNew;
    }
}
```

## 非静态内部类造成的内存泄漏

我们知道，**非静态内部类默认会持有外部类的引用**，**如**果这个非静态的**内部类**的**生命周期比**它的**外部类**的生命周期**长**，那么当销毁**外部类**的时候，它**无法被回收**，就会造成**内存泄漏**。

### 外部类中持有非静态内部类的静态对象
假设 Activity 的代码是这样的

```java
public class MainActivity extends AppCompatActivity {
    
    private static Test test;    
    private class Test {
    }
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        if (test == null) {
            test = new Test();
        }
    }    
}
```
原因：
这个其实和单例的原理是一样的，由于**static静态对象** test 的生命周期**和整个应用的生命周期一致**，而**非静态内部类** Test **持有外部类** MainActivity 的**引用**，导致 **MainActivity 退出**的时候**不能被回收**，从而造成内存泄漏。


解决：
把 **test 改成非静态**，这样 test 的生命周期和 MainActivity 是一样的了，就避免了内存泄漏。
**或**者也可以把 **Test 改成静态内部类**，让 test 不持有 MainActivity 的引用，不过一般没有这种操作。

补充：
**成员变量是static**的**时**候，那么它的**生命周期将和整个app的生命周期一致**。

这必然会导致一系列问题，如果你的app进程设计上是长驻内存的，那即使app切到后台，这部分内存也不会被释放。按照现在手机app内存管理机制，占内存较大的后台进程将优先回收，因为如果此app做过进程互保保活，那会造成app在后台频繁重启。当手机安装了你参与开发的app以后一夜时间手机被消耗空了电量、流量，你的app不得不被用户卸载或者静默。

这里修复的方法是：
不要在类初始时初始化静态成员。可以考虑**lazy初始化（延迟加载）**。架构设计上要思考是否真的有必要这样做，尽量避免。如果架构需要这么设计，那么此对象的生命周期你有责任管理起来。

#### 补充：各种context使用场景
Application 的 context 不是万能的，所以也不能随便乱用，对于有些地方则必须使用 Activity 的 Context，对于Application，Service，Activity三者的Context的应用场景如下：

![image](http://upload-images.jianshu.io/upload_images/9028834-0e88278566a86b6b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其中： NO1表示 Application 和 Service 可以启动一个 Activity，不过需要创建一个新的 task 任务队列。而对于 Dialog 而言，只有在 Activity 中才能创建


### Handler 或 Runnable 作为非静态内部类

Handler、Message和MessageQueue都是相互关联在一起的，万一**Handler发送的Message尚未被处理，则该Message及发送它的Handler对象将会被线程MessageQueue一直持有**。
由于**Handler属于TLS（Thread Local Storage）变量**，生命周期和Activity是不一致的。
因此这种实现方式一般很难保证跟View或者Activity的生命周期一致，故很容易导致无法正确释放。比如：

#### 问题1：
**handler** 和 **runnable** 都有**定时器**的功能，当它们作为**非静态内部类**的时候，同样会**持有外部类的引用**，**如**果它们的内部**有延迟**操作，在延迟操作还没有发生的时候，销毁了外部类，那么**外部类对象无法回收**，从而造成内存泄漏，假设 Activity 的代码如下

```java
public class MainActivity extends AppCompatActivity {
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        
        new Handler().postDelayed(new Runnable() {
            @Override
            public void run() {
                
            }
        }, 10 * 1000);
    }
}
```

上面的代码中，Handler 和 Runnable 作为匿名内部类，都会持有 MainActivity 的引用，而它们内部有一个 10 秒钟的定时器，如果在打开 MainActivity 的 10 秒内关闭了 MainActivity，那么由于 Handler 和 Runnable 的生命周期比 MainActivity 长，会导致 MainActivity 无法被回收，从而造成内存泄漏。

#### 解决1：
那么应该如何避免内存泄漏呢？这里的一般套路就是**把 Handler 和 Runnable 定义为静态内部类**，这样它们就不再持有 MainActivity 的引用了，从而避免了内存泄漏

```java
public class MainActivity extends AppCompatActivity {
    private Handler handler;
    private static class TestHandler extends Handler {
    }
    
    private Runnable runnable;
    private static class TestRunnable implements Runnable {
        @Override
        public void run() {
        }
    }

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        handler = new TestHandler();
        runnable = new TestRunnable();
        handler.postDelayed(runnable, 10 * 1000);
    }
}
```

最好再**在 onDestory 调用 handler 的 removeCallbacks** 方法来移除 Message，这样不但能避免内存泄漏，而且在退出 Activity 时取消了定时器，保证 10 秒以后也不会执行 run 方法

```java
@Override
protected void onDestroy() {
    super.onDestroy();
    handler.removeCallbacks(runnable);
}
```
下面几个方法都可以移除 Message：
![image](http://upload-images.jianshu.io/upload_images/9028834-45919a981d482b65.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 问题2：
还有一种特殊情况，如果 Handler 或者 Runnable 中**持有 Context 对象**，那么即使使用静态内部类，还是会发生内存泄漏

```java
public class MainActivity extends AppCompatActivity {

    private Handler handler;
    private static class TestHandler extends Handler {
        private Context context;
        private TestHandler(Context context) {
            this.context = context;
        }
    }

    private Runnable runnable;
    private static class TestRunnable implements Runnable {
        @Override
        public void run() {
        }
    }

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        handler = new TestHandler(this);
        runnable = new TestRunnable();
        handler.postDelayed(runnable, 10 * 1000);
    }
}
```

上面的代码，使用 [leakcanary](https://link.jianshu.com/?t=https://github.com/square/leakcanary)工具会发现依然会发生内存泄漏，而且造成内存泄漏的原因和之前用非静态内部类是一样的，那么为什么会出现这种情况呢？

这是由于在 Handler 中持有 Context 对象，而这个 Context 对象是通过 TestHandler 的构造方法传入的，它是一个 MainActivity 对象，也就是说，虽然 TestHandler 作为静态内部类不会持有外部类 MainActivity 的引用，但是我们在调用它的**构造方法**时，自己**传入了 MainActivity 的对象**，从而 **handler** 对象**持有了 MainActivity 的引用**，**handler 的生命周期比 MainActivity 的生命周期长**，因此会造成内存泄漏。

#### 解决2：
这种情况可以使用**弱引用**的方式来引用 Context 来避免内存泄漏，代码如下
推荐使用**静态内部类+弱引用WeakReference**这种方式，但要注意**每次使用前判空**。
```java
public class HandlerGoodActivity extends AppCompatActivity {

    private final MyHandler myHandler = new MyHandler(this);
    private static final class MyHandler extends Handler{
        private final WeakReference<HandlerGoodActivity> mActivity;

        public MyHandler(HandlerGoodActivity activity){
            this.mActivity = new WeakReference<HandlerGoodActivity>(activity);//使用弱引用
        }

        @Override
        public void handleMessage(Message msg) {
            super.handleMessage(msg);
            HandlerGoodActivity activity = mActivity.get();
            if (activity != null){
            }
        }
    }

    // 匿名内部类在static的时候绝对不会持有外部类的引用
    private static final Runnable RUNNABLE = new Runnable() {
        @Override
        public void run() {
        }
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_handler_good);

        myHandler.postDelayed(RUNNABLE,1000*60*5);
    }
}
```

综述：推荐使用**静态内部类+弱引用WeakReference**这种方式，但要注意**每次使用前判空**。

创建一个静态Handler内部类，然后对 Handler 持有的对象使用弱引用，这样在回收时也可以回收 Handler 持有的对象，但是这样做虽然避免了 Activity 泄漏，不过 Looper 线程的消息队列中还是可能会有待处理的消息，所以我们在 Activity 的 **Destroy 时或者 Stop 时应该移除消息队列 MessageQueue 中的消息**。


### 匿名类被异步线程所引用

android开发经常会继承实现Activity或者Fragment或者View。如果你使用了**匿名类**，而又**被异步线程所引用**，那得小心，如果没有任何措施同样会导致内存泄漏的

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_inner_bad);

        Runnable runnable1 = new MyRunnable();
        Runnable runnable2 = new Runnable() {
            @Override
            public void run() {
            }
        };
    }

    private static class MyRunnable implements Runnable{
        @Override
        public void run() {
        }
    }
}
```

runnable1 和 runnable2的区别就是，runnable2使用了匿名内部类，我们看看引用时的引用内存

![](http://upload-images.jianshu.io/upload_images/9028834-8b83673fecb23bf2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到，runnable1是没有什么特别的。但runnable2多出了一个MainActivity的引用，若是这个引用再传入到一个异步线程，此线程在和Activity生命周期不一致的时候，也就造成了Activity的泄露。



## 其他的内存泄漏情况

- **创建与关闭**没有成对出现造成的泄露：譬如**Cursor**资源必须手动关闭，**IO流**等对象必须手动关闭，**WebView**必须手动销毁等。
- **对象的注册与反注册**没有成对出现造成的内存泄露；譬如注册**广播**接收器、注册**观察者**（典型的譬如数据库的监听）等。
- 构造**Adapter**时，没有使用缓存的 **convertView**
- **Bitmap**对象不在使用时调用**recycle**()释放内存
- 警惕**线程**未终止造成的内存泄露：
  譬如在Activity中关联了一个生命周期超过Activity的Thread，在**退出Activity时**切记**结束线程**。一个典型的例子就是HandlerThread的run方法是一个死循环，它不会自己结束，线程的生命周期超过了Activity生命周期，我们必须手动在Activity的销毁方法中中调运thread.getLooper().quit();才不会泄露。
- 不要在执行频率很高的方法或者**循环**中**创建对象**（比如onmeasure）
- 可以使用**HashTable**等创建一组对象容器从容器中**取**那些**对象**，而不用每次new与释放。
- **避免**代码设计模式的错误造成内存泄露；譬如**循环引用**，A持有B，B持有C，C持有A，这样的设计谁都得不到释放。


### 集合对象及时清除

我们通常会把一些对象的引用加入到集合容器（比如ArrayList）中，当我们不再需要该对象时，并没有把它的引用从集合中清理掉，这样这个集合就会越来越大。如果这个集合是static的话，那情况就更严重了。

所以在退出程序之前，**将集合里面的东西clear，然后置为null，再退出程序**，如下：

```java
    private List<String> nameList;
    private List<Fragment> list;

    @Override
    public void onDestroy() {
        super.onDestroy();
        if (nameList != null){
            nameList.clear();
            nameList = null;
        }
        if (list != null){
            list.clear();
            list = null;
        }
    }
```

### webView

当我们**不再需要**使用webView的时候，应该调用它的**destory**()方法来销毁它，并释放其占用的内存，否则其占用的内存长期也不能回收，从而造成内存泄漏。

解决方案：
为webView开启另外一个进程，通过AIDL与主线程进行通信，webView所在的进程可以根据业务的需要选择合适的时机进行销毁，从而达到内存的完整释放。

而另外一些诸如listView的Adapter没有缓存之类的这里就不再多提了。


## 应用
### MVP
在 MVP 的架构中，通常 **Presenter** 要同时**持有 View 和 Model 的引用**，如果在 Activity 退出的时候，**Presenter** 正在进行一个**耗时**操作，那么 Presenter 的生命周期会比 Activity 长，导致 **Activity 无法回收**，造成内存泄漏

```java
public class MainActivity extends AppCompatActivity implements TestView {
    private Presenter presenter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        presenter = new Presenter(this);
        presenter.request();
    }
}
```

```java
public class Presenter {
    private TestView view;
    private Model model;

    public Presenter(TestView view) {
        this.view = view;
        model = new Model();
    }

    public void request() {
        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    Thread.sleep(10 * 1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }).start();
    }
}
```

上面的代码中，假设 request 是一个需要耗时 10 秒的操作，那么在 10 秒之内如果退出 Activity 就会内存泄漏。

#### 解决：
使用弱引用，引用视图

```java
public class Presenter {
    private WeakReference<T> mWeakReference;
    private Model model;
    public Presenter(T baseView) {
        setView(baseView);
        model = new Model();
    }

    public T getView() {
        return mWeakReference != null ? mWeakReference.get() : null;
    }

    public void setView(T view) {
        if (view != null) {
            this.mWeakReference = new WeakReference<>(view);
        }
    }

    public void request() {
        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    Thread.sleep(10 * 1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }).start();
    }
}
```


## 补充：占用内存大且生命周期较长的对象

在android开发中，为了防止内存溢出，在处理一些**占用内存大并且生命周期较长的对象**的时候，可以**尽量地使用软引用和弱引用**技术。

```java
public class CacheBySoftRef {
    // 首先定义一个HashMap,保存软应用对象
    private HashMap<String,SoftReference<Bitmap>> imageCache =  new HashMap<>();
    
    // 再来定义一个方法，保存Bitmap的软引用到HashMap
    public void addBitmapToCache(String path){
        // 强引用的Bitmap对象
        Bitmap bitmap = BitmapFactory.decodeFile(path);
        // 软引用的Bitmap对象
        SoftReference<Bitmap> softBitmap = new SoftReference<Bitmap>(bitmap);
        // 添加该对象到Map使其缓存
        imageCache.put(path,softBitmap);
    }

    // 获取的时候，可以通过SoftReference的get()的方法得到Bitmap对象
    public Bitmap getBitmapByPath(String path){
        // 从缓存中取软引用的Bitmap对象
        SoftReference<Bitmap> softBitmap = imageCache.get(path);
        // 判断是否存在软引用
        if (softBitmap == null){
            return null;
        }
        // 通过软引用取出Bitmap对象，如果由于内存不足Bitmap被回收，则取得空；
        // 如果未被回收，则可重复使用，提高速度
        Bitmap bitmap = softBitmap.get();
        return bitmap;
    }
}
```

使用**软引用**以后，在**OutOfMemory**异常发生之**前**，这些**缓存**的**图片**资源的内存空间可以**被释放**掉的，从而避免内存达到上限，避免Crash发生。

如果只是想避免OutOfMemory异常的发生，则可以使用软引用。如果对于应用的性能更在意，想**尽快回收**一些占用内存比较大的对象，则可以使用**弱引用**。

另外可以根据对象是否经常使用来判断选择软引用还是弱引用。如果该对象可能会**经常使用**的，就尽量用**软引用**。如果该对象不被使用的可能性更大些，就可以用弱引用。