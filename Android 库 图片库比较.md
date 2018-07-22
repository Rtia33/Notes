<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [【Android 库 图片库比较】](#android-%E5%BA%93-%E5%9B%BE%E7%89%87%E5%BA%93%E6%AF%94%E8%BE%83)
- [四大图片库比较](#%E5%9B%9B%E5%A4%A7%E5%9B%BE%E7%89%87%E5%BA%93%E6%AF%94%E8%BE%83)
  - [四大图片缓存基本信息](#%E5%9B%9B%E5%A4%A7%E5%9B%BE%E7%89%87%E7%BC%93%E5%AD%98%E5%9F%BA%E6%9C%AC%E4%BF%A1%E6%81%AF)
  - [基本概念](#%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5)
  - [共同优点](#%E5%85%B1%E5%90%8C%E4%BC%98%E7%82%B9)
  - [ImageLoader](#imageloader)
    - [总体设计及流程](#%E6%80%BB%E4%BD%93%E8%AE%BE%E8%AE%A1%E5%8F%8A%E6%B5%81%E7%A8%8B)
    - [ImageLoader 优点](#imageloader-%E4%BC%98%E7%82%B9)
    - [缺点](#%E7%BC%BA%E7%82%B9)
  - [Picasso](#picasso)
    - [总体设计及流程](#%E6%80%BB%E4%BD%93%E8%AE%BE%E8%AE%A1%E5%8F%8A%E6%B5%81%E7%A8%8B-1)
    - [Picasso 优点](#picasso-%E4%BC%98%E7%82%B9)
    - [缺点](#%E7%BC%BA%E7%82%B9-1)
  - [Glide](#glide)
    - [总体设计及流程](#%E6%80%BB%E4%BD%93%E8%AE%BE%E8%AE%A1%E5%8F%8A%E6%B5%81%E7%A8%8B-2)
    - [2\. Glide 优点](#2%5C-glide-%E4%BC%98%E7%82%B9)
  - [Fresco](#fresco)
    - [优点](#%E4%BC%98%E7%82%B9)
    - [缺点](#%E7%BC%BA%E7%82%B9-2)
  - [汇总](#%E6%B1%87%E6%80%BB)
- [Glide vs Picasso](#glide-vs-picasso)
  - [导入库](#%E5%AF%BC%E5%85%A5%E5%BA%93)
  - [基础](#%E5%9F%BA%E7%A1%80)
  - [默认Bitmap格式是RGB_565](#%E9%BB%98%E8%AE%A4bitmap%E6%A0%BC%E5%BC%8F%E6%98%AFrgb_565)
    - [将Glide Bitmap格式设为ARGB_8888](#%E5%B0%86glide-bitmap%E6%A0%BC%E5%BC%8F%E8%AE%BE%E4%B8%BAargb_8888)
  - [内存开销对比](#%E5%86%85%E5%AD%98%E5%BC%80%E9%94%80%E5%AF%B9%E6%AF%94)
  - [Image质量的细节](#image%E8%B4%A8%E9%87%8F%E7%9A%84%E7%BB%86%E8%8A%82)
  - [磁盘缓存](#%E7%A3%81%E7%9B%98%E7%BC%93%E5%AD%98)
  - [特性](#%E7%89%B9%E6%80%A7)
  - [Glide可以做而Picasso 做不到](#glide%E5%8F%AF%E4%BB%A5%E5%81%9A%E8%80%8Cpicasso-%E5%81%9A%E4%B8%8D%E5%88%B0)
  - [配置](#%E9%85%8D%E7%BD%AE)
  - [库的大小](#%E5%BA%93%E7%9A%84%E5%A4%A7%E5%B0%8F)
  - [总结](#%E6%80%BB%E7%BB%93)
- [引用](#%E5%BC%95%E7%94%A8)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->



# 【Android 库 图片库比较】

# 四大图片库比较
## 四大图片缓存基本信息

![image](http://upload-images.jianshu.io/upload_images/9028834-dbb8526032cd9c6f.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**Universal ImageLoader** 是很早开源的图片缓存，在早期被很多应用使用。

**Picasso** 是 Square 开源的项目，且他的主导者是 JakeWharton，所以广为人知。

**Glide** 是 Google 员工的开源项目。

**Fresco** 是 Facebook 的开源的图片缓存

## 基本概念

在正式对比前，先了解几个图片缓存通用的概念：

1.  **RequestManager**：请求**生成和管理模块**；
2.  **Engine**：**引擎**部分，负责**创建任务**（获取数据），并**调度**执行；
3.  **GetDataInterface**：**数据获取**接口，负责从各个数据源获取数据。比如 MemoryCache 从内存缓存获取数据、DiskCache 从本地缓存获取数据，下载器从网络获取数据等。
4.  **Displayer**：资源（图片）**显示**器，用于显示或操作资源。 比如 ImageView，这几个图片缓存都不仅仅支持 ImageView，同时支持其他 View 以及虚拟的 Displayer 概念。
5.  **Processor** 资源（图片）**处理**器， 负责处理资源，比如旋转、压缩、截取等。

以上概念的称呼在不同图片缓存中可能不同，比如 Displayer 在 ImageLoader 中叫做 ImageAware，在 Picasso 和 Glide 中叫做 Target。

## 共同优点

**1\. 使用简单** 
都可以通过一句代码可实现图片获取和显示。

**2\. 可配置度高，自适应程度高** 
图片缓存的下载器（重试机制）、解码器、显示器、处理器、内存缓存、本地缓存、线程池、缓存算法等大都可轻松配置。

自适应程度高，根据系统性能初始化缓存配置、系统信息变更后动态调整策略。 
比如根据 CPU 核数确定最大并发数，根据可用内存确定内存缓存大小，网络状态变化时调整最大并发数等。

**3\. 多级缓存** 
都至少有两级缓存、提高图片加载速度。

**4\. 支持多种数据源** 
支持多种数据源，网络、本地、资源、Assets 等

**5\. 支持多种 Displayer** 
不仅仅支持 ImageView，同时支持其他 View 以及虚拟的 Displayer 概念。

其他小的共同点包括支持动画、支持 transform 处理、获取 EXIF 信息等。

## ImageLoader


### 总体设计及流程
![](https://upload-images.jianshu.io/upload_images/9028834-cc780c4cd84b1c01.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上面是 ImageLoader 的总体设计图。整个库分为 ImageLoaderEngine，Cache 及 ImageDownloader，ImageDecoder，BitmapDisplayer，BitmapProcessor 五大模块，其中 Cache 分为 MemoryCache 和 DiskCache 两部分。

简单的讲就是 ImageLoader 收到加载及显示图片的任务，并将它交给 ImageLoaderEngine，ImageLoaderEngine 分发任务到具体线程池去执行，任务通过 Cache 及 ImageDownloader 获取图片，中间可能经过 BitmapProcessor 和 ImageDecoder 处理，最终转换为Bitmap 交给 BitmapDisplayer 在 ImageAware 中显示。

### ImageLoader 优点

**(1) 支持下载进度监听**

**(2) 可以在 View 滚动中暂停图片加载** 
通过 PauseOnScrollListener 接口可以在 View 滚动中暂停图片加载。

**(3) 默认实现多种内存缓存算法**这几个图片缓存都可以配置缓存算法，不过 ImageLoader 默认实现了较多缓存算法，如 Size 最大先删除、使用最少先删除、最近最少使用、先进先删除、时间最长先删除等。

**(4) 支持本地缓存文件名规则定义**

### 缺点

缺点在于**不支持GIF**图片加载,  缓存机制没有和http的缓存很好的结合, 完全是自己的一套缓存机制

## Picasso

### 总体设计及流程
![](https://upload-images.jianshu.io/upload_images/9028834-bc2540f466a125c1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
上面是 Picasso 的总体设计图。整个库分为 Dispatcher，RequestHandler 及 Downloader，PicassoDrawable 等模块。

Dispatcher 负责分发和处理 Action，包括提交、暂停、继续、取消、网络状态变化、重试等等。

简单的讲就是 Picasso 收到加载及显示图片的任务，创建 Request 并将它交给 Dispatcher，Dispatcher 分发任务到具体 RequestHandler，任务通过 MemoryCache 及 Handler(数据获取接口) 获取图片，图片获取成功后通过 PicassoDrawable 显示到 Target 中。

需要注意的是上面 Data 的 File system 部分，Picasso 没有自定义本地缓存的接口，默认使用 http 的本地缓存，API 9 以上使用 okhttp，以下使用 Urlconnection，所以如果需要自定义本地缓存就需要重定义 Downloader。

### Picasso 优点

**(1) 自带统计监控功能** 
支持图片缓存使用的监控，包括缓存命中率、已使用内存大小、节省的流量等。

**(2) 支持优先级处理** 
每次任务调度前会选择优先级高的任务，比如 App 页面中 Banner 的优先级高于 Icon 时就很适用。

**(3) 支持延迟到图片尺寸计算完成加载**

**(4) 支持飞行模式、并发线程数根据网络类型而变** 
手机切换到飞行模式或网络类型变换时会自动调整线程池最大并发数，比如 wifi 最大并发为 4， 4g 为 3，3g 为 2。 
这里 Picasso 根据网络类型来决定最大并发数，而不是 CPU 核数。

**(5) “无”本地缓存** 
无”本地缓存，不是说没有本地缓存，而是 Picasso 自己没有实现，交给了 Square 的另外一个网络库 okhttp 去实现，这样的好处是可以通过请求 Response Header 中的 Cache-Control 及 Expired 控制图片的过期时间。

### 缺点

 于**不支持GIF**, 并且它可能是想让服务器去处理图片的缩放, 它缓存的图片是未缩放的, 并且默认使用ARGB_8888格式缓存图片, **缓存体积大**。

## Glide

### 总体设计及流程
![](https://upload-images.jianshu.io/upload_images/9028834-da5573fd6af5b9ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
上面是 Glide 的总体设计图。整个库分为 RequestManager（请求管理器），Engine（数据获取引擎）、Fetcher（数据获取器）、MemoryCache（内存缓存）、DiskLRUCache、Transformation（图片处理）、Encoder（本地缓存存储）、Registry（图片类型及解析器配置）、Target（目标）等模块。

简单的讲就是 Glide 收到加载及显示资源的任务，创建 Request 并将它交给RequestManager，Request 启动 Engine 去数据源获取资源(通过 Fetcher )，获取到后 Transformation 处理后交给 Target。

Glide 依赖于 DiskLRUCache、GifDecoder 等开源库去完成本地缓存和 Gif 图片解码工作。

### 2\. Glide 优点

**(1) 图片缓存->媒体缓存** 
Glide 不仅是一个图片缓存，它支持 Gif、WebP、缩略图。甚至是 Video，所以更该当做一个媒体缓存。

**(2) 支持优先级处理**

**(3) 与 Activity/Fragment 生命周期一致，支持 trimMemory** 
Glide 对每个 context 都保持一个 RequestManager，通过 FragmentTransaction 保持与 Activity/Fragment 生命周期一致，并且有对应的 trimMemory 接口实现可供调用。

**(4) 支持 okhttp、Volley** 
Glide 默认通过 UrlConnection 获取数据，可以配合 okhttp 或是 Volley 使用。实际 ImageLoader、Picasso 也都支持 okhttp、Volley。

**(5) 内存友好** 
① Glide 的内存缓存有个 active 的设计 
从内存缓存中取数据时，不像一般的实现用 get，而是用 remove，再将这个缓存数据放到一个 value 为软引用的 activeResources map 中，并计数引用数，在图片加载完成后进行判断，如果引用计数为空则回收掉。

② 内存缓存更小图片 
Glide 以 url、view*width、view*height、屏幕的分辨率等做为联合 key，将处理后的图片缓存在内存缓存中，而不是原始图片以节省大小

③ 与 Activity/Fragment 生命周期一致，支持 trimMemory

④ 图片默认使用默认 RGB*565 而不是 ARGB*888 
虽然清晰度差些，但图片更小，也可配置到 ARGB_888。

其他：Glide 可以通过 signature 或不使用本地缓存支持 url 过期

## Fresco
**Fresco** 是 Facebook 的开源的图片缓存，主要特点包括：

1.  两个内存缓存加上 Native 缓存构成了三级缓存；
2.  支持流式，可以类似网页上模糊渐进式显示图片；
3.  对多帧动画图片支持更好，如 Gif、WebP。

### 优点

1\. 图片存储在安卓系统的匿名共享内存, 而不是虚拟机的堆内存中, 图片的中间缓冲数据也存放在本地堆内存, 所以, 应用程序有更多的内存使用,不会因为图片加载而导致oom, 同时也减少垃圾回收器频繁调用回收Bitmap导致的界面卡顿,性能更高.

2\. 渐进式加载JPEG图片, 支持图片从模糊到清晰加载

3\. 图片可以以任意的中心点显示在ImageView, 而不仅仅是图片的中心.

4\. JPEG图片改变大小也是在native进行的, 不是在虚拟机的堆内存, 同样减少OOM

5\. 很好的支持GIF图片的显示

### 缺点

1. 框架较大, 影响Apk体积

2. 使用较繁琐



## 汇总

![image](http://upload-images.jianshu.io/upload_images/9028834-cffc840d32a5ce05.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

三者总体上来说，ImageLoader 的功能以及代理容易理解长度都一般。

Picasso 代码虽然只在一个包下，没有严格的包区分，但代码简单、逻辑清晰，一两个小时就能叫深入的了解完。

Glide 功能强大，但代码量大、流转复杂。








# Glide vs Picasso
Glide和Picasso有90%的相似度，准确的说，就是Picasso的克隆版本。但是在细节上还是有不少区别的。

## 导入库

Picasso和Glide都在jcenter上。在项目中添加依赖非常简单：

**Picasso**

```
dependencies {
    compile 'com.squareup.picasso:picasso:2.5.1'
}
```

**Glide**

```
dependencies {
    compile 'com.github.bumptech.glide:glide:3.5.2'
    compile 'com.android.support:support-v4:22.0.0'
}
```

Glide需要依赖**Support Library v4**，别忘了。其实**Support Library v4**已经是应用程序的标配了，这不是什么问题。

## 基础

就如我所说的Glide和Picasso非常相似，Glide加载图片的方法和Picasso如出一辙。

**Picasso**

```
Picasso.with(context)
    .load("http://inthecheesefactory.com/uploads/source/glidepicasso/cover.jpg")
    .into(ivImg);
```

**Glide**

```
Glide.with(context)
    .load("http://inthecheesefactory.com/uploads/source/glidepicasso/cover.jpg")
    .into(ivImg);
```

虽然两者看起来一样，但是Glide更易用，因为Glide的with方法不光接受Context，还接受Activity 和 Fragment，Context会自动的从他们获取。

![with](http://upload-images.jianshu.io/upload_images/9028834-0da6c66fab488c8e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

同时将Activity/Fragment作为with()参数的好处是：**图片加载会和Activity/Fragment的生命周期保持一致**，比如在Paused状态暂停加载，在Resumed的时候又自动重新加载。所以我建议传参的时候传递Activity 和 Fragment给Glide，而不是Context。

## 默认Bitmap格式是RGB_565

下面是加载图片时和Picasso的比较（1920x1080 像素的图片加载到768x432的ImageView中）

![firstload](http://upload-images.jianshu.io/upload_images/9028834-2f72cc73c02922a4.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到Glide加载的图片质量要差于Picasso（ps：我看不出来哈），为什么？这是因为**Glide**默认的Bitmap格式是`RGB_565` ，比`ARGB_8888`格式的**内存开销**要**小**一半。下面是Picasso在ARGB8888下与Glide在RGB565下的内存开销图（应用自身占用了8m，因此以8为基准线比较）：

![ram1_1](http://upload-images.jianshu.io/upload_images/9028834-6c2e59427db177f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 将Glide Bitmap格式设为ARGB_8888
如果你对默认的`RGB_565`效果还比较满意，可以不做任何事，但是如果你觉得难以接受，可以创建一个新的**`GlideModule`**将Bitmap格式转换到`ARGB_8888`：

```java
public class GlideConfiguration implements GlideModule {
 
    @Override
    public void applyOptions(Context context, GlideBuilder builder) {
        // Apply options to the builder here.
        builder.setDecodeFormat(DecodeFormat.PREFER_ARGB_8888);
    }
 
    @Override
    public void registerComponents(Context context, Glide glide) {
        // register ModelLoaders here.
    }
}
```

同时在AndroidManifest.xml中将GlideModule定义为meta-data

```xml
<meta-data android:name="com.inthecheesefactory.lab.glidepicasso.GlideConfiguration"
            android:value="GlideModule"/>
```

![quality2](http://upload-images.jianshu.io/upload_images/9028834-c193251ef49304d1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这样看起来就会好很多。

## 内存开销对比
我们再来看看内存开销图，这次貌似Glide花费了两倍于上次的内存，但是Picasso的内存开销仍然远大于Glide。

![ram2_1](http://upload-images.jianshu.io/upload_images/9028834-52143d02d1a5b610.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

原因在于：
**Picasso**是**加载**了**全尺寸**的图片到内存，然后让GPU来实时重绘大小；
**Glide**加载的大小**和ImageView**的**大小**是**一致**的，因此更小。

当然，Picasso也可以指定加载的图片大小的：

```
Picasso.with(this)
    .load("http://nuuneoi.com/uploads/source/playstore/cover.jpg")
    .resize(768, 432)
    .into(ivImgPicasso);
```

但是问题在于你需要主动计算ImageView的大小，或者说你的ImageView大小是具体的值（而不是wrap_content），你也可以这样：

```
Picasso.with(this)
    .load("http://nuuneoi.com/uploads/source/playstore/cover.jpg")
    .fit()
    .centerCrop()
    .into(ivImgPicasso);
```

现在Picasso的内存开销就和Glide差不多了。

![memory3](http://upload-images.jianshu.io/upload_images/9028834-bf8cfe09b10f5fe4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

虽然内存开销差距不到，但是在这个问题上Glide完胜Picasso。因为Glide可以自动计算出任意情况下的ImageView大小。

## Image质量的细节

这是将ImageView还原到真实大小时的比较。

![quality3](http://upload-images.jianshu.io/upload_images/9028834-d8731c8de9ecfbda.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

你可以看到，Glide加载的图片没有Picasso那么平滑，我还没有找到一个可以直观改变图片大小调整算法的方法。

但是这并不算什么坏事，因为很难察觉。

## 磁盘缓存

Picasso和Glide在磁盘缓存策略上有很大的不同。**Picasso缓存**的是**全尺寸**的，而**Glide**缓存的是**跟ImageView尺寸相同**的。

![cache](http://upload-images.jianshu.io/upload_images/9028834-acf4e95338d4acd2.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上面提到的平滑度的问题依然存在，而且如果加载的是RGB565图片，那么缓存中的图片也是RGB565。

 

我 尝试将ImageView调整成不同大小，但不管大小如何Picasso只缓存一个全尺寸的。Glide则不同，它会为每种大小的ImageView缓存 一次。尽管一张图片已经缓存了一次，但是假如你要在另外一个地方再次以不同尺寸显示，需要重新下载，调整成新尺寸的大小，然后将这个尺寸的也缓存起来。

具体说来就是：假如在第一个页面有一个200x200的ImageView，在第二个页面有一个100x100的ImageView，这两个ImageView本来是要显示同一张图片，却需要下载两次。

不过，你可以改变这种行为，让Glide既缓存全尺寸又缓存其他尺寸：

```
Glide.with(this) 
    .load("http://nuuneoi.com/uploads/source/playstore/cover.jpg") 
    .diskCacheStrategy(DiskCacheStrategy.ALL) 
    .into(ivImgGlide);
```

下次在任何ImageView中加载图片的时候，全尺寸的图片将从缓存中取出，重新调整大小，然后缓存。

**Glide**的这种方式优点是**加载显示非常快**。而Picasso的方式则因为需要在显示之前重新调整大小而导致一些延迟，即便你添加了这段代码来让其立即显示：

```
//Picasso.noFade();
```

![loading3](http://upload-images.jianshu.io/upload_images/9028834-1df716f29e6de0d4.gif?imageMogr2/auto-orient/strip)

Picasso和Glide各有所长，你根据自己的需求选择合适的。

对我而言，我更喜欢Glide，因为它远比Picasso快，虽然需要更大的空间来缓存。

## 特性

你可以做到几乎和Picasso一样多的事情，代码也几乎一样。

**Image Resizing**

```
// Picasso.resize(300, 200); 
// Glide.override(300, 200);
```

**Center Cropping**

```
// Picasso.centerCrop(); 
// Glide.centerCrop();
```

**Transforming**

```
// Picasso.transform(new CircleTransform()) 
// Glide.transform(new CircleTransform(context))
```

设置占位图或者加载错误图：

```
// Picasso.placeholder(R.drawable.placeholder).error(R.drawable.imagenotfound) 
// Glide.placeholder(R.drawable.placeholder).error(R.drawable.imagenotfound)
```

几乎和Picasso一样，从Picasso转换到Glide对你来说就是小菜一碟。

 

## Glide可以做而Picasso 做不到

1. **Glide**可以加载**GIF动态图**，而**Picasso不能**。
  . ![gifanimation2](http://upload-images.jianshu.io/upload_images/9028834-bec9db346ed11fba.gif?imageMogr2/auto-orient/strip)
  同时因为Glide和Activity/Fragment的生命周期是一致的，因此gif的动画也会自动的随着Activity/Fragment的状态暂停、重放。Glide 的缓存在gif这里也是一样，调整大小然后缓存。
  但是从我的一次测试结果来看Glide 动画会消费太多的内存，因此谨慎使用。

2. 除了gif动画之外，**Glide**还可以**将**任何的本地**视频解码成**一张**静态图片**。

3. 还有一个特性是你**可以配置图片显示的动画**，而Picasso只有一种动画：fading in。

4. 最后一个是可以使用thumbnail()产生一个你所加载图片的thumbnail（**缩略图**）。

5. 其实还有一些特性，不过不是非常重要，比如将**图像转换成字节数组**等。

## 配置

有许多可以配置的选项，比如大小，缓存的磁盘位置，最大缓存空间，位图格式等等。可以在这个页面查看这些配置 `Configuration `。

## 库的大小

Picasso (v2.5.1)的大小约118kb，而Glide (v3.5.2)的大小约430kb。

![librarysize](http://upload-images.jianshu.io/upload_images/9028834-68a0d3a729837b92.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Anyway 312KB difference might not be that significant.

不过312kb的差距并不是很重要。

Picasso和Glide的方法个数分别是840和2678个。

![methodcount](http://upload-images.jianshu.io/upload_images/9028834-55b61a7aec254623.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

必须指出，对于DEX文件65535个方法的限制来说，2678是一个相当大的数字了。建议在**使用Glide的时候开启ProGuard**。

## 总结

Glide加载图像以及磁盘缓存的方式都要优于Picasso，速度更快，并且Glide更有利于减少OutOfMemoryError的发生，GIF动画是Glide的杀手锏。
不过Picasso的图片质量更高。

虽然我使用了很长时间的Picasso，但是我得承认现在我更喜欢Glide。我的**建议**是**使用Glide**，但是将**Bitmap格式换成 ARGB_8888**、让**Glide缓存同时缓存全尺寸和改变尺寸两种**。


# 引用
[Google推荐的图片加载库Glide介绍](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0327/2650.html)
[安卓几种图片加载框架的比较](https://blog.csdn.net/u013134722/article/details/56676078)

