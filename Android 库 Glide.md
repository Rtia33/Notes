<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [【Android 库 Glide】](#android-%E5%BA%93-glide)
- [引用](#%E5%BC%95%E7%94%A8)
- [Glide的基本用法](#glide%E7%9A%84%E5%9F%BA%E6%9C%AC%E7%94%A8%E6%B3%95)
  - [简介](#%E7%AE%80%E4%BB%8B)
  - [加载图片](#%E5%8A%A0%E8%BD%BD%E5%9B%BE%E7%89%87)
    - [解析](#%E8%A7%A3%E6%9E%90)
      - [with](#with)
      - [load](#load)
      - [into](#into)
  - [占位图](#%E5%8D%A0%E4%BD%8D%E5%9B%BE)
      - [placeholder](#placeholder)
      - [diskCacheStrategy](#diskcachestrategy)
      - [error](#error)
  - [指定图片格式](#%E6%8C%87%E5%AE%9A%E5%9B%BE%E7%89%87%E6%A0%BC%E5%BC%8F)
      - [asBitmap](#asbitmap)
      - [asGif](#asgif)
  - [指定图片大小](#%E6%8C%87%E5%AE%9A%E5%9B%BE%E7%89%87%E5%A4%A7%E5%B0%8F)
      - [override](#override)
- [从源码的角度理解Glide的执行流程](#%E4%BB%8E%E6%BA%90%E7%A0%81%E7%9A%84%E8%A7%92%E5%BA%A6%E7%90%86%E8%A7%A3glide%E7%9A%84%E6%89%A7%E8%A1%8C%E6%B5%81%E7%A8%8B)
  - [如何阅读源码](#%E5%A6%82%E4%BD%95%E9%98%85%E8%AF%BB%E6%BA%90%E7%A0%81)
  - [源码下载](#%E6%BA%90%E7%A0%81%E4%B8%8B%E8%BD%BD)
  - [开始阅读](#%E5%BC%80%E5%A7%8B%E9%98%85%E8%AF%BB)
  - [1. Glide.with(..)](#1-glidewith)
    - [1.1 RequestManagerRetriever.get()](#11-requestmanagerretrieverget)
    - [1.2 requestManagerRetriever.get(..)](#12-requestmanagerretrieverget)
  - [2. Glide.with(..).load(..)](#2-glidewithload)
    - [2.1 requestManager.load(..)](#21-requestmanagerload)
      - [2.1.1 fromString()](#211-fromstring)
      - [2.1.2 loadGeneric(Class< T >)](#212-loadgenericclass-t-)
      - [2.1.3 DrawableTypeRequest](#213-drawabletyperequest)
    - [2.2 drawableTypeRequest.load(..)](#22-drawabletyperequestload)
      - [2.2.1 drawableRequestBuilder.load(..)](#221-drawablerequestbuilderload)
      - [2.2.2 genericRequestBuilder.load(..)](#222-genericrequestbuilderload)
      - [DrawableRequestBuilder其他方法](#drawablerequestbuilder%E5%85%B6%E4%BB%96%E6%96%B9%E6%B3%95)
    - [load调用图解](#load%E8%B0%83%E7%94%A8%E5%9B%BE%E8%A7%A3)
  - [3. Glide.with(…).load(…).into(ImageView)](#3-glidewithloadintoimageview)
    - [3.1 drawableTypeRequest.into(ImageView)](#31-drawabletyperequestintoimageview)
      - [3.1.1 genericRequestBuilder.into(ImageView)](#311-genericrequestbuilderintoimageview)
    - [3.2 glide.buildImageViewTarget(..)](#32-glidebuildimageviewtarget)
      - [3.2.1 imageViewTargetFactory.buildTarget(..)](#321-imageviewtargetfactorybuildtarget)
    - [3.3 genericRequestBuilder.into(Y target)](#33-genericrequestbuilderintoy-target)
      - [3.3.1 buildRequest(Target< TranscodeType>)](#331-buildrequesttarget-transcodetype)
        - [3.3.1.1 GenericRequest.obtain(.)](#3311-genericrequestobtain)
      - [3.3.2   requestTracker.runRequest(Request)](#332---requesttrackerrunrequestrequest)
      - [3.3.2.1 genericRequest.begin()](#3321-genericrequestbegin)
        - [图片URL地址传递图解](#%E5%9B%BE%E7%89%87url%E5%9C%B0%E5%9D%80%E4%BC%A0%E9%80%92%E5%9B%BE%E8%A7%A3)
        - [3.3.2.1.1 onException()](#33211-onexception)
          - [3.3.2.1.1.1 setErrorPlaceholder()](#332111-seterrorplaceholder)
          - [3.3.2.1.1.2 imageViewTarget.onLoadFailed()](#332112-imageviewtargetonloadfailed)
        - [图片加载](#%E5%9B%BE%E7%89%87%E5%8A%A0%E8%BD%BD)
        - [3.3.2.1.2 onSizeReady(int width, int height)](#33212-onsizereadyint-width-int-height)
          - [DrawableTypeRequest构造函数](#drawabletyperequest%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0)
          - [loadProvider来源图解](#loadprovider%E6%9D%A5%E6%BA%90%E5%9B%BE%E8%A7%A3)
          - [3.3.2.1.2.1 imageVideoModelLoader.getResourceFetcher(.)](#332121-imagevideomodelloadergetresourcefetcher)
          - [3.3.2.1.2.2 engine.load(.)](#332122-engineload)
- [引用](#%E5%BC%95%E7%94%A8-1)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# 【Android 库 Glide】

# 引用
 [Android图片加载框架最全解析（一），Glide的基本用法](http://blog.csdn.net/guolin_blog/article/details/53759439)
 [Android图片加载框架最全解析（二），从源码的角度理解Glide的执行流程](http://blog.csdn.net/guolin_blog/article/details/53939176)
 [Android图片加载框架最全解析（三），深入探究Glide的缓存机制](http://blog.csdn.net/guolin_blog/article/details/54895665)
[Android图片加载框架最全解析（四），玩转Glide的回调与监听](http://blog.csdn.net/guolin_blog/article/details/70215985)
 [Android图片加载框架最全解析（五），Glide强大的图片变换功能](http://blog.csdn.net/guolin_blog/article/details/71524668)
 [Android图片加载框架最全解析（六），探究Glide的自定义模块功能](http://blog.csdn.net/guolin_blog/article/details/78179422)
[Android图片加载框架最全解析（七），实现带进度的Glide图片加载功能](http://blog.csdn.net/guolin_blog/article/details/78357251)
[Android图片加载框架最全解析（八），带你全面了解Glide 4的用法](http://blog.csdn.net/guolin_blog/article/details/78582548)

现在Android上的图片加载框架非常成熟，从最早的老牌图片加载框架UniversalImageLoader，到后来Google推出的Volley，再到后来的新兴军Glide和Picasso，当然还有Facebook的Fresco。每一个都非常稳定，功能也都十分强大。但是它们的使用场景基本都是重合的，也就是说我们基本只需要选择其中一个来进行学习和使用就足够了，每一个框架都尝试去掌握的话则有些浪费时间。

从易用性上来讲，Glide和Picasso应该都是完胜其他框架的，这两个框架都实在是太简单好用了，大多数情况下加载图片都是一行代码就能解决的，而UniversalImageLoader和Fresco则在这方面略逊一些。

那么再拿Glide和Picasso对比呢，首先这两个框架的用法非常相似，但其实它们各有特色。Picasso比Glide更加简洁和轻量，Glide比Picasso功能更为丰富。之前已经有人对这两个框架进行过全方面的对比，大家如果想了解更多的话可以去参考一下 [Google推荐的图片加载库Glide介绍](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0327/2650.html) 。

# Glide的基本用法

## 简介

Glide是一款由Bump Technologies开发的图片加载框架，使得我们可以在Android平台上以极度简单的方式加载和展示图片。

本文寄语Glide 3.7.0版本来进行讲解。

要想使用Glide，首先需要将这个库引入到我们的项目当中。新建一个GlideTest项目，然后在app/build.gradle文件当中添加如下依赖：

```java
dependencies {
    compile 'com.github.bumptech.glide:glide:3.7.0'
}
```

如果你还在使用Eclipse，可以点击 [这里](http://download.csdn.net/detail/sinyu890807/9781538) 下载Glide的jar包。

另外，Glide中需要用到网络功能，因此你还得在AndroidManifest.xml中声明一下网络权限才行：

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

就是这么简单，然后我们就可以自由地使用Glide中的任意功能了。

## 加载图片

现在我们就来尝试一下如何使用Glide来加载图片吧：

```java
String url = "http://cn.bing.com/az/hprichbg/rb/Dongdaemun_ZH-CN10736487148_1920x1080.jpg";
Glide.with(this).load(url).into(imageView);
```

没错，就是这么简单。现在我们来运行一下程序，效果如下图所示：
[![](https://upload-images.jianshu.io/upload_images/9028834-5ff3c4df2889eedb.gif?imageMogr2/auto-orient/strip)](https://upload-images.jianshu.io/upload_images/9028834-5ff3c4df2889eedb.gif?imageMogr2/auto-orient/strip)

核心的代码就只有这一行而已：

```java
Glide.with(this).load(url).into(imageView);
```

这一行代码，可以**加载网络**上的**图片**、加载手机**本地**的**图片**、加载应用**资源中**的**图片**等等。

### 解析
下面我们就来详细解析一下这行代码。

#### with
首先，调用Glide.with()方法用于**创建一个加载图片的实例**。
with()方法可以**接收Context、Activity或者Fragment**类型的参数。也就是说我们选择的范围非常广，不管是在Activity还是Fragment中调用with()方法，都可以直接传this。
那如果调用的地方既不在Activity中也不在Fragment中呢？也没关系，我们可以获取当前应用程序的ApplicationContext，传入到with()方法当中。
注意with()方法中**传入的实例会决定Glide加载图片的生命周期**，如果传入的是Activity或者Fragment的实例，那么当这个**Activity或Fragment被销毁**的时候，**图片加载**也会**停止**。如果传入的是**ApplicationContext**，那么只有**当应用程序被杀掉**的时候，图片加载**才**会**停止**。

#### load
接下来看一下load()方法，这个方法用于**指定待加载的图片资源**。
Glide支持加载各种各样的图片资源，包括**网络**图片、**本地**图片、**应用资源**、**二进制流**、**Uri对象**等等。
因此load()方法也有很多个方法重载，除了我们刚才使用的加载一个字符串网址之外，你还可以这样使用load()方法：

```java
// 加载本地图片
File file = new File(getExternalCacheDir() + "/image.jpg");
Glide.with(this).load(file).into(imageView);

// 加载应用资源
int resource = R.drawable.image;
Glide.with(this).load(resource).into(imageView);

// 加载二进制流
byte[] image = getImageBytes();
Glide.with(this).load(image).into(imageView);

// 加载Uri对象
Uri imageUri = getImageUri();
Glide.with(this).load(imageUri).into(imageView);
```
#### into
最后看一下into()方法，这个方法就很简单了，我们希望让图片显示在哪个ImageView上，把这个ImageView的实例传进去就可以了。
当然，into()方法不仅仅是只能接收ImageView类型的参数，还支持很多更丰富的用法，不过那个属于高级技巧，我们会在后面的文章当中学习。

那么回顾一下Glide最基本的使用方式，其实就是关键的三步走：先with()，再load()，最后into()。熟记这三步，你就已经入门Glide了。

## 占位图

现在我们来学一些Glide的扩展内容。其实刚才所学的三步走就是Glide最核心的东西，而我们后面所要学习的所有东西都是在这个三步走的基础上不断进行扩展而已。

观察刚才加载网络图片的效果，你会发现，点击了Load Image按钮之后，要稍微等一会图片才会显示出来。这其实很容易理解，因为从网络上下载图片本来就是需要时间的。那么我们有没有办法再优化一下用户体验呢？当然可以，Glide提供了各种各样非常丰富的API支持，其中就包括了占位图功能。

顾名思义，占位图就是指在图片的加载过程中，我们先显示一张临时的图片，等图片加载出来了再替换成要加载的图片。
#### placeholder
下面我们就来学习一下Glide占位图功能的使用方法，首先我事先准备好了一张loading.jpg图片，用来作为占位图显示。然后修改Glide加载部分的代码，如下所示：

```java
Glide.with(this)
     .load(url)
     .placeholder(R.drawable.loading)
     .into(imageView);
```

没错，就是这么简单。我们只是在刚才的三步走之间插入了一个placeholder()方法，然后将占位图片的资源id传入到这个方法中即可。
另外，这个占位图的用法其实也演示了**Glide当中绝大多数API的用法**，其实就是**在load()和into()方法之间串接**任意想添加的功能就可以了。

#### diskCacheStrategy
不过如果你现在重新运行一下代码并点击Load Image，很可能是根本看不到占位图效果的。因为Glide有非常强大的缓存机制，我们刚才加载那张必应美图的时候Glide自动就已经将它缓存下来了，下次加载的时候将会直接从缓存中读取，不会再去网络下载了，因而加载的速度非常快，所以占位图可能根本来不及显示。

因此这里我们还需要稍微做一点修改，来让占位图能有机会显示出来，修改代码如下所示：

```java
Glide.with(this)
     .load(url)
     .placeholder(R.drawable.loading)
     .diskCacheStrategy(DiskCacheStrategy.NONE)
     .into(imageView);
```

可以看到，这里串接了一个diskCacheStrategy()方法，并传入DiskCacheStrategy.NONE参数，这样就可以禁用掉Glide的缓存功能。

关于Glide缓存方面的内容我们将会在后面的文章进行详细的讲解，这里只是为了测试占位图功能而加的一个额外配置，暂时你只需要知道禁用缓存必须这么写就可以了。

现在重新运行一下代码，效果如下图所示：
[![](https://upload-images.jianshu.io/upload_images/9028834-99c5707725b51853.gif?imageMogr2/auto-orient/strip)](https://upload-images.jianshu.io/upload_images/9028834-99c5707725b51853.gif?imageMogr2/auto-orient/strip)

可以看到，当点击Load Image按钮之后会立即显示一张占位图，然后等真正的图片加载完成之后会将占位图替换掉。

#### error
当然，这只是占位图的一种，除了这种加载占位图之外，还有一种异常占位图。**异常占位图**就是指，如果因为某些异常情况导致图片加载失败，比如说手机网络信号不好，这个时候就显示这张异常占位图。

异常占位图的用法相信你已经可以猜到了，首先准备一张error.jpg图片，然后修改Glide加载部分的代码，如下所示：

```java
Glide.with(this)
     .load(url)
     .placeholder(R.drawable.loading)
     .error(R.drawable.error)
     .diskCacheStrategy(DiskCacheStrategy.NONE)
     .into(imageView);
```

很简单，这里又串接了一个error()方法就可以指定异常占位图了。

现在你可以将图片的url地址修改成一个不存在的图片地址，或者干脆直接将手机的网络给关了，然后重新运行程序，效果如下图所示：

[![](https://upload-images.jianshu.io/upload_images/9028834-9528892e031741e8.gif?imageMogr2/auto-orient/strip)](https://upload-images.jianshu.io/upload_images/9028834-9528892e031741e8.gif?imageMogr2/auto-orient/strip)

这样我们就把Glide提供的占位图功能都掌握了。

## 指定图片格式

我们还需要再了解一下Glide另外一个强大的功能，那就是**Glide**是**支持加载GIF**图片的。
这一点确实非常牛逼，因为相比之下Jake Warton曾经明确表示过，**Picasso**是**不**会**支持加载GIF**图片的。

而使用Glide加载GIF图并不需要编写什么额外的代码，Glide内部会自动判断图片格式。比如这是一张GIF图片的URL地址：

```xml
http://p1.pstatp.com/large/166200019850062839d3
```

我们只需要将刚才那段加载图片代码中的URL地址替换成上面的地址就可以了，现在重新运行一下代码，效果如下图所示：
[![](https://upload-images.jianshu.io/upload_images/9028834-1c8290a0421a78a0.gif?imageMogr2/auto-orient/strip)](https://upload-images.jianshu.io/upload_images/9028834-1c8290a0421a78a0.gif?imageMogr2/auto-orient/strip)

不管我们传入的是一张普通图片，还是一张GIF图片，**Glide**都**会自动进行判断**，并且可以正确地把它解析并展示出来。

#### asBitmap
但是如果我想指定图片的格式该怎么办呢？就比如说，我希望加载的这张图必须是一张静态图片，我不需要Glide自动帮我判断它到底是静图还是GIF图。

想实现这个功能仍然非常简单，我们只需要再串接一个新的方法就可以了，如下所示：

```java
Glide.with(this)
     .load(url)
     .asBitmap()
     .placeholder(R.drawable.loading)
     .error(R.drawable.error)
     .diskCacheStrategy(DiskCacheStrategy.NONE)
     .into(imageView);
```

可以看到，这里在load()方法的后面加入了一个asBitmap()方法，这个方法的意思就是说这里只允许加载静态图片，不需要Glide去帮我们自动进行图片格式的判断了。

现在重新运行一下程序，效果如下图所示：

[![4.gif](https://upload-images.jianshu.io/upload_images/9028834-8048d1578891f81f.gif?imageMogr2/auto-orient/strip)](https://upload-images.jianshu.io/upload_images/9028834-8048d1578891f81f.gif?imageMogr2/auto-orient/strip)

由于调用了asBitmap()方法，现在GIF图就无法正常播放了，而是会在界面上**显示第一帧**的图片。

#### asGif
那么类似地，既然我们能强制指定加载静态图片，就也能强制指定加载动态图片。比如说我们想要实现必须加载动态图片的功能，就可以这样写：

```java
Glide.with(this)
     .load(url)
     .asGif()
     .placeholder(R.drawable.loading)
     .error(R.drawable.error)
     .diskCacheStrategy(DiskCacheStrategy.NONE)
     .into(imageView);
```

这里调用了asGif()方法替代了asBitmap()方法，很好理解，相信不用我多做什么解释了。

那么既然指定了只允许加载动态图片，如果我们传入了一张静态图片的URL地址又会怎么样呢？试一下就知道了，将图片的URL地址改成刚才的必应美图，然后重新运行代码，效果如下图所示。

[![5.gif](https://upload-images.jianshu.io/upload_images/9028834-ba594b935152d646.gif?imageMogr2/auto-orient/strip)](https://upload-images.jianshu.io/upload_images/9028834-ba594b935152d646.gif?imageMogr2/auto-orient/strip)

没错，如果**指定**了只能加载**动态**图片，而**传入**的图片却是一张**静图**的话，那么结果自然就只有**加载失败**喽。

## 指定图片大小

实际上，使用Glide在绝大多数情况下我们都是不需要指定图片大小的。

在学习本节内容之前，你可能还需要先了解一个概念，就是我们平时在加载图片的时候很容易会造成**内存浪费**。什么叫内存浪费呢？比如说一张图片的尺寸是1000*1000像素，但是我们界面上的ImageView可能只有200*200像素，这个时候如果你不对图片进行任何压缩就直接读取到内存中，这就属于内存浪费了，因为程序中根本就用不到这么高像素的图片。

关于图片压缩这方面，我之前也翻译过Android官方的一篇文章，感兴趣的朋友可以去阅读一下 [Android高效加载大图、多图解决方案，有效避免程序OOM](http://blog.csdn.net/guolin_blog/article/details/9316683) 。

而使用**Glide**，我们就完全**不用担心**图片**内存浪费**，甚至是内存溢出的问题。因为Glide从来都不会直接将图片的完整尺寸全部加载到内存中，而是**用多少加载多少**。Glide会**自动判断ImageView的大小**，然后**只将这么大的图片像素加载到内存当中**，帮助我们节省内存开支。

当然，Glide也并没有使用什么神奇的魔法，它内部的实现原理其实就是上面那篇文章当中介绍的技术，因此掌握了最基本的实现原理，你也可以自己实现一套这样的图片压缩机制。

也正是因为Glide是如此的智能，所以刚才在开始的时候我就说了，在绝大多数情况下我们都是不需要指定图片大小的，因为Glide会自动根据ImageView的大小来决定图片的大小。
#### override
不过，如果你真的有这样的需求，必须给图片指定一个固定的大小，Glide仍然是支持这个功能的。修改Glide加载部分的代码，如下所示：

```java
Glide.with(this)
     .load(url)
     .placeholder(R.drawable.loading)
     .error(R.drawable.error)
     .diskCacheStrategy(DiskCacheStrategy.NONE)
     .override(100, 100)
     .into(imageView);
```

仍然非常简单，这里使用override()方法指定了一个图片的尺寸，也就是说，Glide现在只会将图片加载成100*100像素的尺寸，而不会管你的ImageView的大小是多少了。

# 从源码的角度理解Glide的执行流程

在多数情况下，我们想要在界面上加载并展示一张图片只需要一行代码就能实现，如下所示：

```java
Glide.with(this).load(url).into(imageView);
```

虽说只有这简简单单的一行代码，但Glide在背后帮我们默默执行了成吨的工作。

## 如何阅读源码

那么阅读源码到底困难吗？这个当然主要还是要视具体的源码而定。比如同样是图片加载框架，我读Volley的源码时就感觉酣畅淋漓，并且对Volley的架构设计和代码质量深感佩服。读Glide的源码时却让我相当痛苦，代码极其难懂。当然这里我并不是说Glide的代码写得不好，只是因为Glide的复杂程度和Volley完全不是在一个量级上的。

我平时阅读源码时所使用的技巧：抽丝剥茧、点到即止。应该**认准一个功能点**，然后去分析这个功能点是如何实现的。但只要去**追寻主体的实现逻辑**即可，千万不要试图去搞懂每一行代码都是什么意思，那样很容易会陷入到思维黑洞当中，而且越陷越深。因为这些庞大的系统都不是由一个人写出来的，每一行代码都想搞明白，就会感觉自己是在盲人摸象，永远也研究不透。如果只是去分析主体的实现逻辑，那么就有比较明确的目的性，这样阅读源码会更加轻松，也更加有成效。

而今天带大家阅读的Glide源码就非常适合使用这个技巧，因为Glide的源码太复杂了，千万不要试图去搞明白它每行代码的作用，而是应该只分析它的主体实现逻辑。那么我们本篇文章就先确立好一个目标，就是要通过阅读源码搞明白下面这行代码：

```java
Glide.with(this).load(url).into(imageView);
```

到底是如何实现将一张网络图片展示到ImageView上面的。先将Glide的一整套图片加载机制的基本流程梳理清楚，然后我们再通过后面的几篇文章具体去了解Glide源码方方面面的细节。

## 源码下载

既然是要阅读Glide的源码，那么我们自然需要先将Glide的源码下载下来。其实如果你是使用在build.gradle中添加依赖的方式将Glide引入到项目中的，那么源码自动就已经下载下来了，在Android Studio中就可以直接进行查看。

不过，使用添加依赖的方式引入的Glide，我们只能看到它的源码，但不能做任何的修改，如果你还需要修改它的源码的话，可以到GitHub上面将它的完整源码下载下来。

Glide的GitHub主页的地址是：<https://github.com/bumptech/glide>

不过在这个地址下载到的永远都是最新的源码，有可能还正在处于开发当中。而我们整个系列都是使用Glide 3.7.0这个版本来进行讲解的，因此如果你需要专门去下载3.7.0版本的源码，可以到这个地址进行下载：<https://github.com/bumptech/glide/tree/v3.7.0>
## 开始阅读

我们在上一篇文章中已经学习过了，Glide最基本的用法就是三步走：先with()，再load()，最后into()。那么我们开始一步步阅读这三步走的源码，先从with()看起。

## 1. Glide.with(..)

with()方法是Glide类中的一组静态方法，它有好几个方法重载，我们来看一下Glide类中所有with()方法的方法重载：

```java
public class Glide {
/* Glide 源码 */
    ...
    public static RequestManager with(Context context) {
        RequestManagerRetriever retriever = RequestManagerRetriever.get();
        return retriever.get(context);
    }
    public static RequestManager with(Activity activity) {
        RequestManagerRetriever retriever = RequestManagerRetriever.get();
        return retriever.get(activity);
    }
    public static RequestManager with(FragmentActivity activity) {
        RequestManagerRetriever retriever = RequestManagerRetriever.get();
        return retriever.get(activity);
    }
    @TargetApi(Build.VERSION_CODES.HONEYCOMB)
    public static RequestManager with(android.app.Fragment fragment) {
        RequestManagerRetriever retriever = RequestManagerRetriever.get();
        return retriever.get(fragment);
    }
    public static RequestManager with(Fragment fragment) {
        RequestManagerRetriever retriever = RequestManagerRetriever.get();
        return retriever.get(fragment);//分析见1.1
    }
}
```
### 1.1 RequestManagerRetriever.get()
可以看到，with()方法的重载种类非常多，既可以传入Activity，也可以传入Fragment或者是Context。
每一个with()方法重载的代码都非常简单，都是先调用**RequestManagerRetriever**的静态**get**()方法**得到**一个**RequestManagerRetriever对象**，这个静态get()方法就是一个单例实现，没什么需要解释的。

```java
/* RequestManagerRetriever 源码 */
public class RequestManagerRetriever implements Handler.Callback {
    /** The singleton instance of RequestManagerRetriever. */
    private static final RequestManagerRetriever INSTANCE = new RequestManagerRetriever();
    public static RequestManagerRetriever get() {
        return INSTANCE;
    }
}
```

然后再**调用RequestManagerRetriever**的实例**get**()方法，去**获取RequestManager对象**。

### 1.2 requestManagerRetriever.get(..)
而RequestManagerRetriever的实例get()方法中的逻辑是什么样的呢？我们一起来看一看：

```java
/* RequestManagerRetriever 源码 */
public class RequestManagerRetriever implements Handler.Callback {
    private volatile RequestManager applicationManager;
    ...
    //传入Application参数
    public RequestManager get(Context context) {
        if (context == null) {
            throw new IllegalArgumentException("You cannot start a load on a null Context");
        } else if (Util.isOnMainThread() && !(context instanceof Application)) {
            if (context instanceof FragmentActivity) {
                return get((FragmentActivity) context);
            } else if (context instanceof Activity) {
                return get((Activity) context);
            } else if (context instanceof ContextWrapper) {
                return get(((ContextWrapper) context).getBaseContext());
            }
        }
        return getApplicationManager(context);//【①】 getApplicationManager
    }
    private RequestManager getApplicationManager(Context context) {//getApplicationManager
        // Either an application context or we're on a background thread.
        if (applicationManager == null) {
            synchronized (this) {
                if (applicationManager == null) {
                    // Normally pause/resume is taken care of by the fragment we add to the fragment or activity.
                    // However, in this case since the manager attached to the application will not receive lifecycle
                    // events, we must force the manager to start resumed using ApplicationLifecycle.
                    applicationManager = new RequestManager(context.getApplicationContext(),
                            new ApplicationLifecycle(), new EmptyRequestManagerTreeNode());
                }
            }
        }
        return applicationManager;
    }

    //传入非Application参数
    public RequestManager get(FragmentActivity activity) {
        if (Util.isOnBackgroundThread()) {//【④】
            return get(activity.getApplicationContext());
        } else {
            assertNotDestroyed(activity);
            FragmentManager fm = activity.getSupportFragmentManager();
            return supportFragmentGet(activity, fm);//supportFragmentGet
        }
    }
    public RequestManager get(Fragment fragment) {
        if (fragment.getActivity() == null) {
            throw new IllegalArgumentException("You cannot start a load on a fragment before it is attached");
        }
        if (Util.isOnBackgroundThread()) {
            return get(fragment.getActivity().getApplicationContext());
        } else {
            FragmentManager fm = fragment.getChildFragmentManager();
            return supportFragmentGet(fragment.getActivity(), fm);//supportFragmentGet
        }
    }
    RequestManager supportFragmentGet(Context context, FragmentManager fm) {//supportFragmentGet
        SupportRequestManagerFragment current = getSupportRequestManagerFragment(fm);//【③】
        RequestManager requestManager = current.getRequestManager();
        if (requestManager == null) {
            requestManager = new RequestManager(context, current.getLifecycle(), current.getRequestManagerTreeNode());
            current.setRequestManager(requestManager);
        }
        return requestManager;
    }
    SupportRequestManagerFragment getSupportRequestManagerFragment(final FragmentManager fm) {
        SupportRequestManagerFragment current = (SupportRequestManagerFragment) fm.findFragmentByTag(FRAGMENT_TAG);
        if (current == null) {
            current = pendingSupportRequestManagerFragments.get(fm);
            if (current == null) {
                current = new SupportRequestManagerFragment();
                pendingSupportRequestManagerFragments.put(fm, current);
                fm.beginTransaction().add(current, FRAGMENT_TAG).commitAllowingStateLoss();
                handler.obtainMessage(ID_REMOVE_SUPPORT_FRAGMENT_MANAGER, fm).sendToTarget();
            }
        }
        return current;
    }

    @TargetApi(Build.VERSION_CODES.HONEYCOMB)
    public RequestManager get(Activity activity) {
        if (Util.isOnBackgroundThread() || Build.VERSION.SDK_INT < Build.VERSION_CODES.HONEYCOMB) {
            return get(activity.getApplicationContext());
        } else {
            assertNotDestroyed(activity);//assertNotDestroyed
            android.app.FragmentManager fm = activity.getFragmentManager();
            return fragmentGet(activity, fm);//fragmentGet
        }
    }

    @TargetApi(Build.VERSION_CODES.JELLY_BEAN_MR1)
    private static void assertNotDestroyed(Activity activity) {//assertNotDestroyed
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.JELLY_BEAN_MR1 && activity.isDestroyed()) {
            throw new IllegalArgumentException("You cannot start a load for a destroyed activity");
        }
    }

    @TargetApi(Build.VERSION_CODES.JELLY_BEAN_MR1)
    public RequestManager get(android.app.Fragment fragment) {
        if (fragment.getActivity() == null) {
            throw new IllegalArgumentException("You cannot start a load on a fragment before it is attached");
        }
        if (Util.isOnBackgroundThread() || Build.VERSION.SDK_INT < Build.VERSION_CODES.JELLY_BEAN_MR1) {
            return get(fragment.getActivity().getApplicationContext());
        } else {
            android.app.FragmentManager fm = fragment.getChildFragmentManager();
            return fragmentGet(fragment.getActivity(), fm);//fragmentGet
        }
    }
    @TargetApi(Build.VERSION_CODES.HONEYCOMB)
    RequestManager fragmentGet(Context context, android.app.FragmentManager fm) {//fragmentGet
        RequestManagerFragment current = getRequestManagerFragment(fm);//【②】 getRequestManagerFragment
        RequestManager requestManager = current.getRequestManager();
        if (requestManager == null) {
            requestManager = new RequestManager(context, current.getLifecycle(), current.getRequestManagerTreeNode());
            current.setRequestManager(requestManager);
        }
        return requestManager;
    }

    @TargetApi(Build.VERSION_CODES.JELLY_BEAN_MR1)
    RequestManagerFragment getRequestManagerFragment(final android.app.FragmentManager fm) {
        RequestManagerFragment current = (RequestManagerFragment) fm.findFragmentByTag(FRAGMENT_TAG);
        if (current == null) {
            current = pendingRequestManagerFragments.get(fm);
            if (current == null) {
                current = new RequestManagerFragment();
                pendingRequestManagerFragments.put(fm, current);
                fm.beginTransaction().add(current, FRAGMENT_TAG).commitAllowingStateLoss();
                handler.obtainMessage(ID_REMOVE_FRAGMENT_MANAGER, fm).sendToTarget();
            }
        }
        return current;
    }
    ...
}
```

RequestManagerRetriever类中看似有很多个get()方法的重载，什么Context参数，Activity参数，Fragment参数等等，实际上只有**两种情况**而已，即**传入Application类型的参数**，和传入**非Application**类型的参数。

- 我们先来看**传入Application参数**的情况。
  如果在Glide.with()方法中传入的是一个Application对象，那么这里就会调用带有Context参数的get()方法重载，然后执行(【①】)`return getApplicationManager(context);`来获取一个RequestManager对象。
  其实这是最简单的一种情况，因为Application对象的生命周期即应用程序的生命周期，因此Glide并不需要做什么特殊的处理，它自动就是**和应用程序的生命周期**是**同步**的，如果应用程序关闭的话，Glide的加载也会同时终止。
   .
- 接下来我们看**传入非Application参数**的情况。
  不管你在Glide.with()方法中**传入**的是**Activity**、**FragmentActivity**、v4包下的**Fragment**、还是app包下的Fragment，最终的流程都是一样的，那就是会**向当前的Activity当中添加一个隐藏的Fragment**。具体添加的逻辑是在上述代码的【②】`RequestManagerFragment current = getRequestManagerFragment(fm);`和【③】`SupportRequestManagerFragment current = getSupportRequestManagerFragment(fm);`，分别对应的app包和v4包下的两种Fragment的情况。
  那么这里为什么要添加一个隐藏的Fragment呢？因为Glide需要知道加载的生命周期。很简单的一个道理，如果你在某个Activity上正在加载着一张图片，结果图片还没加载出来，Activity就被用户关掉了，那么图片还应该继续加载吗？当然不应该。可是Glide并没有办法知道Activity的生命周期，于是Glide就使用了添加隐藏Fragment的这种小技巧，因为**Fragment的生命周期和Activity是同步的**，如果**Activity**被**销毁**了，Fragment是可以监听到的，这样**Glide**就**可以捕获**这个事件并**停止图片加载**了。

这里额外再提一句，从【④】`if (Util.isOnBackgroundThread()) {`代码可以看出，如果我们是在**非主线程**当中**使用**的Glide，那么不管你是传入的Activity还是Fragment，都会**被强制当成Application来处理**。不过其实这就属于是在分析代码的细节了，本篇文章我们将会把目光主要放在Glide的主线工作流程上面，后面不会过多去分析这些细节方面的内容。

总体来说，第一个**with**()方法的源码还是比较好理解的。其实就是**为了得到一个RequestManager对象**而已，然后**Glide**会**根据**我们**传入with**()方法的**参数**来**确定图片加载的生命周期**，并没有什么特别复杂的逻辑。
不过复杂的逻辑还在后面等着我们呢，接下来我们开始分析第二步，load()方法。

## 2. Glide.with(..).load(..)

### 2.1 requestManager.load(..)
由于**with**()方法**返回**的是一个**RequestManager对象**，那么很容易就能想到，load()方法是在RequestManager类当中的，所以说我们首先要看的就是RequestManager这个类。
不过在上一篇文章中我们学过，Glide是支持图片URL字符串、图片本地路径等等加载形式的，因此RequestManager中也有很多个load()方法的重载。但是这里我们不可能把每个load()方法的重载都看一遍，因此我们就只选其中一个加载图片URL字符串的load()方法来进行研究吧。

RequestManager类的简化代码如下所示：

```java
/* RequestManager 源码 */
public class RequestManager implements LifecycleListener {
    public DrawableTypeRequest<String> load(String string) {
        return (DrawableTypeRequest<String>) fromString().load(string);
    }
    public DrawableTypeRequest<String> fromString() {
        return loadGeneric(String.class);
    }
    private <T> DrawableTypeRequest<T> loadGeneric(Class<T> modelClass) {
        ModelLoader<T, InputStream> streamModelLoader = Glide.buildStreamModelLoader(modelClass, context);
        ModelLoader<T, ParcelFileDescriptor> fileDescriptorModelLoader =
                Glide.buildFileDescriptorModelLoader(modelClass, context);
        if (modelClass != null && streamModelLoader == null && fileDescriptorModelLoader == null) {
            throw new IllegalArgumentException("Unknown type " + modelClass + ". You must provide a Model of a type for"
                    + " which there is a registered ModelLoader, if you are using a custom model, you must first call"
                    + " Glide#register with a ModelLoaderFactory for your custom model class");
        }
        return optionsApplier.apply(
                new DrawableTypeRequest<T>(modelClass, streamModelLoader, fileDescriptorModelLoader, context,
                        glide, requestTracker, lifecycle, optionsApplier));
    }
    ...
}
```

在我们只探究加载图片URL字符串这一个load()方法的情况下，那么比较重要的方法就只剩下上述代码中的这三个方法。

#### 2.1.1 fromString()
```java
/* RequestManager 源码 */
public DrawableTypeRequest<String> load(String string) {
    return (DrawableTypeRequest<String>) fromString().load(string);
}
public DrawableTypeRequest<String> fromString() {
    return loadGeneric(String.class);
}
```

那么我们先来看load()方法，这个方法中的逻辑是非常简单的，只有一行代码，就是先调用了fromString()方法，再调用load()方法，然后把传入的图片URL地址传进去。
而fromString()方法也极为简单，就是调用了loadGeneric()方法，并且指定参数为String.class，因为load()方法传入的是一个字符串参数。
那么看上去，好像主要的工作都是在loadGeneric()方法中进行的了。

#### 2.1.2 loadGeneric(Class< T >)

```java
/* RequestManager 源码 */
private <T> DrawableTypeRequest<T> loadGeneric(Class<T> modelClass) {
    ModelLoader<T, InputStream> streamModelLoader = Glide.buildStreamModelLoader(modelClass, context);
    ModelLoader<T, ParcelFileDescriptor> fileDescriptorModelLoader =
            Glide.buildFileDescriptorModelLoader(modelClass, context);
    if (modelClass != null && streamModelLoader == null && fileDescriptorModelLoader == null) {
        throw new IllegalArgumentException("Unknown type " + modelClass + ". You must provide a Model of a type for"
                + " which there is a registered ModelLoader, if you are using a custom model, you must first call"
                + " Glide#register with a ModelLoaderFactory for your custom model class");
    }
    return optionsApplier.apply(
            new DrawableTypeRequest<T>(modelClass, streamModelLoader, fileDescriptorModelLoader, context,
                    glide, requestTracker, lifecycle, optionsApplier));
}
```

其实loadGeneric()方法也没几行代码，这里分别调用了`Glide.buildStreamModelLoader()`方法和`Glide.buildFileDescriptorModelLoader()`方法来**获得ModelLoader对象**。
**`ModelLoader`**对象是用于**加载图片**的，而我们给load()方法传入不同类型的参数，这里也会得到不同的ModelLoader对象。不过buildStreamModelLoader()方法内部的逻辑还是蛮复杂的，这里就不展开介绍了。
由于我们刚才**传入**的参数是**String.class**，因此最终**得到**的是**StreamStringLoader**对象，它是实现了ModelLoader接口的。

最后我们可以看到，**loadGeneric**()方法是要**返回**一个**DrawableTypeRequest**对象的，因此在loadGeneric()方法的最后又去new了一个DrawableTypeRequest对象，然后把刚才获得的ModelLoader对象，还有一大堆杂七杂八的东西都传了进去。具体每个参数的含义和作用就不解释了，我们只看主线流程。

#### 2.1.3 DrawableTypeRequest
那么这个DrawableTypeRequest的作用是什么呢？我们来看下它的源码，如下所示：

```java
/* DrawableTypeRequest 源码 */
public class DrawableTypeRequest<ModelType> extends DrawableRequestBuilder<ModelType> implements DownloadOptions {
    private final ModelLoader<ModelType, InputStream> streamModelLoader;
    private final ModelLoader<ModelType, ParcelFileDescriptor> fileDescriptorModelLoader;
    private final RequestManager.OptionsApplier optionsApplier;

    //构造方法
    DrawableTypeRequest(Class<ModelType> modelClass, ModelLoader<ModelType, InputStream> streamModelLoader,
            ModelLoader<ModelType, ParcelFileDescriptor> fileDescriptorModelLoader, Context context, Glide glide,
            RequestTracker requestTracker, Lifecycle lifecycle, RequestManager.OptionsApplier optionsApplier) {
        super(context, modelClass,
                buildProvider(glide, streamModelLoader, fileDescriptorModelLoader, GifBitmapWrapper.class,
                        GlideDrawable.class, null),
                glide, requestTracker, lifecycle);
        this.streamModelLoader = streamModelLoader;
        this.fileDescriptorModelLoader = fileDescriptorModelLoader;
        this.optionsApplier = optionsApplier;
    }
    private static <A, Z, R> FixedLoadProvider<A, ImageVideoWrapper, Z, R> buildProvider(Glide glide,
            ModelLoader<A, InputStream> streamModelLoader,
            ModelLoader<A, ParcelFileDescriptor> fileDescriptorModelLoader, Class<Z> resourceClass,
            Class<R> transcodedClass,
            ResourceTranscoder<Z, R> transcoder) {
        if (streamModelLoader == null && fileDescriptorModelLoader == null) {
            return null;
        }

        if (transcoder == null) {
            transcoder = glide.buildTranscoder(resourceClass, transcodedClass);
        }
        DataLoadProvider<ImageVideoWrapper, Z> dataLoadProvider = glide.buildDataProvider(ImageVideoWrapper.class,
                resourceClass);
        ImageVideoModelLoader<A> modelLoader = new ImageVideoModelLoader<A>(streamModelLoader,
                fileDescriptorModelLoader);
        return new FixedLoadProvider<A, ImageVideoWrapper, Z, R>(modelLoader, transcoder, dataLoadProvider);
    }

    public BitmapTypeRequest<ModelType> asBitmap() {
        return optionsApplier.apply(new BitmapTypeRequest<ModelType>(this, streamModelLoader,
                fileDescriptorModelLoader, optionsApplier));
    }

    public GifTypeRequest<ModelType> asGif() {
        return optionsApplier.apply(new GifTypeRequest<ModelType>(this, streamModelLoader, optionsApplier));
    }
    ...
}
```

这个类中的代码本身就不多，我只是稍微做了一点简化。可以看到，最主要的就是它提供了asBitmap()和asGif()这两个方法。这两个方法我们在上一篇文章当中都是学过的，分别是用于强制指定加载静态图片和动态图片。而从源码中可以看出，它们分别又创建了一个BitmapTypeRequest和GifTypeRequest，如果没有进行强制指定的话，那默认就是使用DrawableTypeRequest。

### 2.2 drawableTypeRequest.load(..)
好的，那么我们再回到RequestManager的load()方法中【2.1 requestManager.load(..)】。
刚才已经分析过了，**fromString**()方法会**返回**一个**DrawableTypeRequest对象**，接下来会**调用**这个**DrawableTypeRequest**对象的**load**()方法，把图片的URL地址传进去。
#### 2.2.1 drawableRequestBuilder.load(..)
DrawableTypeRequest中并没有load()方法，其实是**调用**其父类**DrawableRequestBuilder**的**load**()方法：
```java
/* DrawableRequestBuilder 源码 */
public class DrawableRequestBuilder<ModelType>
        extends GenericRequestBuilder<ModelType, ImageVideoWrapper, GifBitmapWrapper, GlideDrawable>
        implements BitmapOptions, DrawableOptions {
    @Override
    public DrawableRequestBuilder<ModelType> load(ModelType model) {
        super.load(model);
        return this;
    }
```

#### 2.2.2 genericRequestBuilder.load(..)
由DrawableRequestBuilder源码可见，DrawableRequestBuilder的load()方法中调用了父类DrawableRequestBuilder的load()方法：

```java
/* GenericRequestBuilder 源码 */
public class GenericRequestBuilder<ModelType, DataType, ResourceType, TranscodeType> implements Cloneable {
    public GenericRequestBuilder<ModelType, DataType, ResourceType, TranscodeType> load(ModelType model) {
        this.model = model;
        isModelSet = true;
        return this;
    }
}
```

#### DrawableRequestBuilder其他方法
DrawableRequestBuilder中有很多个方法，这些方法其实就是Glide绝大多数的API了。里面有不少我们在上文中已经用过了，比如说placeholder()方法、error()方法、diskCacheStrategy()方法、override()方法等。当然还有很多暂时还没用到的API，我们会在后面的文章当中学习。

到这里，第二步load()方法也就分析结束了。为什么呢？因为你会发现DrawableRequestBuilder类中有一个into()方法（【①】），也就是说，最终load()方法返回的其实就是一个DrawableTypeRequest对象。那么接下来我们就要进行第三步了，分析into()方法中的逻辑。
```java
/* DrawableRequestBuilder 源码 */
public class DrawableRequestBuilder<ModelType>
        extends GenericRequestBuilder<ModelType, ImageVideoWrapper, GifBitmapWrapper, GlideDrawable>
        implements BitmapOptions, DrawableOptions {

    DrawableRequestBuilder(Context context, Class<ModelType> modelClass,
            LoadProvider<ModelType, ImageVideoWrapper, GifBitmapWrapper, GlideDrawable> loadProvider, Glide glide,
            RequestTracker requestTracker, Lifecycle lifecycle) {
        super(context, modelClass, loadProvider, GlideDrawable.class, glide, requestTracker, lifecycle);
        // Default to animating.
        crossFade();
    }

    @Override
    public DrawableRequestBuilder<ModelType> load(ModelType model) {
        super.load(model);
        return this;
    }

    @Override
    public Target<GlideDrawable> into(ImageView view) {//【①】
        return super.into(view);
    }

    public DrawableRequestBuilder<ModelType> thumbnail(
            DrawableRequestBuilder<?> thumbnailRequest) {
        super.thumbnail(thumbnailRequest);
        return this;
    }

    @Override
    public DrawableRequestBuilder<ModelType> thumbnail(
            GenericRequestBuilder<?, ?, ?, GlideDrawable> thumbnailRequest) {
        super.thumbnail(thumbnailRequest);
        return this;
    }

    @Override
    public DrawableRequestBuilder<ModelType> thumbnail(float sizeMultiplier) {
        super.thumbnail(sizeMultiplier);
        return this;
    }

    @Override
    public DrawableRequestBuilder<ModelType> sizeMultiplier(float sizeMultiplier) {
        super.sizeMultiplier(sizeMultiplier);
        return this;
    }

    @Override
    public DrawableRequestBuilder<ModelType> decoder(ResourceDecoder<ImageVideoWrapper, GifBitmapWrapper> decoder) {
        super.decoder(decoder);
        return this;
    }

    @Override
    public DrawableRequestBuilder<ModelType> cacheDecoder(ResourceDecoder<File, GifBitmapWrapper> cacheDecoder) {
        super.cacheDecoder(cacheDecoder);
        return this;
    }

    @Override
    public DrawableRequestBuilder<ModelType> encoder(ResourceEncoder<GifBitmapWrapper> encoder) {
        super.encoder(encoder);
        return this;
    }

    @Override
    public DrawableRequestBuilder<ModelType> priority(Priority priority) {
        super.priority(priority);
        return this;
    }

    public DrawableRequestBuilder<ModelType> transform(BitmapTransformation... transformations) {
        return bitmapTransform(transformations);
    }

    public DrawableRequestBuilder<ModelType> centerCrop() {
        return transform(glide.getDrawableCenterCrop());
    }

    public DrawableRequestBuilder<ModelType> fitCenter() {
        return transform(glide.getDrawableFitCenter());
    }

    public DrawableRequestBuilder<ModelType> bitmapTransform(Transformation<Bitmap>... bitmapTransformations) {
        GifBitmapWrapperTransformation[] transformations =
                new GifBitmapWrapperTransformation[bitmapTransformations.length];
        for (int i = 0; i < bitmapTransformations.length; i++) {
            transformations[i] = new GifBitmapWrapperTransformation(glide.getBitmapPool(), bitmapTransformations[i]);
        }
        return transform(transformations);
    }

    @Override
    public DrawableRequestBuilder<ModelType> transform(Transformation<GifBitmapWrapper>... transformation) {
        super.transform(transformation);
        return this;
    }

    @Override
    public DrawableRequestBuilder<ModelType> transcoder(
            ResourceTranscoder<GifBitmapWrapper, GlideDrawable> transcoder) {
        super.transcoder(transcoder);
        return this;
    }

    public final DrawableRequestBuilder<ModelType> crossFade() {
        super.animate(new DrawableCrossFadeFactory<GlideDrawable>());
        return this;
    }

    public DrawableRequestBuilder<ModelType> crossFade(int duration) {
        super.animate(new DrawableCrossFadeFactory<GlideDrawable>(duration));
        return this;
    }

    public DrawableRequestBuilder<ModelType> crossFade(int animationId, int duration) {
        super.animate(new DrawableCrossFadeFactory<GlideDrawable>(context, animationId,
                duration));
        return this;
    }

    @Override
    public DrawableRequestBuilder<ModelType> dontAnimate() {
        super.dontAnimate();
        return this;
    }

    @Override
    public DrawableRequestBuilder<ModelType> animate(ViewPropertyAnimation.Animator animator) {
        super.animate(animator);
        return this;
    }

    @Override
    public DrawableRequestBuilder<ModelType> animate(int animationId) {
        super.animate(animationId);
        return this;
    }

    @Override
    public DrawableRequestBuilder<ModelType> placeholder(int resourceId) {
        super.placeholder(resourceId);
        return this;
    }

    @Override
    public DrawableRequestBuilder<ModelType> placeholder(Drawable drawable) {
        super.placeholder(drawable);
        return this;
    }

    @Override
    public DrawableRequestBuilder<ModelType> fallback(Drawable drawable) {
        super.fallback(drawable);
        return this;
    }

    @Override
    public DrawableRequestBuilder<ModelType> fallback(int resourceId) {
        super.fallback(resourceId);
        return this;
    }

    @Override
    public DrawableRequestBuilder<ModelType> error(int resourceId) {
        super.error(resourceId);
        return this;
    }

    @Override
    public DrawableRequestBuilder<ModelType> error(Drawable drawable) {
        super.error(drawable);
        return this;
    }

    @Override
    public DrawableRequestBuilder<ModelType> listener(
            RequestListener<? super ModelType, GlideDrawable> requestListener) {
        super.listener(requestListener);
        return this;
    }
    @Override
    public DrawableRequestBuilder<ModelType> diskCacheStrategy(DiskCacheStrategy strategy) {
        super.diskCacheStrategy(strategy);
        return this;
    }

    @Override
    public DrawableRequestBuilder<ModelType> skipMemoryCache(boolean skip) {
        super.skipMemoryCache(skip);
        return this;
    }

    @Override
    public DrawableRequestBuilder<ModelType> override(int width, int height) {
        super.override(width, height);
        return this;
    }

    @Override
    public DrawableRequestBuilder<ModelType> sourceEncoder(Encoder<ImageVideoWrapper> sourceEncoder) {
        super.sourceEncoder(sourceEncoder);
        return this;
    }

    @Override
    public DrawableRequestBuilder<ModelType> dontTransform() {
        super.dontTransform();
        return this;
    }

    @Override
    public DrawableRequestBuilder<ModelType> signature(Key signature) {
        super.signature(signature);
        return this;
    }
    @Override
    public DrawableRequestBuilder<ModelType> clone() {
        return (DrawableRequestBuilder<ModelType>) super.clone();
    }

    @Override
    void applyFitCenter() {
        fitCenter();
    }

    @Override
    void applyCenterCrop() {
        centerCrop();
    }
}
```
### load调用图解
[![load调用图解](https://upload-images.jianshu.io/upload_images/9028834-b18e0d6c143938a0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://upload-images.jianshu.io/upload_images/9028834-b18e0d6c143938a0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 3. Glide.with(…).load(…).into(ImageView)

into()方法是整个Glide图片加载流程中逻辑最复杂的地方。
### 3.1 drawableTypeRequest.into(ImageView)
由于**load**()方法**返回**的是一个**DrawableTypeRequest对象**，DrawableTypeRequest中并没有into()方法，最终调用其父类DrawableRequestBuilder的into()方法：
```java
/* DrawableRequestBuilder 源码 */
@Override
public Target<GlideDrawable> into(ImageView view) {
    return super.into(view);
}
```

into()方法的具体逻辑在DrawableRequestBuilder的父类GenericRequestBuilder当中。

#### 3.1.1 genericRequestBuilder.into(ImageView)
```java
/* GenericRequestBuilder 源码 */
public Target<TranscodeType> into(ImageView view) {
    Util.assertMainThread();
    if (view == null) {
        throw new IllegalArgumentException("You must pass in a non null View");
    }
    if (!isTransformationSet && view.getScaleType() != null) {
        switch (view.getScaleType()) {
            case CENTER_CROP:
                applyCenterCrop();
                break;
            case FIT_CENTER:
            case FIT_START:
            case FIT_END:
                applyFitCenter();
                break;
            //$CASES-OMITTED$
            default:
                // Do nothing.
        }
    }
    return into(glide.buildImageViewTarget(view, transcodeClass));
}
```

这里前面一大堆的判断逻辑我们都可以先不用管，等到后面文章讲transform的时候会再进行解释，现在我们只需要关注最后一行代码。
### 3.2 glide.buildImageViewTarget(..)
最后一行代码先是调用了**glide.buildImageViewTarget()**方法，这个方法会**构建出一个Target对象**，Target对象则是用来最终展示图片用的：

```java
/* Glide 源码 */
<R> Target<R> buildImageViewTarget(ImageView imageView, Class<R> transcodedClass) {
    return imageViewTargetFactory.buildTarget(imageView, transcodedClass);
}
```
#### 3.2.1 imageViewTargetFactory.buildTarget(..)
这里其实又是调用了ImageViewTargetFactory的buildTarget()方法：

```java
/* ImageViewTargetFactory 源码 */
public class ImageViewTargetFactory {
    @SuppressWarnings("unchecked")
    public <Z> Target<Z> buildTarget(ImageView view, Class<Z> clazz) {
        if (GlideDrawable.class.isAssignableFrom(clazz)) {
            return (Target<Z>) new GlideDrawableImageViewTarget(view);
        } else if (Bitmap.class.equals(clazz)) {
            return (Target<Z>) new BitmapImageViewTarget(view);
        } else if (Drawable.class.isAssignableFrom(clazz)) {
            return (Target<Z>) new DrawableImageViewTarget(view);
        } else {
            throw new IllegalArgumentException("Unhandled class: " + clazz
                    + ", try .as*(Class).transcode(ResourceTranscoder)");
        }
    }
}
```

可以看到，在**buildTarget**()方法中会**根据传入的class参数来构建不同的Target对象**。那如果你要分析这个class参数是从哪儿传过来的，这可有得你分析了，简单起见我直接帮大家梳理清楚。

这个**class参数**其实基本上只有**两种**情况：
如果你在使用Glide加载图片的时候调用了**asBitmap**()方法，那么这里就会**构建出BitmapImageViewTarget对象**；
**否则**的话**构建**的都是**GlideDrawableImageViewTarget对象**。
至于上述代码中的DrawableImageViewTarget对象，这个通常都是用不到的，我们可以暂时不用管它。

也就是说，通过glide.buildImageViewTarget()方法，我们构建出了一个GlideDrawableImageViewTarget对象。
### 3.3 genericRequestBuilder.into(Y target)
那现在回到【3.1.1 genericRequestBuilder.into(ImageView)】into()方法的最后一行 `return into(glide.buildImageViewTarget(view, transcodeClass));`，可以看到，这里将glide.buildImageViewTarget()方法构建的GlideDrawableImageViewTarget对象传入到了GenericRequestBuilder另一个接收Target对象的into()方法当中了。
```java
/* GenericRequestBuilder 源码 */
public <Y extends Target<TranscodeType>> Y into(Y target) {
    Util.assertMainThread();
    if (target == null) {
        throw new IllegalArgumentException("You must pass in a non null Target");
    }
    if (!isModelSet) {
        throw new IllegalArgumentException("You must first set a model (try #load())");
    }
    Request previous = target.getRequest();
    if (previous != null) {
        previous.clear();
        requestTracker.removeRequest(previous);
        previous.recycle();
    }
    Request request = buildRequest(target);//【①】
    target.setRequest(request);
    lifecycle.addListener(target);
    requestTracker.runRequest(request);//【②】
    return target;
}
```

这里我们还是只抓核心代码，其实只有两行是最关键的，【①】`Request request = buildRequest(target);`构建出了一个Request对象，还有【②】`requestTracker.runRequest(request);`执行这个Request。

**`Request`**是用来**发出加载图片请求**的，它是Glide中非常关键的一个组件。
#### 3.3.1 buildRequest(Target< TranscodeType>)
我们先来看**`buildRequest()`**方法是如何**构建Request对象**的：
```java
/* GenericRequestBuilder 源码 */
private Request buildRequest(Target<TranscodeType> target) {
    if (priority == null) {
        priority = Priority.NORMAL;
    }
    return buildRequestRecursive(target, null);
}

private Request buildRequestRecursive(Target<TranscodeType> target, ThumbnailRequestCoordinator parentCoordinator) {
    if (thumbnailRequestBuilder != null) {
        if (isThumbnailBuilt) {
            throw new IllegalStateException("You cannot use a request as both the main request and a thumbnail, "
                    + "consider using clone() on the request(s) passed to thumbnail()");
        }
        // Recursive case: contains a potentially recursive thumbnail request builder.
        if (thumbnailRequestBuilder.animationFactory.equals(NoAnimation.getFactory())) {
            thumbnailRequestBuilder.animationFactory = animationFactory;
        }

        if (thumbnailRequestBuilder.priority == null) {
            thumbnailRequestBuilder.priority = getThumbnailPriority();
        }

        if (Util.isValidDimensions(overrideWidth, overrideHeight)
                && !Util.isValidDimensions(thumbnailRequestBuilder.overrideWidth,
                        thumbnailRequestBuilder.overrideHeight)) {
          thumbnailRequestBuilder.override(overrideWidth, overrideHeight);
        }

        ThumbnailRequestCoordinator coordinator = new ThumbnailRequestCoordinator(parentCoordinator);
        Request fullRequest = obtainRequest(target, sizeMultiplier, priority, coordinator);
        // Guard against infinite recursion.
        isThumbnailBuilt = true;
        // Recursively generate thumbnail requests.
        Request thumbRequest = thumbnailRequestBuilder.buildRequestRecursive(target, coordinator);
        isThumbnailBuilt = false;
        coordinator.setRequests(fullRequest, thumbRequest);
        return coordinator;
    } else if (thumbSizeMultiplier != null) {
        // Base case: thumbnail multiplier generates a thumbnail request, but cannot recurse.
        ThumbnailRequestCoordinator coordinator = new ThumbnailRequestCoordinator(parentCoordinator);
        Request fullRequest = obtainRequest(target, sizeMultiplier, priority, coordinator);
        Request thumbnailRequest = obtainRequest(target, thumbSizeMultiplier, getThumbnailPriority(), coordinator);
        coordinator.setRequests(fullRequest, thumbnailRequest);
        return coordinator;
    } else {
        // Base case: no thumbnail.
        return obtainRequest(target, sizeMultiplier, priority, parentCoordinator);//【①】
    }
}

private Request obtainRequest(Target<TranscodeType> target, float sizeMultiplier, Priority priority,
        RequestCoordinator requestCoordinator) {
    return GenericRequest.obtain(
            loadProvider,
            model,
            signature,
            context,
            priority,
            target,
            sizeMultiplier,
            placeholderDrawable,
            placeholderId,
            errorPlaceholder,
            errorId,
            fallbackDrawable,
            fallbackResource,
            requestListener,
            requestCoordinator,
            glide.getEngine(),
            transformation,
            transcodeClass,
            isCacheable,
            animationFactory,
            overrideWidth,
            overrideHeight,
            diskCacheStrategy);
}
```

可以看到，buildRequest()方法的内部其实又调用了buildRequestRecursive()方法，而buildRequestRecursive()方法中的代码虽然有点长，但是其中90%的代码都是在处理缩略图的。
如果我们只追主线流程的话，那么只需要看【①】`return obtainRequest(target, sizeMultiplier, priority, parentCoordinator);`就可以了。这里调用了**obtainRequest**()方法来**获取一个Request对象**，而**obtainRequest**()方法中又去**调用**了**GenericRequest的obtain()**方法。

##### 3.3.1.1 GenericRequest.obtain(.)
注意这个obtain()方法需要传入非常多的参数，而其中很多的参数我们都是比较熟悉的，像什么placeholderId、errorPlaceholder、diskCacheStrategy等等。因此，我们就有理由猜测，刚才**在load()方法中调用的所有API**，其实都**是在这里组装到Request对象当中**的。
```java
/* GenericRequest 源码 */
public final class GenericRequest<A, T, Z, R> implements Request, SizeReadyCallback,
        ResourceCallback {
    ...
    public static <A, T, Z, R> GenericRequest<A, T, Z, R> obtain(
            LoadProvider<A, T, Z, R> loadProvider,
            A model,
            Key signature,
            Context context,
            Priority priority,
            Target<R> target,
            float sizeMultiplier,
            Drawable placeholderDrawable,
            int placeholderResourceId,
            Drawable errorDrawable,
            int errorResourceId,
            Drawable fallbackDrawable,
            int fallbackResourceId,
            RequestListener<? super A, R> requestListener,
            RequestCoordinator requestCoordinator,
            Engine engine,
            Transformation<Z> transformation,
            Class<R> transcodeClass,
            boolean isMemoryCacheable,
            GlideAnimationFactory<R> animationFactory,
            int overrideWidth,
            int overrideHeight,
            DiskCacheStrategy diskCacheStrategy) {
        @SuppressWarnings("unchecked")
        GenericRequest<A, T, Z, R> request = (GenericRequest<A, T, Z, R>) REQUEST_POOL.poll();
        if (request == null) {
            request = new GenericRequest<A, T, Z, R>();//【①】
        }
        request.init(loadProvider,//【②】
                model,
                signature,
                context,
                priority,
                target,
                sizeMultiplier,
                placeholderDrawable,
                placeholderResourceId,
                errorDrawable,
                errorResourceId,
                fallbackDrawable,
                fallbackResourceId,
                requestListener,
                requestCoordinator,
                engine,
                transformation,
                transcodeClass,
                isMemoryCacheable,
                animationFactory,
                overrideWidth,
                overrideHeight,
                diskCacheStrategy);
        return request;
    }
    ...
}
```

可以看到，这里在【①】`request = new GenericRequest<A, T, Z, R>();`去new了一个GenericRequest对象，并在最后一行返回，也就是说，**obtain()方法实际上获得的就是一个GenericRequest对象**。
另外这里又在【②】调用了GenericRequest的init()，里面主要就是一些赋值的代码，将传入的这些参数赋值到GenericRequest的成员变量当中，我们就不再跟进去看了。

#### 3.3.2   requestTracker.runRequest(Request)
好，那现在解决了构建Request对象的问题，接下来我们看一下这个Request对象又是怎么执行的。回到刚才的into()方法【3.3 genericRequestBuilder.into(Y target)】，你会发现在第18行【②】调用了`requestTracker.runRequest()`方法来去执行这个Request：

```java
/* RequestTracker 源码 */
public void runRequest(Request request) {
    requests.add(request);
    if (!isPaused) {//Glide当前是不是处理暂停状态
        request.begin();
    } else {
        pendingRequests.add(request);
    }
}
```

这里有一个简单的逻辑判断，就是先判断Glide当前是不是处理暂停状态：
如果不是暂停状态就调用Request的begin()方法来执行Request，
否则的话就先将Request添加到待执行队列里面，等暂停状态解除了之后再执行。
#### 3.3.2.1 genericRequest.begin()
暂停请求的功能仍然不是这篇文章所关心的，这里就直接忽略了，我们重点来看这个begin()方法。
由于**当前的Request对象是一个GenericRequest**（注解①），此这里就需要看GenericRequest中的begin()方法了，如下所示：
注解①：【3.3 genericRequestBuilder.into(Y target)】代码中Request request = buildRequest(target); buildRequest最终调用的是【3.3.1.1 GenericRequest.obtain(.)】返回的是一个 GenericRequest对象。

```java
/* GenericRequest 源码 */
@Override
public void begin() {
    startTime = LogTime.getLogTime();
    if (model == null) {//注解② model就是第二步load()方法中传入的图片URL地址。 
        onException(null);//【①】3.3.2.1.1 onException()
        return;
    }
    status = Status.WAITING_FOR_SIZE;
    if (Util.isValidDimensions(overrideWidth, overrideHeight)) {
        onSizeReady(overrideWidth, overrideHeight);//【③】3.3.2.1.2 onSizeReady(int width, int height)
    } else {
        target.getSize(this);//【④】
    }
    if (!isComplete() && !isFailed() && canNotifyStatusChanged()) {
        target.onLoadStarted(getPlaceholderDrawable());//【②】target.onLoadStarted
    }
    if (Log.isLoggable(TAG, Log.VERBOSE)) {
        logV("finished run method in " + LogTime.getElapsedMillis(startTime));
    }
}
```
##### 图片URL地址传递图解
注解②：**model**也就是我们**load()方法中传入的图片URL地址**。
[![](https://github.com/Rtia33/Notes/blob/master/Pics/into%E6%B5%81%E7%A8%8B.png?raw=true)](https://github.com/Rtia33/Notes/blob/master/Pics/into%E6%B5%81%E7%A8%8B.png?raw=true)

##### 3.3.2.1.1 onException()
如果model等于null，这个时候会调用onException()方法。
```java
/* GenericRequest 源码 */
@Override
public void onException(Exception e) {
    if (Log.isLoggable(TAG, Log.DEBUG)) {
        Log.d(TAG, "load failed", e);
    }
    status = Status.FAILED;
    if (requestListener == null || !requestListener.onException(e, model, target, isFirstReadyResource())) {
        setErrorPlaceholder(e);//setErrorPlaceholder
    }
}
```
###### 3.3.2.1.1.1 setErrorPlaceholder()
onException()方法里面可见，它最终会调用到一个setErrorPlaceholder()当中，如下所示：

```java
/* GenericRequest 源码 */
private void setErrorPlaceholder(Exception e) {
    if (!canNotifyStatusChanged()) {
        return;
    }
    Drawable error = model == null ? getFallbackDrawable() : null;
    if (error == null) {
      error = getErrorDrawable();//获取一个error的占位图
    }
    if (error == null) {
        error = getPlaceholderDrawable();//如获取不到的话会再去获取一个loading占位图
    }
    target.onLoadFailed(e, error);//将占位图传入
}
```

这个方法中会先去获取一个error的占位图，如果获取不到的话会再去获取一个loading占位图，然后调用target.onLoadFailed()方法并将占位图传入。
那么onLoadFailed()方法中做了什么呢？我们看一下：
###### 3.3.2.1.1.2 imageViewTarget.onLoadFailed()
```
public abstract class ImageViewTarget<Z> extends ViewTarget<ImageView, Z> implements GlideAnimation.ViewAdapter {
    ...
    @Override
    public void onLoadFailed(Exception e, Drawable errorDrawable) {
        view.setImageDrawable(errorDrawable);
    }
    @Override
    public void onLoadStarted(Drawable placeholder) {
        view.setImageDrawable(placeholder);
    }
    ...
}
```
就是**将这张error占位图显示到ImageView**上而已，因为现在出现了异常，没办法展示正常的图片了。
在【3.3.2.1 genericRequest.begin()】方法的第15行【②】，调用了一个`target.onLoadStarted()`方法，并**传入**了一个**loading占位图**，在也就说，在图片请求开始之前，会先使用这张占位图代替最终的图片显示。这也是我们在上一篇文章中学过的**placeholder()和error()**这两个占位图API**底层**的**实现**原理。

##### 图片加载
我们继续回到begin()方法【3.3.2.1 genericRequest.begin()】。
刚才讲了占位图的实现，那么具体的**图片加载**又是从哪里开始的呢？是在begin()方法的第10行【③】和第12行【④】。

这里要分**两种情况**：
一种是你使用了override() API为图片指定了一个固定的宽高，一种是没有指定。
- 如果**指定**了一个**固定**的**宽高**的话，就会执行第10行代码【③】，调用**`onSizeReady()`**方法。
- 如果没指定的话，就会执行第12行代码【④】，调用**`target.getSize()`**方法。
  这个target.getSize()方法的内部会根据ImageView的layout_width和layout_height值做一系列的计算，来**算**出**图片**应该的**宽高**。具体的计算细节我就不带着大家分析了，总之在**计算完之后**，它也会**调用onSizeReady()**方法。


##### 3.3.2.1.2 onSizeReady(int width, int height)
也就是说，不管是哪种情况，最终都会调用到onSizeReady()方法，在这里进行下一步操作。那么我们跟到这个方法里面来：

```java
/* GenericRequest 源码 */
@Override
public void onSizeReady(int width, int height) {
    if (Log.isLoggable(TAG, Log.VERBOSE)) {
        logV("Got onSizeReady in " + LogTime.getElapsedMillis(startTime));
    }
    if (status != Status.WAITING_FOR_SIZE) {
        return;
    }
    status = Status.RUNNING;
    width = Math.round(sizeMultiplier * width);
    height = Math.round(sizeMultiplier * height);
    ModelLoader<A, T> modelLoader = loadProvider.getModelLoader();//【①】
    final DataFetcher<T> dataFetcher = modelLoader.getResourceFetcher(model, width, height);//【②】3.3.2.1.2.1 imageVideoModelLoader.getResourceFetcher
    if (dataFetcher == null) {
        onException(new Exception("Failed to load model: \'" + model + "\'"));
        return;
    }
    ResourceTranscoder<Z, R> transcoder = loadProvider.getTranscoder();//【③】
    if (Log.isLoggable(TAG, Log.VERBOSE)) {
        logV("finished setup for calling load in " + LogTime.getElapsedMillis(startTime));
    }
    loadedFromMemoryCache = true;
    loadStatus = engine.load(signature, width, height, dataFetcher, loadProvider, transformation, transcoder,//【④】
            priority, isMemoryCacheable, diskCacheStrategy, this);
    loadedFromMemoryCache = resource != null;
    if (Log.isLoggable(TAG, Log.VERBOSE)) {
        logV("finished onSizeReady in " + LogTime.getElapsedMillis(startTime));
    }
}
```

从这里开始，真正复杂的地方来了，我们需要慢慢进行分析。先来看一下，在第12行【①】调用了`loadProvider.getModelLoader()`方法。那么我们第一个要搞清楚的就是，这个loadProvider是什么？要搞清楚这点，需要先回到第二步的load()方法当中。
load()方法返回一个DrawableTypeRequest对象，我们重新来看一下DrawableTypeRequest类的构造函数：
###### DrawableTypeRequest构造函数
```java
/* DrawableTypeRequest 源码 */
public class DrawableTypeRequest<ModelType> extends DrawableRequestBuilder<ModelType> implements DownloadOptions {
    private final ModelLoader<ModelType, InputStream> streamModelLoader;
    private final ModelLoader<ModelType, ParcelFileDescriptor> fileDescriptorModelLoader;
    private final RequestManager.OptionsApplier optionsApplier;
    
    //DrawableTypeRequest构造函数
    DrawableTypeRequest(Class<ModelType> modelClass, ModelLoader<ModelType, InputStream> streamModelLoader,
            ModelLoader<ModelType, ParcelFileDescriptor> fileDescriptorModelLoader, Context context, Glide glide,
            RequestTracker requestTracker, Lifecycle lifecycle, RequestManager.OptionsApplier optionsApplier) {
        super(context, modelClass,
                //【①】buildProvider
                buildProvider(glide, streamModelLoader, fileDescriptorModelLoader, GifBitmapWrapper.class,
                        GlideDrawable.class, null),
                glide, requestTracker, lifecycle);
        this.streamModelLoader = streamModelLoader;
        this.fileDescriptorModelLoader = fileDescriptorModelLoader;
        this.optionsApplier = optionsApplier;
    }
    //buildProvider
    private static <A, Z, R> FixedLoadProvider<A, ImageVideoWrapper, Z, R> buildProvider(Glide glide,
            ModelLoader<A, InputStream> streamModelLoader,
            ModelLoader<A, ParcelFileDescriptor> fileDescriptorModelLoader, Class<Z> resourceClass,
            Class<R> transcodedClass,
            ResourceTranscoder<Z, R> transcoder) {
        if (streamModelLoader == null && fileDescriptorModelLoader == null) {
            return null;
        }
        if (transcoder == null) {
            transcoder = glide.buildTranscoder(resourceClass, transcodedClass);//【②】
        }
        DataLoadProvider<ImageVideoWrapper, Z> dataLoadProvider 
                = glide.buildDataProvider(ImageVideoWrapper.class, resourceClass);//【③】
        ImageVideoModelLoader<A> modelLoader 
                = new ImageVideoModelLoader<A>(streamModelLoader, fileDescriptorModelLoader);//【④】
        return new FixedLoadProvider<A, ImageVideoWrapper, Z, R>(modelLoader, transcoder, dataLoadProvider);//【⑤】
    }
    ...
}
```

可以看到，在【①】下面，也就是构造函数中，调用了一个`buildProvider()`方法，并把streamModelLoader和fileDescriptorModelLoader等参数传入到这个方法中，这两个ModelLoader就是之前在【2.1.2 loadGeneric(Class< T >)】方法中构建出来的。

那么我们再来看一下buildProvider()方法里面做了什么，在【②】调用了`glide.buildTranscoder()`方法来**构建**一个**ResourceTranscoder**接口，它是用于**对图片进行转码**的，由于ResourceTranscoder是一个接口，这里实际会**构建**出一个**`GifBitmapWrapperDrawableTranscoder`对象**。

接下来在【③】调用了`glide.buildDataProvider()`方法来**构建**一个**DataLoadProvider**接口，它是用于**对图片进行编解码**的，由于DataLoadProvider是一个接口，这里实际会**构建**出一个**ImageVideoGifDrawableLoadProvider对象**。

然后在【④】，**new**了一个**`ImageVideoModelLoader`**的实例，并**把**之前**loadGeneric**()方法中**构建的**两个**ModelLoader封装到了ImageVideoModelLoader当中。**

最后，在【⑤】，**new**出一个**`FixedLoadProvider`**，并**把**刚才构建的出来的**GifBitmapWrapperDrawableTranscoder、ImageVideoModelLoader、ImageVideoGifDrawableLoadProvider都封装进去**，这个**也就是onSizeReady()方法中的loadProvider**了。

好的，那么我们回到【3.3.2.1.2 onSizeReady(int width, int height)】方法中的【①】 `loadProvider.getModelLoader();`和【③】`loadProvider.getTranscoder();`，**得到**的对象分别也就是刚才我们分析的**ImageVideoModelLoader**和**GifBitmapWrapperDrawableTranscoder**了。

###### loadProvider来源图解
[![loadProvider来源](https://upload-images.jianshu.io/upload_images/9028834-3527724dbfcb065d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://upload-images.jianshu.io/upload_images/9028834-3527724dbfcb065d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###### 3.3.2.1.2.1 imageVideoModelLoader.getResourceFetcher(.)
而在【3.3.2.1.2 onSizeReady(int width, int height)】【②】`modelLoader.getResourceFetcher(model, width, height);`，又调用了ImageVideoModelLoader的**`getResourceFetcher()`**方法：
```java
/* ImageVideoModelLoader 源码 */
public class ImageVideoModelLoader<A> implements ModelLoader<A, ImageVideoWrapper> {
    private static final String TAG = "IVML";

    private final ModelLoader<A, InputStream> streamLoader;
    private final ModelLoader<A, ParcelFileDescriptor> fileDescriptorLoader;

    public ImageVideoModelLoader(ModelLoader<A, InputStream> streamLoader,
            ModelLoader<A, ParcelFileDescriptor> fileDescriptorLoader) {
        if (streamLoader == null && fileDescriptorLoader == null) {
            throw new NullPointerException("At least one of streamLoader and fileDescriptorLoader must be non null");
        }
        this.streamLoader = streamLoader;
        this.fileDescriptorLoader = fileDescriptorLoader;
    }

    @Override
    public DataFetcher<ImageVideoWrapper> getResourceFetcher(A model, int width, int height) {
        DataFetcher<InputStream> streamFetcher = null;
        if (streamLoader != null) {
            streamFetcher = streamLoader.getResourceFetcher(model, width, height);//【①】
        }
        DataFetcher<ParcelFileDescriptor> fileDescriptorFetcher = null;
        if (fileDescriptorLoader != null) {
            fileDescriptorFetcher = fileDescriptorLoader.getResourceFetcher(model, width, height);
        }

        if (streamFetcher != null || fileDescriptorFetcher != null) {
            return new ImageVideoFetcher(streamFetcher, fileDescriptorFetcher);//【②】
        } else {
            return null;
        }
    }

    static class ImageVideoFetcher implements DataFetcher<ImageVideoWrapper> {
        private final DataFetcher<InputStream> streamFetcher;
        private final DataFetcher<ParcelFileDescriptor> fileDescriptorFetcher;

        public ImageVideoFetcher(DataFetcher<InputStream> streamFetcher,
                DataFetcher<ParcelFileDescriptor> fileDescriptorFetcher) {
            this.streamFetcher = streamFetcher;
            this.fileDescriptorFetcher = fileDescriptorFetcher;
        }
        ...
    }
}
```

可以看到，在第20行【①】先调用`streamLoader.getResourceFetcher()`方法**获取**一个**DataFetcher**，而这个**streamLoader**其实**就是**我们在**loadGeneric**()方法中**构建出的StreamStringLoader**，调用它的**getResourceFetcher**()方法会**得到**一个**HttpUrlFetcher对象**。
然后在第28行【②】`return new ImageVideoFetcher(streamFetcher, fileDescriptorFetcher);`new出了一个ImageVideoFetcher对象，并把获得的HttpUrlFetcher对象传进去。也就是说，ImageVideoModelLoader的getResourceFetcher()方法得到的是一个ImageVideoFetcher。

###### 3.3.2.1.2.2 engine.load(.)
那么我们再次回到【3.3.2.1.2 onSizeReady(int width, int height)】方法，第23行【④】，这里将刚才获得的ImageVideoFetcher、GifBitmapWrapperDrawableTranscoder等等一系列的值一起传入到了Engine的load()方法当中。接下来我们就要看一看，这个Engine的load()方法当中，到底做了什么？代码如下所示：

```java
/* Engine 源码 */
public class Engine implements EngineJobListener,
        MemoryCache.ResourceRemovedListener,
        EngineResource.ResourceListener {
    ...    
    public <T, Z, R> LoadStatus load(Key signature, int width, int height, DataFetcher<T> fetcher,
            DataLoadProvider<T, Z> loadProvider, Transformation<Z> transformation, ResourceTranscoder<Z, R> transcoder,
            Priority priority, boolean isMemoryCacheable, DiskCacheStrategy diskCacheStrategy, ResourceCallback cb) {
        Util.assertMainThread();
        long startTime = LogTime.getLogTime();

        final String id = fetcher.getId();
        EngineKey key = keyFactory.buildKey(id, signature, width, height, loadProvider.getCacheDecoder(),
                loadProvider.getSourceDecoder(), transformation, loadProvider.getEncoder(),
                transcoder, loadProvider.getSourceEncoder());

        EngineResource<?> cached = loadFromCache(key, isMemoryCacheable);
        if (cached != null) {
            cb.onResourceReady(cached);
            if (Log.isLoggable(TAG, Log.VERBOSE)) {
                logWithTimeAndKey("Loaded resource from cache", startTime, key);
            }
            return null;
        }

        EngineResource<?> active = loadFromActiveResources(key, isMemoryCacheable);
        if (active != null) {
            cb.onResourceReady(active);
            if (Log.isLoggable(TAG, Log.VERBOSE)) {
                logWithTimeAndKey("Loaded resource from active resources", startTime, key);
            }
            return null;
        }

        EngineJob current = jobs.get(key);
        if (current != null) {
            current.addCallback(cb);
            if (Log.isLoggable(TAG, Log.VERBOSE)) {
                logWithTimeAndKey("Added to existing load", startTime, key);
            }
            return new LoadStatus(cb, current);
        }

        EngineJob engineJob = engineJobFactory.build(key, isMemoryCacheable);//【①】
        DecodeJob<T, Z, R> decodeJob = new DecodeJob<T, Z, R>(key, width, height, fetcher, loadProvider, transformation,//【②】
                transcoder, diskCacheProvider, diskCacheStrategy, priority);
        EngineRunnable runnable = new EngineRunnable(engineJob, decodeJob, priority);//【③】
        jobs.put(key, engineJob);
        engineJob.addCallback(cb);
        engineJob.start(runnable);//【④】

        if (Log.isLoggable(TAG, Log.VERBOSE)) {
            logWithTimeAndKey("Started new load", startTime, key);
        }
        return new LoadStatus(cb, engineJob);
    }

    ...
}
```

load()方法中的代码虽然有点长，但大多数的代码都是在处理缓存的。关于缓存的内容我们会在下一篇文章当中学习，现在只需要从第45行【①】看起就行。这里构建了一个**`EngineJob`**，它的主要作用就是用来**开启线程**的，为后面的异步加载图片做准备。接下来第46行【②】创建了一个**`DecodeJob`**对象，从名字上来看，它好像是用来对图片进行解码的，但实际上它的任务十分繁重，待会我们就知道了。继续往下看，第48行【③】创建了一个**`EngineRunnable`**对象，并且在51行调用了EngineJob的start()方法来运行EngineRunnable对象，这实际上就是让EngineRunnable的run()方法在子线程当中执行了。


那么我们现在就可以去看看EngineRunnable的run()方法里做了些什么，如下所示：

```java
/* EngineRunnable 源码 */
@Override
public void run() {
    if (isCancelled) {
        return;
    }
    Exception exception = null;
    Resource<?> resource = null;
    try {
        resource = decode();//【①】
    } catch (Exception e) {
        if (Log.isLoggable(TAG, Log.VERBOSE)) {
            Log.v(TAG, "Exception decoding", e);
        }
        exception = e;
    }
    if (isCancelled) {
        if (resource != null) {
            resource.recycle();
        }
        return;
    }
    if (resource == null) {
        onLoadFailed(exception);
    } else {
        onLoadComplete(resource);
    }
}
```

这个方法中的代码并不多，但我们仍然还是要抓重点。在第9行【①】，这里调用了一个**`decode()`**方法，并且这个方法返回了一个Resource对象。看上去所有的逻辑应该都在这个decode()方法执行的了，那我们跟进去瞧一瞧：


```java
/* EngineRunnable 源码 */
private Resource<?> decode() throws Exception {
    if (isDecodingFromCache()) {
        return decodeFromCache();
    } else {
        return decodeFromSource();
    }
}
```

decode()方法中又分了两种情况，从缓存当中去decode图片的话就会执行decodeFromCache()，否则的话就执行**`decodeFromSource()`**。本篇文章中我们不讨论缓存的情况，那么就直接来看decodeFromSource()方法的代码吧，如下所示：


```java
/* EngineRunnable 源码 */
private Resource<?> decodeFromSource() throws Exception {
    return decodeJob.decodeFromSource();
}
```

这里又调用了DecodeJob的**`decodeFromSource()`**方法：

```java
/* DecodeJob 源码 */
class DecodeJob<A, T, Z> {
    ...
    public Resource<Z> decodeFromSource() throws Exception {
        Resource<T> decoded = decodeSource();
        return transformEncodeAndTranscode(decoded);
    }

    private Resource<T> decodeSource() throws Exception {
        Resource<T> decoded = null;
        try {
            long startTime = LogTime.getLogTime();
            final A data = fetcher.loadData(priority);
            if (Log.isLoggable(TAG, Log.VERBOSE)) {
                logWithTimeAndKey("Fetched data", startTime);
            }
            if (isCancelled) {
                return null;
            }
            decoded = decodeFromSourceData(data);
        } finally {
            fetcher.cleanup();
        }
        return decoded;
    }
    ...
}
```

主要的方法就这些，我都帮大家提取出来了。那么我们先来看一下decodeFromSource()方法，其实它的工作分为两部，**第一步**是调用**decodeSource**()方法来**获得**一个**Resource对象**，**第二步**是调用**transformEncodeAndTranscode**()方法来**处理这个Resource对象**。

那么我们先来看第一步，decodeSource()方法中的逻辑也并不复杂，首先在第14行【①】调用了`fetcher.loadData()`方法。
那么这个**fetcher**是什么呢？其实就是刚才在**onSizeReady**()方法**中得到的ImageVideoFetcher对象**，这里调用它的loadData()方法，代码如下所示：

```java
/* ImageVideoFetcher 源码 */
@Override
public ImageVideoWrapper loadData(Priority priority) throws Exception {
    InputStream is = null;
    if (streamFetcher != null) {
        try {
            is = streamFetcher.loadData(priority);//【①】
        } catch (Exception e) {
            if (Log.isLoggable(TAG, Log.VERBOSE)) {
                Log.v(TAG, "Exception fetching input stream, trying ParcelFileDescriptor", e);
            }
            if (fileDescriptorFetcher == null) {
                throw e;
            }
        }
    }
    ParcelFileDescriptor fileDescriptor = null;
    if (fileDescriptorFetcher != null) {
        try {
            fileDescriptor = fileDescriptorFetcher.loadData(priority);
        } catch (Exception e) {
            if (Log.isLoggable(TAG, Log.VERBOSE)) {
                Log.v(TAG, "Exception fetching ParcelFileDescriptor", e);
            }
            if (is == null) {
                throw e;
            }
        }
    }
    return new ImageVideoWrapper(is, fileDescriptor);
}
```

可以看到，在ImageVideoFetcher的loadData()方法的第6行【①】` streamFetcher.loadData(priority);`。
那么这个**streamFetcher**是什么呢？自然就是刚才在**组装ImageVideoFetcher对象时传进来的HttpUrlFetcher**了。
因此这里又会去调用HttpUrlFetcher的loadData()方法，那么我们继续跟进去瞧一瞧：

```java
/* HttpUrlFetcher 源码 */
public class HttpUrlFetcher implements DataFetcher<InputStream> {
    ...
    @Override
    public InputStream loadData(Priority priority) throws Exception {
        return loadDataWithRedirects(glideUrl.toURL(), 0 /*redirects*/, null /*lastUrl*/, glideUrl.getHeaders());
    }

    private InputStream loadDataWithRedirects(URL url, int redirects, URL lastUrl, Map<String, String> headers)
            throws IOException {
        if (redirects >= MAXIMUM_REDIRECTS) {
            throw new IOException("Too many (> " + MAXIMUM_REDIRECTS + ") redirects!");
        } else {
            // Comparing the URLs using .equals performs additional network I/O and is generally broken.
            // See http://michaelscharf.blogspot.com/2006/11/javaneturlequals-and-hashcode-make.html.
            try {
                if (lastUrl != null && url.toURI().equals(lastUrl.toURI())) {
                    throw new IOException("In re-direct loop");
                }
            } catch (URISyntaxException e) {
                // Do nothing, this is best effort.
            }
        }
        urlConnection = connectionFactory.build(url);
        for (Map.Entry<String, String> headerEntry : headers.entrySet()) {
          urlConnection.addRequestProperty(headerEntry.getKey(), headerEntry.getValue());
        }
        urlConnection.setConnectTimeout(2500);
        urlConnection.setReadTimeout(2500);
        urlConnection.setUseCaches(false);
        urlConnection.setDoInput(true);

        // Connect explicitly to avoid errors in decoders if connection fails.
        urlConnection.connect();
        if (isCancelled) {
            return null;
        }
        final int statusCode = urlConnection.getResponseCode();
        if (statusCode / 100 == 2) {
            return getStreamForSuccessfulRequest(urlConnection);
        } else if (statusCode / 100 == 3) {
            String redirectUrlString = urlConnection.getHeaderField("Location");
            if (TextUtils.isEmpty(redirectUrlString)) {
                throw new IOException("Received empty or null redirect url");
            }
            URL redirectUrl = new URL(url, redirectUrlString);
            return loadDataWithRedirects(redirectUrl, redirects + 1, url, headers);
        } else {
            if (statusCode == -1) {
                throw new IOException("Unable to retrieve response code from HttpUrlConnection.");
            }
            throw new IOException("Request failed " + statusCode + ": " + urlConnection.getResponseMessage());
        }
    }

    private InputStream getStreamForSuccessfulRequest(HttpURLConnection urlConnection)
            throws IOException {
        if (TextUtils.isEmpty(urlConnection.getContentEncoding())) {
            int contentLength = urlConnection.getContentLength();
            stream = ContentLengthInputStream.obtain(urlConnection.getInputStream(), contentLength);
        } else {
            if (Log.isLoggable(TAG, Log.DEBUG)) {
                Log.d(TAG, "Got non empty content encoding: " + urlConnection.getContentEncoding());
            }
            stream = urlConnection.getInputStream();
        }
        return stream;
    }
    ...
}
```

我们终于在这里找到网络通讯的代码了。

不过也别高兴得太早，现在离最终分析完还早着呢。可以看到，loadData()方法只是返回了一个InputStream，服务器返回的数据连读都还没开始读呢。所以我们还是要静下心来继续分析，回到刚才ImageVideoFetcher的loadData()方法中，在这个方法的最后一行，创建了一个ImageVideoWrapper对象，并把刚才得到的InputStream作为参数传了进去。

然后我们回到再上一层，也就是DecodeJob的decodeSource()方法当中，在得到了这个ImageVideoWrapper对象之后，紧接着又将这个对象传入到了decodeFromSourceData()当中，来去解码这个对象。decodeFromSourceData()方法的代码如下所示：

```java
/* DecodeJob 源码 */
private Resource<T> decodeFromSourceData(A data) throws IOException {
    final Resource<T> decoded;
    if (diskCacheStrategy.cacheSource()) {
        decoded = cacheAndDecodeSourceData(data);
    } else {
        long startTime = LogTime.getLogTime();
        decoded = loadProvider.getSourceDecoder().decode(data, width, height);//【①】
        if (Log.isLoggable(TAG, Log.VERBOSE)) {
            logWithTimeAndKey("Decoded from source", startTime);
        }
    }
    return decoded;
}
```

可以看到，这里在第7行【①】调用了`loadProvider.getSourceDecoder().decode()`方法来进行**解码**。
**loadProvider**就是刚才在**onSizeReady**()方法中**得到的FixedLoadProvider**；loadProvider.getSourceDecoder()得到的是一个**GifBitmapWrapperResourceDecoder**对象，也就是要调用这个对象的decode()方法来对图片进行解码。
那么我们来看下GifBitmapWrapperResourceDecoder的代码：

```java
/* GifBitmapWrapperResourceDecoder 源码 */
public class GifBitmapWrapperResourceDecoder implements ResourceDecoder<ImageVideoWrapper, GifBitmapWrapper> {
    ...
    @SuppressWarnings("resource")
    // @see ResourceDecoder.decode
    @Override
    public Resource<GifBitmapWrapper> decode(ImageVideoWrapper source, int width, int height) throws IOException {
        ByteArrayPool pool = ByteArrayPool.get();
        byte[] tempBytes = pool.getBytes();
        GifBitmapWrapper wrapper = null;
        try {
            wrapper = decode(source, width, height, tempBytes);//【①】
        } finally {
            pool.releaseBytes(tempBytes);
        }
        return wrapper != null ? new GifBitmapWrapperResource(wrapper) : null;
    }

    private GifBitmapWrapper decode(ImageVideoWrapper source, int width, int height, byte[] bytes) throws IOException {
        final GifBitmapWrapper result;
        if (source.getStream() != null) {
            result = decodeStream(source, width, height, bytes);//【②】
        } else {
            result = decodeBitmapWrapper(source, width, height);
        }
        return result;
    }

    private GifBitmapWrapper decodeStream(ImageVideoWrapper source, int width, int height, byte[] bytes)
            throws IOException {
        InputStream bis = streamFactory.build(source.getStream(), bytes);
        bis.mark(MARK_LIMIT_BYTES);
        ImageHeaderParser.ImageType type = parser.parse(bis);
        bis.reset();
        GifBitmapWrapper result = null;
        if (type == ImageHeaderParser.ImageType.GIF) {
            result = decodeGifWrapper(bis, width, height);
        }
        // Decoding the gif may fail even if the type matches.
        if (result == null) {
            // We can only reset the buffered InputStream, so to start from the beginning of the stream, we need to
            // pass in a new source containing the buffered stream rather than the original stream.
            ImageVideoWrapper forBitmapDecoder = new ImageVideoWrapper(bis, source.getFileDescriptor());
            result = decodeBitmapWrapper(forBitmapDecoder, width, height);
        }
        return result;
    }

    private GifBitmapWrapper decodeBitmapWrapper(ImageVideoWrapper toDecode, int width, int height) throws IOException {
        GifBitmapWrapper result = null;
        Resource<Bitmap> bitmapResource = bitmapDecoder.decode(toDecode, width, height);//【③】
        if (bitmapResource != null) {
            result = new GifBitmapWrapper(bitmapResource, null);
        }
        return result;
    }
    ...
}
```

首先，在decode()方法中【①】，又去调用了另外一个decode()方法的重载。
然后在第23行【②】调用了**`decodeStream()`**方法，准备**从服务器返回的流当中读取数据。
**decodeStream()方法中会先从流中**读取2个字节的数据**，来**判断**这张图是**GIF**图还是普通的**静图**：
如果是**GIF**图就调用`decodeGifWrapper()`方法来进行解码，
如果是普通的**静图**就用调用`decodeBitmapWrapper()`方法来进行解码。

这里我们只分析普通静图的实现流程，GIF图的实现有点过于复杂了，无法在本篇文章当中分析。

然后我们来看一下decodeBitmapWrapper()方法，这里在第52行【③】调用了`bitmapDecoder.decode()`方法。
这个bitmapDecoder是一个**`ImageVideoBitmapDecoder`**对象，那么我们来看一下它的代码，如下所示：

```java
/* ImageVideoBitmapDecoder 源码 */
public class ImageVideoBitmapDecoder implements ResourceDecoder<ImageVideoWrapper, Bitmap> {
    private final ResourceDecoder<InputStream, Bitmap> streamDecoder;
    private final ResourceDecoder<ParcelFileDescriptor, Bitmap> fileDescriptorDecoder;

    public ImageVideoBitmapDecoder(ResourceDecoder<InputStream, Bitmap> streamDecoder,
            ResourceDecoder<ParcelFileDescriptor, Bitmap> fileDescriptorDecoder) {
        this.streamDecoder = streamDecoder;
        this.fileDescriptorDecoder = fileDescriptorDecoder;
    }

    @Override
    public Resource<Bitmap> decode(ImageVideoWrapper source, int width, int height) throws IOException {
        Resource<Bitmap> result = null;
        InputStream is = source.getStream();//【①】
        if (is != null) {
            try {
                result = streamDecoder.decode(is, width, height);//【②】
            } catch (IOException e) {
                if (Log.isLoggable(TAG, Log.VERBOSE)) {
                    Log.v(TAG, "Failed to load image from stream, trying FileDescriptor", e);
                }
            }
        }
        if (result == null) {
            ParcelFileDescriptor fileDescriptor = source.getFileDescriptor();
            if (fileDescriptor != null) {
                result = fileDescriptorDecoder.decode(fileDescriptor, width, height);
            }
        }
        return result;
    }
    ...
}
```

代码并不复杂，在第14行【①】先调用了`source.getStream()`来**获取**到**服务器返回的InputStream**，然后在第17行【②】调用`streamDecoder.decode()`方法进行**解码**。
streamDecode是一个**`StreamBitmapDecoder`**对象，那么我们再来看这个类的源码，如下所示：

```java
/* StreamBitmapDecoder 源码 */
public class StreamBitmapDecoder implements ResourceDecoder<InputStream, Bitmap> {
    ...
    private final Downsampler downsampler;
    private BitmapPool bitmapPool;
    private DecodeFormat decodeFormat;
    public StreamBitmapDecoder(Downsampler downsampler, BitmapPool bitmapPool, DecodeFormat decodeFormat) {
        this.downsampler = downsampler;
        this.bitmapPool = bitmapPool;
        this.decodeFormat = decodeFormat;
    }

    @Override
    public Resource<Bitmap> decode(InputStream source, int width, int height) {
        Bitmap bitmap = downsampler.decode(source, bitmapPool, width, height, decodeFormat);
        return BitmapResource.obtain(bitmap, bitmapPool);//【①】
    }
    ...
}
```

可以看到，它的decode()方法又去调用了**`Downsampler`**的decode()方法。接下来又到了激动人心的时刻了，Downsampler的代码如下所示：

```
/* Downsampler 源码 */
public abstract class Downsampler implements BitmapDecoder<InputStream> {
    ...
    @Override
    public Bitmap decode(InputStream is, BitmapPool pool, int outWidth, int outHeight, DecodeFormat decodeFormat) {
        final ByteArrayPool byteArrayPool = ByteArrayPool.get();
        final byte[] bytesForOptions = byteArrayPool.getBytes();
        final byte[] bytesForStream = byteArrayPool.getBytes();
        final BitmapFactory.Options options = getDefaultOptions();
        // Use to fix the mark limit to avoid allocating buffers that fit entire images.
        RecyclableBufferedInputStream bufferedStream = new RecyclableBufferedInputStream(
                is, bytesForStream);
        // Use to retrieve exceptions thrown while reading.
        // TODO(#126): when the framework no longer returns partially decoded Bitmaps or provides a way to determine
        // if a Bitmap is partially decoded, consider removing.
        ExceptionCatchingInputStream exceptionStream =
                ExceptionCatchingInputStream.obtain(bufferedStream);
        // Use to read data.
        // Ensures that we can always reset after reading an image header so that we can still attempt to decode the
        // full image even when the header decode fails and/or overflows our read buffer. See #283.
        MarkEnforcingInputStream invalidatingStream = new MarkEnforcingInputStream(exceptionStream);
        try {
            exceptionStream.mark(MARK_POSITION);
            int orientation = 0;
            try {
                orientation = new ImageHeaderParser(exceptionStream).getOrientation();
            } catch (IOException e) {
                if (Log.isLoggable(TAG, Log.WARN)) {
                    Log.w(TAG, "Cannot determine the image orientation from header", e);
                }
            } finally {
                try {
                    exceptionStream.reset();
                } catch (IOException e) {
                    if (Log.isLoggable(TAG, Log.WARN)) {
                        Log.w(TAG, "Cannot reset the input stream", e);
                    }
                }
            }
            options.inTempStorage = bytesForOptions;
            final int[] inDimens = getDimensions(invalidatingStream, bufferedStream, options);
            final int inWidth = inDimens[0];
            final int inHeight = inDimens[1];
            final int degreesToRotate = TransformationUtils.getExifOrientationDegrees(orientation);
            final int sampleSize = getRoundedSampleSize(degreesToRotate, inWidth, inHeight, outWidth, outHeight);
            final Bitmap downsampled =
                    downsampleWithSize(invalidatingStream, bufferedStream, options, pool, inWidth, inHeight, sampleSize,
                            decodeFormat);
            // BitmapFactory swallows exceptions during decodes and in some cases when inBitmap is non null, may catch
            // and log a stack trace but still return a non null bitmap. To avoid displaying partially decoded bitmaps,
            // we catch exceptions reading from the stream in our ExceptionCatchingInputStream and throw them here.
            final Exception streamException = exceptionStream.getException();
            if (streamException != null) {
                throw new RuntimeException(streamException);
            }
            Bitmap rotated = null;
            if (downsampled != null) {
                rotated = TransformationUtils.rotateImageExif(downsampled, pool, orientation);
                if (!downsampled.equals(rotated) && !pool.put(downsampled)) {
                    downsampled.recycle();
                }
            }
            return rotated;
        } finally {
            byteArrayPool.releaseBytes(bytesForOptions);
            byteArrayPool.releaseBytes(bytesForStream);
            exceptionStream.release();
            releaseOptions(options);
        }
    }

    private Bitmap downsampleWithSize(MarkEnforcingInputStream is, RecyclableBufferedInputStream  bufferedStream,
            BitmapFactory.Options options, BitmapPool pool, int inWidth, int inHeight, int sampleSize,
            DecodeFormat decodeFormat) {
        // Prior to KitKat, the inBitmap size must exactly match the size of the bitmap we're decoding.
        Bitmap.Config config = getConfig(is, decodeFormat);
        options.inSampleSize = sampleSize;
        options.inPreferredConfig = config;
        if ((options.inSampleSize == 1 || Build.VERSION_CODES.KITKAT <= Build.VERSION.SDK_INT) && shouldUsePool(is)) {
            int targetWidth = (int) Math.ceil(inWidth / (double) sampleSize);
            int targetHeight = (int) Math.ceil(inHeight / (double) sampleSize);
            // BitmapFactory will clear out the Bitmap before writing to it, so getDirty is safe.
            setInBitmap(options, pool.getDirty(targetWidth, targetHeight, config));
        }
        return decodeStream(is, bufferedStream, options);
    }

    /**
     * A method for getting the dimensions of an image from the given InputStream.
     *
     * @param is The InputStream representing the image.
     * @param options The options to pass to
     *          {@link BitmapFactory#decodeStream(InputStream, android.graphics.Rect,
     *              BitmapFactory.Options)}.
     * @return an array containing the dimensions of the image in the form {width, height}.
     */
    public int[] getDimensions(MarkEnforcingInputStream is, RecyclableBufferedInputStream bufferedStream,
            BitmapFactory.Options options) {
        options.inJustDecodeBounds = true;
        decodeStream(is, bufferedStream, options);
        options.inJustDecodeBounds = false;
        return new int[] { options.outWidth, options.outHeight };
    }

    private static Bitmap decodeStream(MarkEnforcingInputStream is, RecyclableBufferedInputStream bufferedStream,
            BitmapFactory.Options options) {
         if (options.inJustDecodeBounds) {
             // This is large, but jpeg headers are not size bounded so we need something large enough to minimize
             // the possibility of not being able to fit enough of the header in the buffer to get the image size so
             // that we don't fail to load images. The BufferedInputStream will create a new buffer of 2x the
             // original size each time we use up the buffer space without passing the mark so this is a maximum
             // bound on the buffer size, not a default. Most of the time we won't go past our pre-allocated 16kb.
             is.mark(MARK_POSITION);
         } else {
             // Once we've read the image header, we no longer need to allow the buffer to expand in size. To avoid
             // unnecessary allocations reading image data, we fix the mark limit so that it is no larger than our
             // current buffer size here. See issue #225.
             bufferedStream.fixMarkLimit();
         }
        final Bitmap result = BitmapFactory.decodeStream(is, null, options);
        try {
            if (options.inJustDecodeBounds) {
                is.reset();
            }
        } catch (IOException e) {
            if (Log.isLoggable(TAG, Log.ERROR)) {
                Log.e(TAG, "Exception loading inDecodeBounds=" + options.inJustDecodeBounds
                        + " sample=" + options.inSampleSize, e);
            }
        }

        return result;
    }

    ...
}
```

可以看到，**对服务器返回的InputStream的读取，以及对图片的加载全都在这里**了。
当然这里其实处理了很多的逻辑，包括对**图片的压缩**，甚至还有**旋转**、**圆角等**逻辑处理，但是我们目前只需要关注主线逻辑就行了。`decode()`方法执行之后，会**返回**一个**Bitmap**对象，那么图片在这里其实也就已经被加载出来了，剩下的工作就是如果让这个Bitmap显示到界面上，我们继续往下分析。

回到刚才的**StreamBitmapDecoder**当中，你会发现，它的decode()方法**返回**的是一个`Resource<Bitmap>`对象。而我们从Downsampler中得到的是一个Bitmap对象，因此这里在第18行【①】又调用了**`BitmapResource.obtain()`**方法，将Bitmap对象包装成了Resource<Bitmap>对象。代码如下所示：

``` java
/* BitmapResource 源码 */
public class BitmapResource implements Resource<Bitmap> {
    private final Bitmap bitmap;
    private final BitmapPool bitmapPool;

    /**
     * Returns a new {@link BitmapResource} wrapping the given {@link Bitmap} if the Bitmap is non-null or null if the
     * given Bitmap is null.
     *
     * @param bitmap A Bitmap.
     * @param bitmapPool A non-null {@link BitmapPool}.
     */
    public static BitmapResource obtain(Bitmap bitmap, BitmapPool bitmapPool) {
        if (bitmap == null) {
            return null;
        } else {
            return new BitmapResource(bitmap, bitmapPool);
        }
    }

    public BitmapResource(Bitmap bitmap, BitmapPool bitmapPool) {
        if (bitmap == null) {
            throw new NullPointerException("Bitmap must not be null");
        }
        if (bitmapPool == null) {
            throw new NullPointerException("BitmapPool must not be null");
        }
        this.bitmap = bitmap;
        this.bitmapPool = bitmapPool;
    }

    @Override
    public Bitmap get() {
        return bitmap;
    }

    @Override
    public int getSize() {
        return Util.getBitmapByteSize(bitmap);
    }

    @Override
    public void recycle() {
        if (!bitmapPool.put(bitmap)) {
            bitmap.recycle();
        }
    }
}
```

BitmapResource的源码也非常简单，经过这样一层包装之后，如果我还需要获取Bitmap，只需要调用Resource<Bitmap>的get()方法就可以了。

然后我们需要一层层继续向上返回，StreamBitmapDecoder会将值返回到ImageVideoBitmapDecoder当中，而ImageVideoBitmapDecoder又会**将值返回到GifBitmapWrapperResourceDecoder的decodeBitmapWrapper()方法当中**。
由于代码隔得有点太远了，我重新把decodeBitmapWrapper()方法的代码贴一下：

``` java
/* GifBitmapWrapper 源码 */
private GifBitmapWrapper decodeBitmapWrapper(ImageVideoWrapper toDecode, int width, int height) throws IOException {
    GifBitmapWrapper result = null;
    Resource<Bitmap> bitmapResource = bitmapDecoder.decode(toDecode, width, height);
    if (bitmapResource != null) {
        result = new GifBitmapWrapper(bitmapResource, null);//【①】
    }
    return result;
}12345678

```

可以看到，decodeBitmapWrapper()方法返回的是一个GifBitmapWrapper对象。因此，这里在第5行【①】，又将`Resource<Bitmap>`封装到了一个**`GifBitmapWrapper`**对象当中。这个GifBitmapWrapper顾名思义，就是**既能封装GIF**，**又能封装Bitmap**，从而保证了不管是什么类型的图片Glide都能从容应对。我们顺便来看下GifBitmapWrapper的源码吧，如下所示：

```java
/* GifBitmapWrapper 源码 */
public class GifBitmapWrapper {
    private final Resource<GifDrawable> gifResource;
    private final Resource<Bitmap> bitmapResource;

    public GifBitmapWrapper(Resource<Bitmap> bitmapResource, Resource<GifDrawable> gifResource) {
        if (bitmapResource != null && gifResource != null) {
            throw new IllegalArgumentException("Can only contain either a bitmap resource or a gif resource, not both");
        }
        if (bitmapResource == null && gifResource == null) {
            throw new IllegalArgumentException("Must contain either a bitmap resource or a gif resource");
        }
        this.bitmapResource = bitmapResource;
        this.gifResource = gifResource;
    }

    /**
     * Returns the size of the wrapped resource.
     */
    public int getSize() {
        if (bitmapResource != null) {
            return bitmapResource.getSize();
        } else {
            return gifResource.getSize();
        }
    }

    /**
     * Returns the wrapped {@link Bitmap} resource if it exists, or null.
     */
    public Resource<Bitmap> getBitmapResource() {
        return bitmapResource;
    }

    /**
     * Returns the wrapped {@link GifDrawable} resource if it exists, or null.
     */
    public Resource<GifDrawable> getGifResource() {
        return gifResource;
    }
}
```

还是比较简单的，就是分别对gifResource和bitmapResource做了一层封装而已，相信没有什么解释的必要。

然后这个GifBitmapWrapper对象会一直向上返回，返回到GifBitmapWrapperResourceDecoder最外层的decode()方法的时候，会对它再做一次封装，如下所示：

```java
/* GifBitmapWrapperResourceDecoder 源码 */
@Override
public Resource<GifBitmapWrapper> decode(ImageVideoWrapper source, int width, int height) throws IOException {
    ByteArrayPool pool = ByteArrayPool.get();
    byte[] tempBytes = pool.getBytes();
    GifBitmapWrapper wrapper = null;
    try {
        wrapper = decode(source, width, height, tempBytes);
    } finally {
        pool.releaseBytes(tempBytes);
    }
    return wrapper != null ? new GifBitmapWrapperResource(wrapper) : null;//【①】
}
```

可以看到，这里在第11行【①】，又将GifBitmapWrapper封装到了一个GifBitmapWrapperResource对象当中，最终返回的是一个Resource<GifBitmapWrapper>对象。这个GifBitmapWrapperResource和刚才的BitmapResource是相似的，它们都实现的Resource接口，都可以通过get()方法来获取封装起来的具体内容。

**`GifBitmapWrapperResource`**的源码如下所示：

``` java
/* GifBitmapWrapperResource 源码 */
public class GifBitmapWrapperResource implements Resource<GifBitmapWrapper> {
    private final GifBitmapWrapper data;

    public GifBitmapWrapperResource(GifBitmapWrapper data) {
        if (data == null) {
            throw new NullPointerException("Data must not be null");
        }
        this.data = data;
    }

    @Override
    public GifBitmapWrapper get() {
        return data;
    }

    @Override
    public int getSize() {
        return data.getSize();
    }

    @Override
    public void recycle() {
        Resource<Bitmap> bitmapResource = data.getBitmapResource();
        if (bitmapResource != null) {
            bitmapResource.recycle();
        }
        Resource<GifDrawable> gifDataResource = data.getGifResource();
        if (gifDataResource != null) {
            gifDataResource.recycle();
        }
    }
}
```

经过这一层的封装之后，我们从网络上得到的图片就能够以Resource接口的形式返回，并且还能同时处理Bitmap图片和GIF图片这两种情况。

那么现在我们可以回到**DecodeJob**当中了，它的**decodeFromSourceData()**方法**返回**的是一个`Resource<T>`对象，其实也**就是**`Resource<GifBitmapWrapper>对象`了。

然后继续向上返回，最终返回到decodeFromSource()方法当中，如下所示：

``` java
/* DecodeJob 源码 */
public Resource<Z> decodeFromSource() throws Exception {
    Resource<T> decoded = decodeSource();
    return transformEncodeAndTranscode(decoded);
}
```

刚才我们就是从这里跟进到decodeSource()方法当中，然后执行了一大堆一大堆的逻辑，最终得到了这个Resource<T>对象。然而你会发现，decodeFromSource()方法最终返回的却是一个Resource<Z>对象，那么这到底是怎么回事呢？我们就需要跟进到**`transformEncodeAndTranscode()`**方法来瞧一瞧了，代码如下所示：

``` java
/* DecodeJob 源码 */
private Resource<Z> transformEncodeAndTranscode(Resource<T> decoded) {
    long startTime = LogTime.getLogTime();
    Resource<T> transformed = transform(decoded);
    if (Log.isLoggable(TAG, Log.VERBOSE)) {
        logWithTimeAndKey("Transformed resource from source", startTime);
    }
    writeTransformedToCache(transformed);
    startTime = LogTime.getLogTime();
    Resource<Z> result = transcode(transformed);//【①】
    if (Log.isLoggable(TAG, Log.VERBOSE)) {
        logWithTimeAndKey("Transcoded transformed from source", startTime);
    }
    return result;
}

private Resource<Z> transcode(Resource<T> transformed) {
    if (transformed == null) {
        return null;
    }
    return transcoder.transcode(transformed);//【②】
}
```

首先，这个【①】方法开头的几行transform还有cache，这都是我们后面才会学习的东西，现在不用管它们就可以了。需要注意的是第9行，这里调用了一个`transcode()`方法，就把`Resource<T>`对象**转换成**`Resource<Z>`对象了。

而transcode()方法中【②】又是调用了`transcoder.transcode()`方法，那么这个transcoder是什么呢？
在第二步load()方法返回的那个DrawableTypeRequest对象，它的构建函数中去构建了一个FixedLoadProvider对象，然后我们将三个参数传入到了FixedLoadProvider当中，其中就有一个GifBitmapWrapperDrawableTranscoder对象。【见loadProvider来源图解】
后来在onSizeReady()方法中获取到了这个参数，并传递到了Engine当中，然后又由Engine传递到了DecodeJob当中。
因此，这里的**transcoder**其实**就是**这个**GifBitmapWrapperDrawableTranscoder**对象。那么我们来看一下它的源码：

``` java
/* GifBitmapWrapperDrawableTranscoder 源码 */
public class GifBitmapWrapperDrawableTranscoder implements ResourceTranscoder<GifBitmapWrapper, GlideDrawable> {
    private final ResourceTranscoder<Bitmap, GlideBitmapDrawable> bitmapDrawableResourceTranscoder;

    public GifBitmapWrapperDrawableTranscoder(
            ResourceTranscoder<Bitmap, GlideBitmapDrawable> bitmapDrawableResourceTranscoder) {
        this.bitmapDrawableResourceTranscoder = bitmapDrawableResourceTranscoder;
    }

    @Override
    public Resource<GlideDrawable> transcode(Resource<GifBitmapWrapper> toTranscode) {
        GifBitmapWrapper gifBitmap = toTranscode.get();
        Resource<Bitmap> bitmapResource = gifBitmap.getBitmapResource();
        final Resource<? extends GlideDrawable> result;
        if (bitmapResource != null) {
            result = bitmapDrawableResourceTranscoder.transcode(bitmapResource);//【①】
        } else {
            result = gifBitmap.getGifResource();
        }
        return (Resource<GlideDrawable>) result;
    }
    ...
}
```

这里我来简单解释一下，**GifBitmapWrapperDrawableTranscoder**的**核心作用**就是用来**转码**的。因为GifBitmapWrapper是无法直接显示到ImageView上面的，只有Bitmap或者Drawable才能显示到ImageView上。
因此，这里的transcode()方法先从`Resource<GifBitmapWrapper>`中**取出GifBitmapWrapper**对象，然后再从GifBitmapWrapper中**取出**`Resource<Bitmap>`对象。

接下来做了一个判断：
如果Resource< Bitmap>为空，那么说明此时加载的是**GIF图**，直接调用**getGifResource**()方法将图片**取出**即可，因为Glide用于加载GIF图片是使用的GifDrawable这个类，它本身就是一个Drawable对象了。
而如果Resource< Bitmap>不为空，那么就需要**再**做一次**转码**，将**Bitmap转换成Drawable**对象才行，因为要保证静图和动图的类型一致性，不然逻辑上是不好处理的。

这里在第15行【①】`bitmapDrawableResourceTranscoder.transcode(bitmapResource)`又进行了一次转码，是调用的**`GlideBitmapDrawableTranscoder`**对象的transcode()方法，代码如下所示：

``` java
/* GlideBitmapDrawableTranscoder 源码 */
public class GlideBitmapDrawableTranscoder implements ResourceTranscoder<Bitmap, GlideBitmapDrawable> {
    private final Resources resources;
    private final BitmapPool bitmapPool;

    public GlideBitmapDrawableTranscoder(Context context) {
        this(context.getResources(), Glide.get(context).getBitmapPool());
    }

    public GlideBitmapDrawableTranscoder(Resources resources, BitmapPool bitmapPool) {
        this.resources = resources;
        this.bitmapPool = bitmapPool;
    }

    @Override
    public Resource<GlideBitmapDrawable> transcode(Resource<Bitmap> toTranscode) {
        GlideBitmapDrawable drawable = new GlideBitmapDrawable(resources, toTranscode.get());
        return new GlideBitmapDrawableResource(drawable, bitmapPool);
    }
    ...
}
```

可以看到，这里new出了一个GlideBitmapDrawable对象，并把Bitmap封装到里面。然后对GlideBitmapDrawable再进行一次封装，返回一个`Resource<GlideBitmapDrawable>`对象。

现在再返回到GifBitmapWrapperDrawableTranscoder的transcode()方法中，你会发现它们的类型就一致了。因为不管是静图的`Resource<GlideBitmapDrawable>`对象，还是动图的`Resource<GifDrawable>`对象，它们都是属于父类`Resource<GlideDrawable>`对象的。因此transcode()方法也是直接返回了`Resource<GlideDrawable>`，而这个Resource< GlideDrawable>其实也就是转换过后的`Resource<Z>`了。

那么我们继续回到DecodeJob当中，它的decodeFromSource()方法得到了`Resource<Z>`对象，当然也就是`Resource<GlideDrawable>`对象。
然后继续向上返回会回到EngineRunnable的decodeFromSource()方法，再回到decode()方法，再回到run()方法当中。那么我们重新再贴一下EngineRunnable run()方法的源码：

``` java
/* EngineRunnable 源码 */
@Override
public void run() {
    if (isCancelled) {
        return;
    }
    Exception exception = null;
    Resource<?> resource = null;
    try {
        resource = decode();//【①】
    } catch (Exception e) {
        if (Log.isLoggable(TAG, Log.VERBOSE)) {
            Log.v(TAG, "Exception decoding", e);
        }
        exception = e;
    }
    if (isCancelled) {
        if (resource != null) {
            resource.recycle();
        }
        return;
    }
    if (resource == null) {
        onLoadFailed(exception);
    } else {
        onLoadComplete(resource);//【②】
    }
}
```

也就是说，经过第9行【①】decode()方法的执行，我们最终得到了这个`Resource<GlideDrawable>`对象，那么接下来就是如何将它显示出来了。可以看到，这里在第25行【②】调用了onLoadComplete()方法，表示图片加载已经完成了，代码如下所示：

``` java
/* EngineRunnable 源码 */
private void onLoadComplete(Resource resource) {
    manager.onResourceReady(resource);
}
```

这个**manager**就是**`EngineJob`**对象，（Engine.load中创建EngineRunnable时将engineJob赋值给manager）因此这里实际上调用的是EngineJob的onResourceReady()方法，代码如下所示：

``` java
/* EngineJob 源码 */
class EngineJob implements EngineRunnable.EngineRunnableManager {

    private static final Handler MAIN_THREAD_HANDLER = new Handler(Looper.getMainLooper(), new MainThreadCallback());

    private final List<ResourceCallback> cbs = new ArrayList<ResourceCallback>();

    ...

    public void addCallback(ResourceCallback cb) {
        Util.assertMainThread();
        if (hasResource) {
            cb.onResourceReady(engineResource);
        } else if (hasException) {
            cb.onException(exception);
        } else {
            cbs.add(cb);
        }
    }

    @Override
    public void onResourceReady(final Resource<?> resource) {
        this.resource = resource;
        MAIN_THREAD_HANDLER.obtainMessage(MSG_COMPLETE, this).sendToTarget();
    }

    private void handleResultOnMainThread() {
        if (isCancelled) {
            resource.recycle();
            return;
        } else if (cbs.isEmpty()) {
            throw new IllegalStateException("Received a resource without any callbacks to notify");
        }
        engineResource = engineResourceFactory.build(resource, isCacheable);
        hasResource = true;
        engineResource.acquire();
        listener.onEngineJobComplete(key, engineResource);
        for (ResourceCallback cb : cbs) {
            if (!isInIgnoredCallbacks(cb)) {
                engineResource.acquire();
                cb.onResourceReady(engineResource);
            }
        }
        engineResource.release();
    }

    @Override
    public void onException(final Exception e) {
        this.exception = e;
        MAIN_THREAD_HANDLER.obtainMessage(MSG_EXCEPTION, this).sendToTarget();
    }

    private void handleExceptionOnMainThread() {
        if (isCancelled) {
            return;
        } else if (cbs.isEmpty()) {
            throw new IllegalStateException("Received an exception without any callbacks to notify");
        }
        hasException = true;
        listener.onEngineJobComplete(key, null);
        for (ResourceCallback cb : cbs) {
            if (!isInIgnoredCallbacks(cb)) {
                cb.onException(exception);
            }
        }
    }

    private static class MainThreadCallback implements Handler.Callback {

        @Override
        public boolean handleMessage(Message message) {
            if (MSG_COMPLETE == message.what || MSG_EXCEPTION == message.what) {
                EngineJob job = (EngineJob) message.obj;
                if (MSG_COMPLETE == message.what) {
                    job.handleResultOnMainThread();//【①】
                } else {
                    job.handleExceptionOnMainThread();
                }
                return true;
            }
            return false;
        }
    }
    ...
}
```

可以看到，这里在onResourceReady()方法使用Handler发出了一条MSG_COMPLETE消息，那么在MainThreadCallback的handleMessage()方法中就会收到这条消息。从这里开始，所有的逻辑又回到主线程当中进行了，因为很快就需要更新UI了。

然后在第72行【①】调用了`handleResultOnMainThread()`方法，这个方法中又通过一个循环，调用了所有ResourceCallback的onResourceReady()方法。
那么这个**`ResourceCallback`**是什么呢？答案在addCallback()方法当中，它会向cbs集合中去添加ResourceCallback。
那么这个addCallback()方法又是哪里调用的呢？其实调用的地方我们早就已经看过了，只不过之前没有注意，现在重新来看一下Engine的load()方法，如下所示：

``` java
/* Engine 源码 */
public class Engine implements EngineJobListener,
        MemoryCache.ResourceRemovedListener,
        EngineResource.ResourceListener {

    ...    

    public <T, Z, R> LoadStatus load(Key signature, int width, int height, DataFetcher<T> fetcher,
            DataLoadProvider<T, Z> loadProvider, Transformation<Z> transformation, ResourceTranscoder<Z, R> transcoder, Priority priority, 
            boolean isMemoryCacheable, DiskCacheStrategy diskCacheStrategy, ResourceCallback cb) {

        ...

        EngineJob engineJob = engineJobFactory.build(key, isMemoryCacheable);
        DecodeJob<T, Z, R> decodeJob = new DecodeJob<T, Z, R>(key, width, height, fetcher, loadProvider, transformation,
                transcoder, diskCacheProvider, diskCacheStrategy, priority);
        EngineRunnable runnable = new EngineRunnable(engineJob, decodeJob, priority);
        jobs.put(key, engineJob);
        engineJob.addCallback(cb);//【①】
        engineJob.start(runnable);

        if (Log.isLoggable(TAG, Log.VERBOSE)) {
            logWithTimeAndKey("Started new load", startTime, key);
        }
        return new LoadStatus(cb, engineJob);
    }

    ...
}
```

这次把目光放在第18行【①】`engineJob.addCallback(cb);`，看到了吗？就是在这里调用的EngineJob的addCallback()方法来**注册**的一个**ResourceCallback**。
那么接下来的问题就是，Engine.load()方法的ResourceCallback参数又是谁传过来的呢？这就需要回到GenericRequest的onSizeReady()方法当中了，我们看到ResourceCallback是load()方法的最后一个参数，那么在onSizeReady()方法中调用load()方法时传入的最后一个参数是什么？代码如下所示：

``` java
/* GenericRequest 源码 */
public final class GenericRequest<A, T, Z, R> implements Request, SizeReadyCallback,
        ResourceCallback {
    ...
    @Override
    public void onSizeReady(int width, int height) {
        if (Log.isLoggable(TAG, Log.VERBOSE)) {
            logV("Got onSizeReady in " + LogTime.getElapsedMillis(startTime));
        }
        if (status != Status.WAITING_FOR_SIZE) {
            return;
        }
        status = Status.RUNNING;
        width = Math.round(sizeMultiplier * width);
        height = Math.round(sizeMultiplier * height);
        ModelLoader<A, T> modelLoader = loadProvider.getModelLoader();
        final DataFetcher<T> dataFetcher = modelLoader.getResourceFetcher(model, width, height);
        if (dataFetcher == null) {
            onException(new Exception("Failed to load model: \'" + model + "\'"));
            return;
        }
        ResourceTranscoder<Z, R> transcoder = loadProvider.getTranscoder();
        if (Log.isLoggable(TAG, Log.VERBOSE)) {
            logV("finished setup for calling load in " + LogTime.getElapsedMillis(startTime));
        }
        loadedFromMemoryCache = true;
        loadStatus = engine.load(signature, width, height, dataFetcher, loadProvider, transformation, 
                transcoder, priority, isMemoryCacheable, diskCacheStrategy, this);//【①】
        loadedFromMemoryCache = resource != null;
        if (Log.isLoggable(TAG, Log.VERBOSE)) {
            logV("finished onSizeReady in " + LogTime.getElapsedMillis(startTime));
        }
    }

    ...
}
```

请将目光锁定在第29行【①】的最后一个参数，this。没错，就是this。
**GenericRequest**本身就**实现了ResourceCallback的接口**，因此**EngineJob**的**回调**最终其实就是回调到了**GenericRequest的onResourceReady()**方法当中了，代码如下所示：

``` java
/* GenericRequest 源码 */
public void onResourceReady(Resource<?> resource) {
    if (resource == null) {
        onException(new Exception("Expected to receive a Resource<R> with an object of " + transcodeClass
                + " inside, but instead got null."));
        return;
    }
    Object received = resource.get();
    if (received == null || !transcodeClass.isAssignableFrom(received.getClass())) {
        releaseResource(resource);
        onException(new Exception("Expected to receive an object of " + transcodeClass
                + " but instead got " + (received != null ? received.getClass() : "") + "{" + received + "}"
                + " inside Resource{" + resource + "}."
                + (received != null ? "" : " "
                    + "To indicate failure return a null Resource object, "
                    + "rather than a Resource object containing null data.")
        ));
        return;
    }
    if (!canSetResource()) {
        releaseResource(resource);
        // We can't set the status to complete before asking canSetResource().
        status = Status.COMPLETE;
        return;
    }
    onResourceReady(resource, (R) received);
}

private void onResourceReady(Resource<?> resource, R result) {
    // We must call isFirstReadyResource before setting status.
    boolean isFirstResource = isFirstReadyResource();
    status = Status.COMPLETE;
    this.resource = resource;
    if (requestListener == null || !requestListener.onResourceReady(result, model, target, loadedFromMemoryCache,
            isFirstResource)) {
        GlideAnimation<R> animation = animationFactory.build(loadedFromMemoryCache, isFirstResource);
        target.onResourceReady(result, animation);//【①】
    }
    notifyLoadSuccess();
    if (Log.isLoggable(TAG, Log.VERBOSE)) {
        logV("Resource ready in " + LogTime.getElapsedMillis(startTime) + " size: "
                + (resource.getSize() * TO_MEGABYTE) + " fromCache: " + loadedFromMemoryCache);
    }
}
```

这里有两个onResourceReady()方法，首先在第一个onResourceReady()方法当中，调用`resource.get()`方法**获取**到了封装的**图片**对象，也就是**GlideBitmapDrawable**对象，或者是**GifDrawable**对象。
然后将这个值传入到了第二个onResourceReady()方法当中，并在第36行【①】调用了target.onResourceReady()方法。

那么这个**target**又是什么呢？这个又需要向上翻很久了，在第三步**into()**方法的一开始，我们就分析了在into()方法的最后一行，调用了glide.buildImageViewTarget()方法来构建出一个Target，而这个Target就是一个**`GlideDrawableImageViewTarget`**对象。

那么我们去看GlideDrawableImageViewTarget的源码就可以了，如下所示：

``` java
/* GlideDrawableImageViewTarget 源码 */
public class GlideDrawableImageViewTarget extends ImageViewTarget<GlideDrawable> {
    private static final float SQUARE_RATIO_MARGIN = 0.05f;
    private int maxLoopCount;
    private GlideDrawable resource;

    public GlideDrawableImageViewTarget(ImageView view) {
        this(view, GlideDrawable.LOOP_FOREVER);
    }

    public GlideDrawableImageViewTarget(ImageView view, int maxLoopCount) {
        super(view);
        this.maxLoopCount = maxLoopCount;
    }

    @Override
    public void onResourceReady(GlideDrawable resource, GlideAnimation<? super GlideDrawable> animation) {
        if (!resource.isAnimated()) {
            float viewRatio = view.getWidth() / (float) view.getHeight();
            float drawableRatio = resource.getIntrinsicWidth() / (float) resource.getIntrinsicHeight();
            if (Math.abs(viewRatio - 1f) <= SQUARE_RATIO_MARGIN
                    && Math.abs(drawableRatio - 1f) <= SQUARE_RATIO_MARGIN) {
                resource = new SquaringDrawable(resource, view.getWidth());
            }
        }
        super.onResourceReady(resource, animation);//【①】
        this.resource = resource;
        resource.setLoopCount(maxLoopCount);
        resource.start();
    }

    @Override
    protected void setResource(GlideDrawable resource) {
        view.setImageDrawable(resource);
    }

    @Override
    public void onStart() {
        if (resource != null) {
            resource.start();
        }
    }

    @Override
    public void onStop() {
        if (resource != null) {
            resource.stop();
        }
    }
}
```

在GlideDrawableImageViewTarget的onResourceReady()方法中做了一些**逻辑处理**，包括**如**果是**GIF**图片的话，就调用**resource.start()**方法开始**播放图片**，但是好像并没有看到哪里有将GlideDrawable显示到ImageView上的逻辑。

确实没有，不过父类里面有，这里在第25行【①】调用了super.onResourceReady()方法，GlideDrawableImageViewTarget的父类是ImageViewTarget，我们来看下它的代码吧：

```
public abstract class ImageViewTarget<Z> extends ViewTarget<ImageView, Z> implements GlideAnimation.ViewAdapter {
    ...
    @Override
    public void onResourceReady(Z resource, GlideAnimation<? super Z> glideAnimation) {
        if (glideAnimation == null || !glideAnimation.animate(resource, this)) {
            setResource(resource);
        }
    }
    protected abstract void setResource(Z resource);
}
```

可以看到，在ImageViewTarget的onResourceReady()方法当中调用了setResource()方法，而ImageViewTarget的setResource()方法是一个抽象方法，具体的实现还是在子类那边实现的。

那子类的setResource()方法是怎么实现的呢？回头再来看一下GlideDrawableImageViewTarget的setResource()方法，没错，调用的view.setImageDrawable()方法，而这个view就是ImageView。代码执行到这里，图片终于也就显示出来了。

那么，我们对Glide执行流程的源码分析，到这里也终于结束了。








# 引用
 [Android图片加载框架最全解析（一），Glide的基本用法](http://blog.csdn.net/guolin_blog/article/details/53759439)
 [Android图片加载框架最全解析（二），从源码的角度理解Glide的执行流程](http://blog.csdn.net/guolin_blog/article/details/53939176)
 [Android图片加载框架最全解析（三），深入探究Glide的缓存机制](http://blog.csdn.net/guolin_blog/article/details/54895665)
[Android图片加载框架最全解析（四），玩转Glide的回调与监听](http://blog.csdn.net/guolin_blog/article/details/70215985)
 [Android图片加载框架最全解析（五），Glide强大的图片变换功能](http://blog.csdn.net/guolin_blog/article/details/71524668)
 [Android图片加载框架最全解析（六），探究Glide的自定义模块功能](http://blog.csdn.net/guolin_blog/article/details/78179422)
[Android图片加载框架最全解析（七），实现带进度的Glide图片加载功能](http://blog.csdn.net/guolin_blog/article/details/78357251)
[Android图片加载框架最全解析（八），带你全面了解Glide 4的用法](http://blog.csdn.net/guolin_blog/article/details/78582548)