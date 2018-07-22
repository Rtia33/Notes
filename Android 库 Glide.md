<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [【Android 库 Glide】](#android-%E5%BA%93-glide)
- [引用](#%E5%BC%95%E7%94%A8)
- [Glide的使用](#glide%E7%9A%84%E4%BD%BF%E7%94%A8)
  - [入门教程](#%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B)
    - [1.添加依赖](#1%E6%B7%BB%E5%8A%A0%E4%BE%9D%E8%B5%96)
    - [2.添加网络权限](#2%E6%B7%BB%E5%8A%A0%E7%BD%91%E7%BB%9C%E6%9D%83%E9%99%90)
    - [3.使用](#3%E4%BD%BF%E7%94%A8)
    - [解析](#%E8%A7%A3%E6%9E%90)
      - [with](#with)
      - [load](#load)
      - [into](#into)
  - [占位图以及加载动画](#%E5%8D%A0%E4%BD%8D%E5%9B%BE%E4%BB%A5%E5%8F%8A%E5%8A%A0%E8%BD%BD%E5%8A%A8%E7%94%BB)
    - [设置占位图(placeholder)](#%E8%AE%BE%E7%BD%AE%E5%8D%A0%E4%BD%8D%E5%9B%BEplaceholder)
    - [设置错误图片(error)](#%E8%AE%BE%E7%BD%AE%E9%94%99%E8%AF%AF%E5%9B%BE%E7%89%87error)
    - [设置动画(crossFade)](#%E8%AE%BE%E7%BD%AE%E5%8A%A8%E7%94%BBcrossfade)
    - [取消动画(dontAnimate)](#%E5%8F%96%E6%B6%88%E5%8A%A8%E7%94%BBdontanimate)
  - [加载本地图片](#%E5%8A%A0%E8%BD%BD%E6%9C%AC%E5%9C%B0%E5%9B%BE%E7%89%87)
    - [1.在清单文件中增加权限](#1%E5%9C%A8%E6%B8%85%E5%8D%95%E6%96%87%E4%BB%B6%E4%B8%AD%E5%A2%9E%E5%8A%A0%E6%9D%83%E9%99%90)
    - [2.加载](#2%E5%8A%A0%E8%BD%BD)
  - [加载gif](#%E5%8A%A0%E8%BD%BDgif)
    - [简单加载](#%E7%AE%80%E5%8D%95%E5%8A%A0%E8%BD%BD)
    - [把gif当作普通图片加载(asBitmap)](#%E6%8A%8Agif%E5%BD%93%E4%BD%9C%E6%99%AE%E9%80%9A%E5%9B%BE%E7%89%87%E5%8A%A0%E8%BD%BDasbitmap)
    - [检查是否gif(asGif)](#%E6%A3%80%E6%9F%A5%E6%98%AF%E5%90%A6gifasgif)
    - [加载本地视频缩略图](#%E5%8A%A0%E8%BD%BD%E6%9C%AC%E5%9C%B0%E8%A7%86%E9%A2%91%E7%BC%A9%E7%95%A5%E5%9B%BE)
    - [其他](#%E5%85%B6%E4%BB%96)
  - [绑定生命周期](#%E7%BB%91%E5%AE%9A%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F)
    - [with](#with-1)
  - [内存缓存与磁盘缓存](#%E5%86%85%E5%AD%98%E7%BC%93%E5%AD%98%E4%B8%8E%E7%A3%81%E7%9B%98%E7%BC%93%E5%AD%98)
    - [缓存的资源](#%E7%BC%93%E5%AD%98%E7%9A%84%E8%B5%84%E6%BA%90)
    - [内存缓存策略(skipMemoryCache)](#%E5%86%85%E5%AD%98%E7%BC%93%E5%AD%98%E7%AD%96%E7%95%A5skipmemorycache)
    - [磁盘缓存策略(diskCacheStrategy)](#%E7%A3%81%E7%9B%98%E7%BC%93%E5%AD%98%E7%AD%96%E7%95%A5diskcachestrategy)
    - [组合策略](#%E7%BB%84%E5%90%88%E7%AD%96%E7%95%A5)
    - [缓存大小及路径](#%E7%BC%93%E5%AD%98%E5%A4%A7%E5%B0%8F%E5%8F%8A%E8%B7%AF%E5%BE%84)
      - [内存缓存最大空间](#%E5%86%85%E5%AD%98%E7%BC%93%E5%AD%98%E6%9C%80%E5%A4%A7%E7%A9%BA%E9%97%B4)
      - [磁盘缓存大小](#%E7%A3%81%E7%9B%98%E7%BC%93%E5%AD%98%E5%A4%A7%E5%B0%8F)
      - [磁盘缓存目录](#%E7%A3%81%E7%9B%98%E7%BC%93%E5%AD%98%E7%9B%AE%E5%BD%95)
    - [清除缓存](#%E6%B8%85%E9%99%A4%E7%BC%93%E5%AD%98)
      - [清除所有缓存](#%E6%B8%85%E9%99%A4%E6%89%80%E6%9C%89%E7%BC%93%E5%AD%98)
      - [清除单个缓存](#%E6%B8%85%E9%99%A4%E5%8D%95%E4%B8%AA%E7%BC%93%E5%AD%98)
  - [通过GlideModule定制Glide](#%E9%80%9A%E8%BF%87glidemodule%E5%AE%9A%E5%88%B6glide)
    - [1.创建一个类实现GlideModule](#1%E5%88%9B%E5%BB%BA%E4%B8%80%E4%B8%AA%E7%B1%BB%E5%AE%9E%E7%8E%B0glidemodule)
    - [2.配置清单文件](#2%E9%85%8D%E7%BD%AE%E6%B8%85%E5%8D%95%E6%96%87%E4%BB%B6)
    - [3.进行自定义配置](#3%E8%BF%9B%E8%A1%8C%E8%87%AA%E5%AE%9A%E4%B9%89%E9%85%8D%E7%BD%AE)
    - [例子(配置默认图片质量)](#%E4%BE%8B%E5%AD%90%E9%85%8D%E7%BD%AE%E9%BB%98%E8%AE%A4%E5%9B%BE%E7%89%87%E8%B4%A8%E9%87%8F)
    - [可以配置多个GlideModule](#%E5%8F%AF%E4%BB%A5%E9%85%8D%E7%BD%AE%E5%A4%9A%E4%B8%AAglidemodule)
    - [自定义内存缓存](#%E8%87%AA%E5%AE%9A%E4%B9%89%E5%86%85%E5%AD%98%E7%BC%93%E5%AD%98)
    - [自定义磁盘缓存](#%E8%87%AA%E5%AE%9A%E4%B9%89%E7%A3%81%E7%9B%98%E7%BC%93%E5%AD%98)
      - [私有缓存](#%E7%A7%81%E6%9C%89%E7%BC%93%E5%AD%98)
      - [外部缓存](#%E5%A4%96%E9%83%A8%E7%BC%93%E5%AD%98)
      - [完全自定义路径](#%E5%AE%8C%E5%85%A8%E8%87%AA%E5%AE%9A%E4%B9%89%E8%B7%AF%E5%BE%84)
  - [图片的压缩](#%E5%9B%BE%E7%89%87%E7%9A%84%E5%8E%8B%E7%BC%A9)
    - [Android图片显示相关知识](#android%E5%9B%BE%E7%89%87%E6%98%BE%E7%A4%BA%E7%9B%B8%E5%85%B3%E7%9F%A5%E8%AF%86)
      - [图片质量分类](#%E5%9B%BE%E7%89%87%E8%B4%A8%E9%87%8F%E5%88%86%E7%B1%BB)
      - [图片默认质量](#%E5%9B%BE%E7%89%87%E9%BB%98%E8%AE%A4%E8%B4%A8%E9%87%8F)
      - [占用内存](#%E5%8D%A0%E7%94%A8%E5%86%85%E5%AD%98)
    - [图片质量的压缩或者提高](#%E5%9B%BE%E7%89%87%E8%B4%A8%E9%87%8F%E7%9A%84%E5%8E%8B%E7%BC%A9%E6%88%96%E8%80%85%E6%8F%90%E9%AB%98)
    - [指定图片大小(override)](#%E6%8C%87%E5%AE%9A%E5%9B%BE%E7%89%87%E5%A4%A7%E5%B0%8Foverride)
  - [图片预处理(圆角，高斯模糊等)](#%E5%9B%BE%E7%89%87%E9%A2%84%E5%A4%84%E7%90%86%E5%9C%86%E8%A7%92%E9%AB%98%E6%96%AF%E6%A8%A1%E7%B3%8A%E7%AD%89)
    - [1.创建一个类继承BitmapTransformation](#1%E5%88%9B%E5%BB%BA%E4%B8%80%E4%B8%AA%E7%B1%BB%E7%BB%A7%E6%89%BFbitmaptransformation)
    - [2.使用](#2%E4%BD%BF%E7%94%A8)
    - [举例(圆角处理)](#%E4%B8%BE%E4%BE%8B%E5%9C%86%E8%A7%92%E5%A4%84%E7%90%86)
      - [1 自定义Transformation](#1-%E8%87%AA%E5%AE%9A%E4%B9%89transformation)
        - [2 使用](#2-%E4%BD%BF%E7%94%A8)
    - [使用多个transform](#%E4%BD%BF%E7%94%A8%E5%A4%9A%E4%B8%AAtransform)
    - [三方库](#%E4%B8%89%E6%96%B9%E5%BA%93)
  - [图片的剪裁(ScaleType)](#%E5%9B%BE%E7%89%87%E7%9A%84%E5%89%AA%E8%A3%81scaletype)
    - [ImageView的ScaleType](#imageview%E7%9A%84scaletype)
      - [效果图](#%E6%95%88%E6%9E%9C%E5%9B%BE)
      - [属性介绍](#%E5%B1%9E%E6%80%A7%E4%BB%8B%E7%BB%8D)
        - [(1)matrix](#1matrix)
        - [(2)center](#2center)
        - [(3)centerInside](#3centerinside)
        - [(4)centerCrop](#4centercrop)
        - [(5)fitCenter(默认)](#5fitcenter%E9%BB%98%E8%AE%A4)
        - [(6)fitStart](#6fitstart)
        - [(7)fitEnd](#7fitend)
        - [(8)fitXY](#8fitxy)
      - [默认是FIT_CENTER](#%E9%BB%98%E8%AE%A4%E6%98%AFfit_center)
    - [Glide配置](#glide%E9%85%8D%E7%BD%AE)
      - [默认策略](#%E9%BB%98%E8%AE%A4%E7%AD%96%E7%95%A5)
      - [fitCenter](#fitcenter)
      - [centerCrop](#centercrop)
      - [效果图对比](#%E6%95%88%E6%9E%9C%E5%9B%BE%E5%AF%B9%E6%AF%94)
      - [注意](#%E6%B3%A8%E6%84%8F)
- [从源码的角度理解Glide的执行流程](#%E4%BB%8E%E6%BA%90%E7%A0%81%E7%9A%84%E8%A7%92%E5%BA%A6%E7%90%86%E8%A7%A3glide%E7%9A%84%E6%89%A7%E8%A1%8C%E6%B5%81%E7%A8%8B)
  - [如何阅读源码](#%E5%A6%82%E4%BD%95%E9%98%85%E8%AF%BB%E6%BA%90%E7%A0%81)
  - [源码下载](#%E6%BA%90%E7%A0%81%E4%B8%8B%E8%BD%BD)
  - [开始阅读](#%E5%BC%80%E5%A7%8B%E9%98%85%E8%AF%BB)
  - [1. Glide.with(..)](#1-glidewith)
    - [1.1 RequestManagerRetriever.get()](#11-requestmanagerretrieverget)
    - [1.2 requestManagerRetriever.get(..)](#12-requestmanagerretrieverget)
  - [2. Glide.with(..).load(..)](#2-glidewithload)
    - [load调用图解](#load%E8%B0%83%E7%94%A8%E5%9B%BE%E8%A7%A3)
    - [2.1 requestManager.load(..)](#21-requestmanagerload)
      - [2.1.1 fromString()](#211-fromstring)
      - [2.1.2 loadGeneric(Class< T >)](#212-loadgenericclass-t-)
      - [2.1.3 DrawableTypeRequest](#213-drawabletyperequest)
    - [2.2 drawableTypeRequest.load(..)](#22-drawabletyperequestload)
      - [2.2.1 drawableRequestBuilder.load(..)](#221-drawablerequestbuilderload)
      - [2.2.2 genericRequestBuilder.load(..)](#222-genericrequestbuilderload)
      - [DrawableRequestBuilder其他方法](#drawablerequestbuilder%E5%85%B6%E4%BB%96%E6%96%B9%E6%B3%95)
  - [3. Glide.with(…).load(…).into(ImageView)](#3-glidewithloadintoimageview)
    - [into调用图解](#into%E8%B0%83%E7%94%A8%E5%9B%BE%E8%A7%A3)
      - [begin调用图解](#begin%E8%B0%83%E7%94%A8%E5%9B%BE%E8%A7%A3)
      - [onSizeReady调用图解](#onsizeready%E8%B0%83%E7%94%A8%E5%9B%BE%E8%A7%A3)
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
- [Glide的缓存机制](#glide%E7%9A%84%E7%BC%93%E5%AD%98%E6%9C%BA%E5%88%B6)
  - [Glide缓存简介](#glide%E7%BC%93%E5%AD%98%E7%AE%80%E4%BB%8B)
  - [缓存Key](#%E7%BC%93%E5%AD%98key)
  - [内存缓存](#%E5%86%85%E5%AD%98%E7%BC%93%E5%AD%98)
    - [禁用内存缓存](#%E7%A6%81%E7%94%A8%E5%86%85%E5%AD%98%E7%BC%93%E5%AD%98)
    - [内存缓存源码分析](#%E5%86%85%E5%AD%98%E7%BC%93%E5%AD%98%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90)
      - [Glide.buildStreamModelLoader()](#glidebuildstreammodelloader)
      - [glideBuilder.createGlide()](#glidebuildercreateglide)
      - [内存缓存读取](#%E5%86%85%E5%AD%98%E7%BC%93%E5%AD%98%E8%AF%BB%E5%8F%96)
      - [loadFromCache()和loadFromActiveResources()](#loadfromcache%E5%92%8Cloadfromactiveresources)
      - [弱引用缓存写入](#%E5%BC%B1%E5%BC%95%E7%94%A8%E7%BC%93%E5%AD%98%E5%86%99%E5%85%A5)
      - [LruCache缓存写入](#lrucache%E7%BC%93%E5%AD%98%E5%86%99%E5%85%A5)
  - [硬盘缓存](#%E7%A1%AC%E7%9B%98%E7%BC%93%E5%AD%98)
    - [diskCacheStrategy](#diskcachestrategy)
    - [硬盘缓存源码分析](#%E7%A1%AC%E7%9B%98%E7%BC%93%E5%AD%98%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90)
      - [读取](#%E8%AF%BB%E5%8F%96)
      - [EngineRunnable.decodeFromCache()](#enginerunnabledecodefromcache)
      - [DecodeJob.loadFromCache(key)](#decodejobloadfromcachekey)
      - [写入](#%E5%86%99%E5%85%A5)
      - [EngineRunnable.decodeFromSource()](#enginerunnabledecodefromsource)
      - [DecodeJob.decodeSource()](#decodejobdecodesource)
      - [缓存图解](#%E7%BC%93%E5%AD%98%E5%9B%BE%E8%A7%A3)
        - [Engine的创建图解](#engine%E7%9A%84%E5%88%9B%E5%BB%BA%E5%9B%BE%E8%A7%A3)
        - [读取内存缓存图解](#%E8%AF%BB%E5%8F%96%E5%86%85%E5%AD%98%E7%BC%93%E5%AD%98%E5%9B%BE%E8%A7%A3)
        - [写入内存缓存图解](#%E5%86%99%E5%85%A5%E5%86%85%E5%AD%98%E7%BC%93%E5%AD%98%E5%9B%BE%E8%A7%A3)
        - [读取硬盘缓存图解](#%E8%AF%BB%E5%8F%96%E7%A1%AC%E7%9B%98%E7%BC%93%E5%AD%98%E5%9B%BE%E8%A7%A3)
        - [写入硬盘缓存图解](#%E5%86%99%E5%85%A5%E7%A1%AC%E7%9B%98%E7%BC%93%E5%AD%98%E5%9B%BE%E8%A7%A3)
  - [高级技巧](#%E9%AB%98%E7%BA%A7%E6%8A%80%E5%B7%A7)

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

[1.Glide-入门教程](http://blog.csdn.net/yulyu/article/details/55053099)
[2.Glide-占位图以及加载动画](http://blog.csdn.net/yulyu/article/details/55054747)
[3.Glide-加载本地图片](http://blog.csdn.net/yulyu/article/details/55056352)
[4.Glide-加载Gif](http://blog.csdn.net/yulyu/article/details/55095407)
[5.Glide-绑定生命周期](http://blog.csdn.net/yulyu/article/details/55096684)
[6.Glide-内存缓存与磁盘缓存](http://blog.csdn.net/yulyu/article/details/55096713)
[7.Glide-通过Modules定制Glide](http://blog.csdn.net/yulyu/article/details/55194237)
[8.Glide-自定义缓存](http://blog.csdn.net/yulyu/article/details/55211250)
[9.Glide-图片的压缩](http://blog.csdn.net/yulyu/article/details/55215718)
[10.Glide-图片预处理(圆角,高斯模糊等)](http://blog.csdn.net/yulyu/article/details/55261351)
[11.Glide-图片的剪裁(ScaleType)](http://blog.csdn.net/yulyu/article/details/55261439)
[12.Glide-源码详解](http://blog.csdn.net/yulyu/article/details/60331803)

现在Android上的图片加载框架非常成熟，从最早的老牌图片加载框架UniversalImageLoader，到后来Google推出的Volley，再到后来的新兴军Glide和Picasso，当然还有Facebook的Fresco。每一个都非常稳定，功能也都十分强大。但是它们的使用场景基本都是重合的，也就是说我们基本只需要选择其中一个来进行学习和使用就足够了，每一个框架都尝试去掌握的话则有些浪费时间。

从易用性上来讲，Glide和Picasso应该都是完胜其他框架的，这两个框架都实在是太简单好用了，大多数情况下加载图片都是一行代码就能解决的，而UniversalImageLoader和Fresco则在这方面略逊一些。

那么再拿Glide和Picasso对比呢，首先这两个框架的用法非常相似，但其实它们各有特色。Picasso比Glide更加简洁和轻量，Glide比Picasso功能更为丰富。之前已经有人对这两个框架进行过全方面的对比，大家如果想了解更多的话可以去参考一下 [Google推荐的图片加载库Glide介绍](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0327/2650.html) 。

# Glide的使用
Glide是一款由Bump Technologies开发的图片加载框架，使得我们可以在Android平台上以极度简单的方式加载和展示图片。
本文基于Glide 3.7.0版本来进行讲解。

## 入门教程

### 1.添加依赖
```
compile 'com.github.bumptech.glide:glide:3.7.0'
```
如果你还在使用Eclipse，可以点击 [这里](http://download.csdn.net/detail/sinyu890807/9781538) 下载Glide的jar包。

### 2.添加网络权限
```
<uses-permission android:name="android.permission.INTERNET"/>
```

### 3.使用
`Glide.with(MainActivity.this).load(url).into(iv);`
```java
public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ImageView  iv = (ImageView)findViewById(R.id.iv);
        String   url = "https://img.gcall.com/dca5/M00/10/8E/wKhoNlggetaENWylAAAAAAAAAAA457.jpg";
        //加载图片
        Glide.with(MainActivity.this).load(url).into(iv);
    }
}
```


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

## 占位图以及加载动画

### 设置占位图(placeholder)
有时候加载的图片过大时,或者网络不好时,我们经常希望控件在加载过程中有一张默认的占位图
```java
Glide.with(this).load(url)
	.placeholder(R.mipmap.place)
	.into(iv);
```

### 设置错误图片(error)
当图片链接地址有误或者网络不行的时候,我们需要显示一个错误图片
```java
Glide.with(this).load(url).placeholder(R.mipmap.place)
	.error(R.mipmap.icon_photo_error)
	.into(iv);
```

### 设置动画(crossFade)
Glide默认是包含淡入淡出动画的时间为300ms(毫秒)，我们可以修改这个动画的时间
```java
Glide.with(this).load(url).placeholder(R.mipmap.place).error(R.mipmap.icon_photo_error)
	.crossFade(5000)
	.into(iv);
```

### 取消动画(dontAnimate)

当我们不希望有淡入淡出动画时

```java
Glide.with(this).load(url).placeholder(R.mipmap.place).error(R.mipmap.icon_photo_error)
	.dontAnimate()
	.into(iv);
```

## 加载本地图片

### 1.在清单文件中增加权限

```
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```

### 2.加载

其实Glide加载本地图片和加载网络图片调用的方法是一样的,唯一的区别是说加载SD卡的图片需要SD卡的权限,加载网络需要网络权限

```java
    //本地文件
    File file = new File(Environment.getExternalStorageDirectory(), "xiayu.png");
    //加载图片
    Glide.with(this).load(file).into(iv);
```

## 加载gif

### 简单加载
如果只是简单加载gif，其实**跟加载普通图片一样**，Glide会自行判断
```java
Glide.with(this).load(mGifUrl).placeholder(R.mipmap.place).error(R.mipmap.icon_photo_error).into(mIv);
```

### 把gif当作普通图片加载(asBitmap)
如果希望加载gif时**只加载gif的第一帧**，把gif当作普通图片一样加载，那么只需要加上asBitmap方法即可

```java
Glide.with(this).load(mGifUrl).asBitmap().placeholder(R.mipmap.place).error(R.mipmap.icon_photo_error).into(mIv);
```

### 检查是否gif(asGif)
如果你希望加载的只是gif，如果**不是gif就显示错误图片**，那么只用加上asGif方法即可

```java
Glide.with(this).load(mGifUrl)
	.asGif()
	.placeholder(R.mipmap.place).error(R.mipmap.icon_photo_error).into(mIv);
```

### 加载本地视频缩略图
网络上很多文章上都是从一篇译文里面拷贝过来的，里面说Glide可以加载本地视频,但是那篇译文漏翻译一句了，Glide只会**加载本地视频的第一帧**，也就是缩略图，而且其实加载缩略图的时候也无需转化为Uri，**直接把File丢进去**就行了
``` java
mVideoFile = new File(Environment.getExternalStorageDirectory(), "xiayu.mp4");
Glide.with(this).load(mVideoFile).placeholder(R.mipmap.place).error(R.mipmap.icon_photo_error).into(mIv);
```

### 其他
在大多数情况下，当你使用diskCacheStrategy（DiskCacheStrategy.**SOURCE**）时，Gif的**加载速度**会显**着提高**(其实就是把gif资源缓存到磁盘)

``` java
Glide.with(this).load(mGifUrl)
	.diskCacheStrategy(DiskCacheStrategy.SOURCE)
	.placeholder(R.mipmap.place).error(R.mipmap.icon_photo_error).into(mIv);
```

## 绑定生命周期

### with

其实Glide与activity和fragment绑定生命周期很简单，只用在**with**的时候**传入想绑定生命周期的Context**就行。

比如通常在MainActivity中传入this,或者MainActivity即可

(在Glide内部会根据你Context的实际类型做不同的处理，具体的分析会在以后的源码分析中展示)

``` java
Glide.with(this).load(mUrl).into(mIv);
```

## 内存缓存与磁盘缓存

### 缓存的资源

Glide的缓存资源分为两种:

- 1.原图(SOURCE) :原始图片
- 2.处理图(RESULT) :经过压缩和变形等处理后的图片

### 内存缓存策略(skipMemoryCache)

Glide**默认**是会在内存中**缓存处理图(RESULT)**的。

可以通过调用**`skipMemoryCache(true)`**来设置**跳过内存缓存**

``` java
    //跳过内存缓存
    Glide.with(this).load(mUrl)
	    .skipMemoryCache(true)
	    .into(mIv);
```

调用skipMemoryCache(false)没有代码上的意义，因为Glide默认就是不跳过内存缓存的，但是显示调用这个方法，可以让别人一目了然的知道你这次请求是会在内存中缓存的，所以还是建议显示调用一下这个方法来表明你的内存缓存策略

### 磁盘缓存策略(diskCacheStrategy)

Glide磁盘缓存策略分为四种，默认的是RESULT(默认值这一点网上很多文章都写错了，但是这一点很重要):

- **1.ALL**:缓存原图(SOURCE)和处理图(RESULT)
- **2.NONE**:什么都不缓存
- **3.SOURCE**:只缓存原图(SOURCE)
- **4.RESULT**:只缓存处理图(RESULT) **—默认值**

### 组合策略

和其他三级缓存一样，Glide的缓存读取顺序是 **内存–>磁盘–>网络**

需要注意的是Glide的**内存缓存**和**磁盘缓存**的配置相互没有直接影响，所以可以**同时进行配置**

例:

**1.内存不缓存，磁盘缓存缓存所有图片**

``` java
Glide.with(this).load(mUrl)
	.skipMemoryCache(true)
	.diskCacheStrategy(DiskCacheStrategy.ALL)
	.into(mIv);
```

**2.内存缓存处理图，磁盘缓存原图**

``` java
Glide.with(this).load(mUrl)
	.skipMemoryCache(false)
	.diskCacheStrategy(DiskCacheStrategy.SOURCE)
	.into(mIv);
```

### 缓存大小及路径

#### 内存缓存最大空间

Glide的内存缓存其实涉及到比较多的计算，这里就介绍最重要的一个参数，就是内存缓存最大空间

内存缓存最大空间(maxSize)=每个进程可用的最大内存 \* 0.4

(低配手机的话是: 每个进程可用的最大内存 \* 0.33)

#### 磁盘缓存大小

磁盘缓存大小: 250 \* 1024 * 1024(250MB)

``` java
/** 250 MB of cache. */
    int DEFAULT_DISK_CACHE_SIZE = 250 * 1024 * 1024;
```
#### 磁盘缓存目录

磁盘缓存目录: 项目/cache/image_manager_disk_cache

``` java
    String DEFAULT_DISK_CACHE_DIR = "image_manager_disk_cache";
```

### 清除缓存

#### 清除所有缓存

清除所有内存缓存(需要在Ui线程操作)

``` java
Glide.get(this).clearMemory();
```

清除所有磁盘缓存(需要在子线程操作)

``` java
Glide.get(MainActivity.this).clearDiskCache();
```

注：在使用中的资源不会被清除

#### 清除单个缓存

由于Glide可能会缓存一张图片的多个分辨率的图片，并且文件名是被哈希过的，所以并不能很好的删除单个资源的缓存，以下是官方文档中的描述

``` java
Because File names are hashed keys, there is no good way to simply delete all of the cached files on disk that
correspond to a particular url or file path. The problem would be simpler if you were only ever allowed to load
or cache the original image, but since Glide also caches thumbnails and provides various transformations, each
of which will result in a new File in the cache, tracking down and deleting every cached version of an image 
is difficult.

In practice, the best way to invalidate a cache file is to change your identifier when the content changes 
(url, uri, file path etc).
```

## 通过GlideModule定制Glide

### 1.创建一个类实现GlideModule

``` java
public class XiayuGlideModule implements GlideModule {
    @Override
    public void applyOptions(Context context, GlideBuilder builder) {
       //TODO
    }
    @Override
    public void registerComponents(Context context, Glide glide) {
       //TODO
    }
}
```

### 2.配置清单文件

在AndroidManifest中配置刚刚创建的GlideModule,需要在application节点下添加

``` xml
<application>
    ...

    <meta-data
        android:name="com.xiayu.xiayuglidedemo.XiayuGlideModule"
        android:value="GlideModule" />
</application>
```

其中android:name就是刚才创建的GlideModule的实现类

### 3.进行自定义配置

刚才创建的GlideModule的实现类时，会要实现两个方法，这里要用到的是其中的**`applyOptions`**方法，applyOptions方法里面提供了一个GlideBuilder，通过GlideBuilder我们就能实现自定义配置了

``` java
public class XiayuGlideModule implements GlideModule {
    @Override
    public void applyOptions(Context context, GlideBuilder builder) {
        builder.setDiskCache();//自定义磁盘缓存

        builder.setMemoryCache();//自定义内存缓存

        builder.setBitmapPool(); //自定义图片池

        builder.setDiskCacheService();//自定义本地缓存的线程池

        builder.setResizeService();//自定义核心处理的线程池

        builder.setDecodeFormat();//自定义图片质量
    }

    @Override
    public void registerComponents(Context context, Glide glide) {
        //TODO
    }
}
```

### 例子(配置默认图片质量)

由于Glide的配置涉及到的东西比较多，在以后的文章里面会对每一种配置做说明具体介绍，这里就先示范一个比较简单的配置，那就是图片质量配置

用过Picasso的朋友应该知道，Picasso默认的图片质量是ARGB_8888,而Glide默认的图片质量是RGB_565，这里我们就来修改默认配置，让Glide的默认质量为ARGB_8888

(ARGB_8888是指32位图，即每个像素占4byte) 
(RGB_565是16位图，即每个像素占2byte)

``` java
public class XiayuGlideModule implements GlideModule {
    @Override
    public void applyOptions(Context context, GlideBuilder builder) {
        //自定义图片质量
        builder.setDecodeFormat(DecodeFormat.PREFER_ARGB_8888);
    }

    @Override
    public void registerComponents(Context context, Glide glide) {
        //TODO
    }
}
```

### 可以配置多个GlideModule

在清单文件中可以配置**多个GlideModule**，Glide会依次遍历并读取

### 自定义内存缓存

通过调用builder的setXXX方法就可以自定义内存缓存的大小了

(**MemoryCach**以及**BitmapPool**都与内存缓存有关)

``` java
public class XiayuGlideModule implements GlideModule {

    @Override
    public void applyOptions(Context context, GlideBuilder builder) {
        //配置内存缓存大小 10MB
        builder.setMemoryCache(new LruResourceCache(10*1024*1024));
        //配置图片池大小   20MB
        builder.setBitmapPool(new LruBitmapPool(20*1024*1024));
    }

    @Override
    public void registerComponents(Context context, Glide glide) {
    }
}
```

但是内存缓存的大小数值其实不应该是随便配置的，Glide的内部的默认值是通过一系列的计算获取的，比如说判断手机是否高配置手机等。

这样有个取巧的办法，就是**获取Glide默认的设置**，**然后**在这个设置的基础上进行**修改**

``` java
public class XiayuGlideModule implements GlideModule {
    @Override
    public void applyOptions(Context context, GlideBuilder builder) {
        //获取内存计算器
        MemorySizeCalculator calculator = new MemorySizeCalculator(context);
        //获取Glide默认内存缓存大小
        int defaultMemoryCacheSize = calculator.getMemoryCacheSize();
        //获取Glide默认图片池大小
        int defaultBitmapPoolSize  = calculator.getBitmapPoolSize();
        //将数值修改为之前的1.1倍
        int myMemoryCacheSize  = (int) (1.1 * defaultMemoryCacheSize);
        int myBitmapPoolSize   = (int) (1.1 * defaultBitmapPoolSize);
        //修改默认值
        builder.setMemoryCache(new LruResourceCache(myMemoryCacheSize));
        builder.setBitmapPool(new LruBitmapPool(myBitmapPoolSize));
    }

    @Override
    public void registerComponents(Context context, Glide glide) {

    }
}
```

### 自定义磁盘缓存

磁盘缓存按照访问权限及路径可以分为两种 
\- **私有缓存** (自己本app可以使用，目录在data/data/应用包名 下面) 
\- **外部缓存**(都可以访问，目录在扩展空间内，如SD卡)

#### 私有缓存
- 修改缓存大小
  下面这样配置的话，主要是**修改**缓存**大小**，缓存路径仍为默认路径(即data/data/应用包名/cache/image_manager_disk_cache)
``` java
public class XiayuGlideModule implements GlideModule {
    @Override
    public void applyOptions(Context context, GlideBuilder builder) {
        //设置磁盘缓存大小
        int size = 100 * 1024 * 1024;
        //设置磁盘缓存
        builder.setDiskCache(new InternalCacheDiskCacheFactory(context, size));
    }

    @Override
    public void registerComponents(Context context, Glide glide) {
    }
}
```

- 修改缓存路径
  如果需求**修改**缓存**路径**的话，需要增加一下参数即可(变为data/data/应用包名/cache/xiayu)
``` java
    //设置磁盘缓存大小
    int size = 100 * 1024 * 1024;
    String dir = "xiayu";
    //设置磁盘缓存
    builder.setDiskCache(new InternalCacheDiskCacheFactory(context,dir, size));
```

#### 外部缓存

下面这样配置的话，缓存会在 SDCard/Android/data/应用包名/cache/image_manager_disk_cache目录下

``` java
public class XiayuGlideModule implements GlideModule {

    @Override
    public void applyOptions(Context context, GlideBuilder builder) {
        //设置磁盘缓存大小
        int size = 100 * 1024 * 1024;
        //设置磁盘缓存
        builder.setDiskCache(new ExternalCacheDiskCacheFactory(context, size));
    }

    @Override
    public void registerComponents(Context context, Glide glide) {
    }
}
```

如果需求修改缓存路径的话，需要增加一下参数即可(变为SDCard/Android/data/应用包名/cache/xiayu)

``` java
    //设置磁盘缓存大小
    int size = 100 * 1024 * 1024;
    String dir = "xiayu";
    //设置磁盘缓存
    builder.setDiskCache(new ExternalCacheDiskCacheFactory(context,dir, size));
```

#### 完全自定义路径

上面两种缓存都是把缓存的父目录定死的，能够改变的只是子目录，Glide还提供了一种方式可以完全自定义缓存目录

(需要注意的是这里的路径是配置的绝对路径，所以如果没有指定在sd卡目录下的话是无法直接看到的)

``` java
public class XiayuGlideModule implements GlideModule {

    @Override
    public void applyOptions(Context context, GlideBuilder builder) {
        //设置磁盘缓存大小
        int size = 100 * 1024 * 1024;
        String dirFolder = "xia";
        String dirName = "yu";
        //设置磁盘缓存
        builder.setDiskCache(new DiskLruCacheFactory(dirFolder, size));

        //builder.setDiskCache(new DiskLruCacheFactory(dirFolder, dirName,size));
    }

    @Override
    public void registerComponents(Context context, Glide glide) {
    }
}
```

## 图片的压缩

### Android图片显示相关知识
#### 图片质量分类
安卓图片显示的质量配置主要分为四种:

- **ARGB_8888** :32位图，带透明度，每个像素占4个字节
- **ARGB_4444** :16位图，带透明度，每个像素占2个字节
- **RGB_565** :16位图，不带透明度，每个像素占2个字节
- **ALPHA_8** :32位图，只有透明度，不带颜色，每个像素占4个字节

(A代表透明度，RGB代表红绿蓝:即颜色)

#### 图片默认质量
Picasso的默认质量是 ARGB_8888 
Glide的默认质量则为 RGB_565

#### 占用内存
加载一张4000 \* 2000(一般手机拍摄的都超过这个像素)的图片

- Picasso需要占用的内存为: 32MB
  4000 \* 2000 * 4 / 1024 / 1024 = 30 (MB)

- Glide需要占用的内存为: 16MB
  4000 \* 2000 * 2 / 1024 / 1024 = 15 (MB)

也就是说只要同时加载几张图片，你的应用就会OOM(内存溢出了)，最恐怖的是就算你的ImageView的宽高只有10px,同样会占用那么多内存，这就是为什么需要做图片压缩的原因了

### 图片质量的压缩或者提高

在Glide里面默认用的是RGB_565，如果需要修改，可通过GlideModule定制【见-通过GlideModule定制Glide】

### 指定图片大小(override)

实际上，使用Glide在绝大多数情况下我们都是不需要指定图片大小的。
我们平时在加载图片的时候很容易会造成**内存浪费**。什么叫内存浪费呢？比如说一张图片的尺寸是1000*1000像素，但是我们界面上的ImageView可能只有200*200像素，这个时候如果你不对图片进行任何压缩就直接读取到内存中，这就属于内存浪费了，因为程序中根本就用不到这么高像素的图片。

关于图片压缩这方面，我之前也翻译过Android官方的一篇文章，感兴趣的朋友可以去阅读一下 [Android高效加载大图、多图解决方案，有效避免程序OOM](http://blog.csdn.net/guolin_blog/article/details/9316683) 。

而使用**Glide**，我们就完全**不用担心**图片**内存浪费**，甚至是内存溢出的问题。因为Glide从来都不会直接将图片的完整尺寸全部加载到内存中，而是**用多少加载多少**。Glide会**自动判断ImageView的大小**，然后**只将这么大的图片像素加载到内存当中**，帮助我们节省内存开支。

当然，Glide也并没有使用什么神奇的魔法，它内部的实现原理其实就是上面那篇文章当中介绍的技术，因此掌握了最基本的实现原理，你也可以自己实现一套这样的图片压缩机制。

也正是因为Glide是如此的智能，所以刚才在开始的时候我就说了，在绝大多数情况下我们都是不需要指定图片大小的，因为Glide会自动根据ImageView的大小来决定图片的大小。

不过，如果你真的有这样的需求，必须给图片指定一个固定的大小，通过调用**`override`**，就可以把图片压缩到相应的尺寸来显示了，类似这些被处理过的图片，就是之前文章中提到的RESULT(处理图)

``` java
Glide.with(this).load(mUrl)
	.override(300,300)
	.into(mIv);
```

注意，这里具体会压缩到什么尺寸还会根据很多条件来计算，所以最终压缩的结果的宽高会比较接近你的传值，但是不一定会完全相同，如果感兴趣的可以期待本系列后面的Glide源码解析中的具体介绍

(一般来说控件是什么尺寸就传入相应的宽高，这样是比较适合的压缩比例)



## 图片预处理(圆角，高斯模糊等)

### 1.创建一个类继承BitmapTransformation

需要实现两个方法，其中transform方法里面能拿到bitmap对象，这里就是对图片做处理的地方

``` java
public  class CornersTransform extends BitmapTransformation {
        @Override
        protected Bitmap transform(BitmapPool pool, Bitmap toTransform, int outWidth, int outHeight) {
            //TODO
        }

        @Override
        public String getId() {
           //TODO
        }
}
```

### 2.使用

通过调用transform方法就能展示处理后的图片

``` java
Glide.with(this).load(url)
	.transform(new CornersTransform())
	.into(iv1);
```

### 举例(圆角处理)
#### 1 自定义Transformation

``` java
public  class CornersTransform extends BitmapTransformation {
    private float radius;

    public CornersTransform(Context context) {
        super(context);
        radius = 10;
    }

    public CornersTransform(Context context, float radius) {
        super(context);
        this.radius = radius;
    }

    @Override
    protected Bitmap transform(BitmapPool pool, Bitmap toTransform, int outWidth, int outHeight) {
        return cornersCrop(pool, toTransform);
    }

    private Bitmap cornersCrop(BitmapPool pool, Bitmap source) {
        if (source == null) return null;

        Bitmap result = pool.get(source.getWidth(), source.getHeight(), Bitmap.Config.ARGB_8888);
        if (result == null) {
            result = Bitmap.createBitmap(source.getWidth(), source.getHeight(), Bitmap.Config.ARGB_8888);
        }

        Canvas canvas = new Canvas(result);
        Paint  paint  = new Paint();
        paint.setShader(new BitmapShader(source, BitmapShader.TileMode.CLAMP, BitmapShader.TileMode.CLAMP));
        paint.setAntiAlias(true);
        RectF rectF = new RectF(0f, 0f, source.getWidth(), source.getHeight());
        canvas.drawRoundRect(rectF, radius, radius, paint);
        return result;
    }

    @Override
    public String getId() {
        return getClass().getName();
    }
}
```

##### 2 使用

``` java
Glide.with(this).load(url).transform(new CornersTransform(this,50)).into(iv1);
```

效果如下:

![](https://upload-images.jianshu.io/upload_images/9028834-30576bb91b2561fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 使用多个transform

transform方法是**不支持多次调用**的，如果你调用了两次，那么第二次的会覆盖了第一次的效果

但是他有一个重载的方法**可以传入多个对象**，这样传入的变形器都能够生效

``` java
Glide.with(this).load(url)
	.transform(new CircleTransform(this),new CornersTransform(this,50))
	.into(iv1);
```

### 三方库

如果你觉得自己自定义transform比较困难，或者你想学习别人的图片处理方法，可以在试一试github上的这个三方库

[Glide Transformations](https://github.com/wasabeef/glide-transformations)

<https://github.com/wasabeef/glide-transformations>

效果(支持圆角，高斯模糊等)
[![gif](https://upload-images.jianshu.io/upload_images/9028834-9a4a6ab1119992c3.gif?imageMogr2/auto-orient/strip)](https://upload-images.jianshu.io/upload_images/9028834-9a4a6ab1119992c3.gif?imageMogr2/auto-orient/strip)



## 图片的剪裁(ScaleType)

讲到图片的剪裁，我们首先要介绍一下ImageView的ScaleType设置效果

### ImageView的ScaleType

ImageView的ScaleType一共有**8种属性**:

#### 效果图

![图片宽高>ImageView](https://upload-images.jianshu.io/upload_images/9028834-d9327c8021307354.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  ![图片宽高<mageView](https://upload-images.jianshu.io/upload_images/9028834-f03bb6e73810efd7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 属性介绍
##### (1)matrix
**不缩放**，图片与控件 **左上角** 对齐，当图片大小超过控件时将 被裁剪

##### (2)center
**不缩放**，图片与控件 **中心点** 对齐，当图片大小超过控件时将 被裁剪

##### (3)centerInside
以**完整显示**图片为目标，**不剪裁**，当**显示不下**的**时**候将**缩放**，能够显示的情况下不缩放

##### (4)centerCrop
以**填满整个控件**为目标，**等比缩放**，超过控件时将被 **裁剪** ( 宽高都要填满，所以只要图片宽高比与控件宽高比不同时，一定会被剪裁)

##### (5)fitCenter(默认)
**自适应**控件， **不剪裁**，在**不超过控件**的前提下，**等比缩放到最大**，**居中**显示

##### (6)fitStart
**自适应**控件， 不剪裁，在不超过控件的前提下，等比缩放到最大，**靠左(上)**显示

##### (7)fitEnd
**自适应**控件， 不剪裁，在不超过控件的前提下，等比缩放到最大，**靠右(下)**显示

##### (8)fitXY
以**填满整个控件**为目标， 不按比例**拉伸**或缩放(可能会变形)，不剪裁


#### 默认是FIT_CENTER
有些文章说默认是matrix,是不正确的，其实**默认是FIT_CENTER**

可以通过ImageView的源码看到默认设置

``` java
private void initImageView() {
    mMatrix = new Matrix();
    mScaleType = ScaleType.FIT_CENTER;

    if (!sCompatDone) {
        final int targetSdkVersion = mContext.getApplicationInfo().targetSdkVersion;
        sCompatAdjustViewBounds = targetSdkVersion <= Build.VERSION_CODES.JELLY_BEAN_MR1;
        sCompatUseCorrectStreamDensity = targetSdkVersion > Build.VERSION_CODES.M;
        sCompatDrawableVisibilityDispatch = targetSdkVersion < Build.VERSION_CODES.N;
        sCompatDone = true;
    }
}
```



### Glide配置

Glide有两个方法可以设置图片剪裁的策略
①**fitCenter()**
②**centerCrop()**

这两个方法其实都是通过调用transform方法来对图片进行处理

#### 默认策略
当你没有调用上述两个方法，并且也没有调用transform方法的时候，在Glide调用into方法时，会根据你设置的ImageView的ScaleType来做处理

``` java
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
```

applyCenterCrop其实还是调用的centerCrop

applyFitCenter其实还是调用的applyFitCenter

通过默认的配置加载图片

``` java
Glide.with(this).load(url).into(iv1);
```

效果跟ImageView设置src一样

#### fitCenter

内部是调用transform方法来处理的，处理代码这边只是展示一下，就不做详细的介绍了，有兴趣的朋友可以研究

``` java
public static Bitmap fitCenter(Bitmap toFit, BitmapPool pool, int width, int height) {
        if (toFit.getWidth() == width && toFit.getHeight() == height) {
            if (Log.isLoggable(TAG, Log.VERBOSE)) {
                Log.v(TAG, "requested target size matches input, returning input");
            }
            return toFit;
        }
        final float widthPercentage = width / (float) toFit.getWidth();
        final float heightPercentage = height / (float) toFit.getHeight();
        final float minPercentage = Math.min(widthPercentage, heightPercentage);

        // take the floor of the target width/height, not round. If the matrix
        // passed into drawBitmap rounds differently, we want to slightly
        // overdraw, not underdraw, to avoid artifacts from bitmap reuse.
        final int targetWidth = (int) (minPercentage * toFit.getWidth());
        final int targetHeight = (int) (minPercentage * toFit.getHeight());

        if (toFit.getWidth() == targetWidth && toFit.getHeight() == targetHeight) {
            if (Log.isLoggable(TAG, Log.VERBOSE)) {
                Log.v(TAG, "adjusted target size matches input, returning input");
            }
            return toFit;
        }

        Bitmap.Config config = getSafeConfig(toFit);
        Bitmap toReuse = pool.get(targetWidth, targetHeight, config);
        if (toReuse == null) {
            toReuse = Bitmap.createBitmap(targetWidth, targetHeight, config);
        }
        // We don't add or remove alpha, so keep the alpha setting of the Bitmap we were given.
        TransformationUtils.setAlpha(toFit, toReuse);

        if (Log.isLoggable(TAG, Log.VERBOSE)) {
            Log.v(TAG, "request: " + width + "x" + height);
            Log.v(TAG, "toFit:   " + toFit.getWidth() + "x" + toFit.getHeight());
            Log.v(TAG, "toReuse: " + toReuse.getWidth() + "x" + toReuse.getHeight());
            Log.v(TAG, "minPct:   " + minPercentage);
        }

        Canvas canvas = new Canvas(toReuse);
        Matrix matrix = new Matrix();
        matrix.setScale(minPercentage, minPercentage);
        Paint paint = new Paint(PAINT_FLAGS);
        canvas.drawBitmap(toFit, matrix, paint);

        return toReuse;
    }
```

效果图:

``` java
Glide.with(this).load(url).fitCenter().into(iv1);
```

#### centerCrop

跟fitCenter的原理一样，只是具体处理逻辑不一样

``` java
public static Bitmap centerCrop(Bitmap recycled, Bitmap toCrop, int width, int height) {
    if (toCrop == null) {
        return null;
    } else if (toCrop.getWidth() == width && toCrop.getHeight() == height) {
        return toCrop;
    }
    // From ImageView/Bitmap.createScaledBitmap.
    final float scale;
    float dx = 0, dy = 0;
    Matrix m = new Matrix();
    if (toCrop.getWidth() * height > width * toCrop.getHeight()) {
        scale = (float) height / (float) toCrop.getHeight();
        dx = (width - toCrop.getWidth() * scale) * 0.5f;
    } else {
        scale = (float) width / (float) toCrop.getWidth();
        dy = (height - toCrop.getHeight() * scale) * 0.5f;
    }

    m.setScale(scale, scale);
    m.postTranslate((int) (dx + 0.5f), (int) (dy + 0.5f));
    final Bitmap result;
    if (recycled != null) {
        result = recycled;
    } else {
        result = Bitmap.createBitmap(width, height, getSafeConfig(toCrop));
    }

    // We don't add or remove alpha, so keep the alpha setting of the Bitmap we were given.
    TransformationUtils.setAlpha(toCrop, result);

    Canvas canvas = new Canvas(result);
    Paint paint = new Paint(PAINT_FLAGS);
    canvas.drawBitmap(toCrop, m, paint);
    return result;
}
```

效果图(所有的都是与centerCrop效果一样):

``` java
Glide.with(this).load(url).centerCrop().into(iv2);
```

#### 效果图对比
[![scaleType效果对比图](https://upload-images.jianshu.io/upload_images/9028834-91c74c3c2a9e1fe9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://upload-images.jianshu.io/upload_images/9028834-91c74c3c2a9e1fe9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 注意

有一点要注意的就是fitCenter和centerCrop方法与transform方法可以共存，但是有时候会互相影响，如果说圆角处理遇到了剪裁，圆角那一部分可能会刚好被剪裁掉了


这一章节涉及到的主要是取图方面的知识，如果是对取图方面要求不高的项目，那么用原生的scaletpye或者是Glide提供的两个方法即可，但是如果是取图规则特别复杂的项目那么就需要通过自定义transform了，具体可以参考Glide的两个transform的处理，也可以用github上不错的三方库。



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

### load调用图解

[![load调用图解](https://upload-images.jianshu.io/upload_images/9028834-67009b4eefd164bd.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://github.com/Rtia33/Notes/blob/master/Pics/Glide_load%E5%9B%BE%E8%A7%A3.jpg?raw=true)

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

## 3. Glide.with(…).load(…).into(ImageView)

into()方法是整个Glide图片加载流程中逻辑最复杂的地方。

### into调用图解

[![into调用图解](https://upload-images.jianshu.io/upload_images/9028834-99f9bea808aee2b5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://github.com/Rtia33/Notes/blob/master/Pics/Glide_into%E5%9B%BE%E8%A7%A3.jpg?raw=true)

#### begin调用图解

[![begin调用图解](https://upload-images.jianshu.io/upload_images/9028834-29b267154443da4e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://github.com/Rtia33/Notes/blob/master/Pics/Glide_begin%E5%9B%BE%E8%A7%A3.jpg?raw=true)

#### onSizeReady调用图解

[![onSizeReady调用图解](https://upload-images.jianshu.io/upload_images/9028834-922b260dd3402cf9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://github.com/Rtia33/Notes/blob/master/Pics/Glide_onSizeReady%E5%9B%BE%E8%A7%A3.jpg?raw=true)

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
[![image](http://upload-images.jianshu.io/upload_images/9028834-07681da74cb3b896.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://raw.githubusercontent.com/Rtia33/Notes/master/Pics/Glide_model%E4%BC%A0%E9%80%92%E5%9B%BE%E8%A7%A3.png)

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

[![loadProvider来源图解](https://upload-images.jianshu.io/upload_images/9028834-d7708a4a11b9d3cc.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://github.com/Rtia33/Notes/blob/master/Pics/Glide_loadProvider%E6%9D%A5%E6%BA%90%E5%9B%BE%E8%A7%A3.jpg?raw=true)

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

```java
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

```java
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

```java
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

```java
/* DecodeJob 源码 */
public Resource<Z> decodeFromSource() throws Exception {
    Resource<T> decoded = decodeSource();
    return transformEncodeAndTranscode(decoded);
}
```

刚才我们就是从这里跟进到decodeSource()方法当中，然后执行了一大堆一大堆的逻辑，最终得到了这个Resource<T>对象。然而你会发现，decodeFromSource()方法最终返回的却是一个Resource<Z>对象，那么这到底是怎么回事呢？我们就需要跟进到**`transformEncodeAndTranscode()`**方法来瞧一瞧了，代码如下所示：

```java
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

```java
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

```java
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
        onLoadComplete(resource);//【②】
    }
}
```

也就是说，经过第9行【①】decode()方法的执行，我们最终得到了这个`Resource<GlideDrawable>`对象，那么接下来就是如何将它显示出来了。可以看到，这里在第25行【②】调用了onLoadComplete()方法，表示图片加载已经完成了，代码如下所示：

```java
/* EngineRunnable 源码 */
private void onLoadComplete(Resource resource) {
    manager.onResourceReady(resource);
}
```

这个**manager**就是**`EngineJob`**对象，（Engine.load中创建EngineRunnable时将engineJob赋值给manager）因此这里实际上调用的是EngineJob的onResourceReady()方法，代码如下所示：

```java
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

```java
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

```java
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

```java
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

```java
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

# Glide的缓存机制

## Glide缓存简介

Glide的缓存设计可以说是非常先进的，考虑的场景也很周全。
在缓存这一功能上，Glide又将它分成了两个模块，一个是**内存缓存**，一个是**硬盘缓存**。

这两个缓存模块的作用各不相同：
**内存缓存**的主要作用是**防止**应用**重复**将图片数据**读取到内存**当中；
**硬盘缓存**的主要作用是**防止**应用**重复从网络**或其他地方重复**下载**和读取数据。

内存缓存和硬盘缓存的相互结合才构成了Glide极佳的图片缓存效果，那么接下来我们就分别来分析一下这两种缓存的使用方法以及它们的实现原理。

## 缓存Key

既然是缓存功能，就必然会有用于进行缓存的Key。那么Glide的缓存Key是怎么生成的呢？

生成缓存Key的代码在Engine类的load()方法当中（onSizeReady调用engine.load），这部分代码我们在上一篇文章当中已经分析过了，只不过当时忽略了缓存相关的内容，那么我们现在重新来看一下：

```java
/* Engine 源码 */
public class Engine implements EngineJobListener,
        MemoryCache.ResourceRemovedListener,
        EngineResource.ResourceListener {

    public <T, Z, R> LoadStatus load(Key signature, int width, int height, DataFetcher<T> fetcher,
            DataLoadProvider<T, Z> loadProvider, Transformation<Z> transformation, ResourceTranscoder<Z, R> transcoder,
            Priority priority, boolean isMemoryCacheable, DiskCacheStrategy diskCacheStrategy, ResourceCallback cb) {
        Util.assertMainThread();
        long startTime = LogTime.getLogTime();

        final String id = fetcher.getId();//【①】
        EngineKey key = keyFactory.buildKey(id, signature, width, height, loadProvider.getCacheDecoder(),//【②】
                loadProvider.getSourceDecoder(), transformation, loadProvider.getEncoder(),
                transcoder, loadProvider.getSourceEncoder());
        ...
    }
    ...
}
```

可以看到，这里在第11行【①】调用了`fetcher.getId()`方法获得了一个id字符串，这个字符串也就是我们要**加载的图片的唯一标识**，比如说如果是一张网络上的图片的话，那么这个id就是这张图片的url地址。

接下来在第12行【②】，将这个**id连同着signature、width、height等等10个参数一起传入**到`EngineKeyFactory.buildKey()`方法当中，从而构建出了一个**`EngineKey`**对象，这个EngineKey也就是Glide中的缓存Key了。

可见，决定缓存Key的条件非常多，即使你用override()方法改变了一下图片的width或者height，也会生成一个完全不同的缓存Key。

EngineKey类的源码大家有兴趣可以自己去看一下，其实主要就是重写了equals()和hashCode()方法，保证只有传入EngineKey的所有参数都相同的情况下才认为是同一个EngineKey对象，我就不在这里将源码贴出来了。

## 内存缓存

**默认**情况下，Glide自动就是**开启内存缓存**的。也就是说，当我们使用Glide加载了一张图片之后，这张图片就会被缓存到内存当中，在它从内存中被清除之前，下次使用Glide再加载这张图片都会直接从内存当中读取，而不用重新从网络或硬盘上读取了，这样无疑就可以大幅度**提升**图片的**加载效率**。
比方说你在一个RecyclerView当中反复上下滑动，RecyclerView中只要是Glide加载过的图片都可以直接从内存当中迅速读取并展示出来，从而大大提升了用户体验。

而Glide最为人性化的是，你甚至不需要编写任何额外的代码就能自动享受到这个极为便利的内存缓存功能，因为Glide默认就已经将它开启了。

那么既然已经默认开启了这个功能，还有什么可讲的用法呢？

### 禁用内存缓存

只需要调用**skipMemoryCache()**方法并传入**true**，就表示禁用掉Glide的内存缓存功能。

```
Glide.with(this)
     .load(url)
     .skipMemoryCache(true)
     .into(imageView);

```

接下来我们就通过阅读源码来分析一下Glide的内存缓存功能是如何实现的。

### 内存缓存源码分析

其实说到内存缓存的实现，非常容易就让人想到**LruCache**算法（Least Recently Used），也叫近期最少使用算法。它的主要算法原理就是把**最近使用的对象用强引用存储在LinkedHashMap中**，并且**把最近最少使用的对象在缓存值达到预设定值之前从内存中移除**。
LruCache的用法也比较简单，我在 [Android高效加载大图、多图解决方案，有效避免程序OOM](http://blog.csdn.net/guolin_blog/article/details/9316683) 这篇文章当中有提到过它的用法，感兴趣的朋友可以去参考一下。

**Glide内存缓存**的实现也是**使用**的**LruCache**算法。不过除了LruCache算法之外，Glide**还结合**了一种**弱引用**的机制，共同完成了内存缓存功能，下面就让我们来通过源码分析一下。

#### Glide.buildStreamModelLoader()

首先回忆一下，在上一篇文章的第二步load()方法中，我们当时分析到了在`loadGeneric()`方法中会调用`Glide.buildStreamModelLoader()`方法来获取一个ModelLoader对象。当时没有再跟进到这个方法的里面再去分析，那么我们现在来看下它的源码：

```java
/* Glide 源码 */
public class Glide {
    public static <T, Y> ModelLoader<T, Y> buildModelLoader(Class<T> modelClass, Class<Y> resourceClass,
            Context context) {
         if (modelClass == null) {
            if (Log.isLoggable(TAG, Log.DEBUG)) {
                Log.d(TAG, "Unable to load null model, setting placeholder only");
            }
            return null;
        }
        return Glide.get(context).getLoaderFactory().buildModelLoader(modelClass, resourceClass);//【①】
    }

    public static Glide get(Context context) {
        if (glide == null) {
            synchronized (Glide.class) {
                if (glide == null) {
                    Context applicationContext = context.getApplicationContext();
                    List<GlideModule> modules = new ManifestParser(applicationContext).parse();
                    GlideBuilder builder = new GlideBuilder(applicationContext);
                    for (GlideModule module : modules) {
                        module.applyOptions(applicationContext, builder);
                    }
                    glide = builder.createGlide();//【②】
                    for (GlideModule module : modules) {
                        module.registerComponents(applicationContext, glide);
                    }
                }
            }
        }
        return glide;
    }
    ...
}
```

这里我们还是只看关键，在第11行【①】去构建ModelLoader对象的时候，先调用了一个Glide.get()方法，而这个方法就是关键。我们可以看到，get()方法中实现的是一个单例功能，而创建Glide对象则是在第24行【②】调用GlideBuilder的createGlide()方法来创建的，那么我们跟到这个方法当中：

#### glideBuilder.createGlide()

```java
/* GlideBuilder 源码 */
public class GlideBuilder {
    ...
    Glide createGlide() {
        if (sourceService == null) {
            final int cores = Math.max(1, Runtime.getRuntime().availableProcessors());
            sourceService = new FifoPriorityThreadPoolExecutor(cores);
        }
        if (diskCacheService == null) {
            diskCacheService = new FifoPriorityThreadPoolExecutor(1);
        }
        MemorySizeCalculator calculator = new MemorySizeCalculator(context);
        if (bitmapPool == null) {
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.HONEYCOMB) {
                int size = calculator.getBitmapPoolSize();
                bitmapPool = new LruBitmapPool(size);
            } else {
                bitmapPool = new BitmapPoolAdapter();
            }
        }
        if (memoryCache == null) {
            memoryCache = new LruResourceCache(calculator.getMemoryCacheSize());//【①】
        }
        if (diskCacheFactory == null) {
            diskCacheFactory = new InternalCacheDiskCacheFactory(context);
        }
        if (engine == null) {
            engine = new Engine(memoryCache, diskCacheFactory, diskCacheService, sourceService);
        }
        if (decodeFormat == null) {
            decodeFormat = DecodeFormat.DEFAULT;
        }
        return new Glide(engine, memoryCache, bitmapPool, context, decodeFormat);
    }
}
```

这里也就是构建Glide对象的地方了。那么观察第22行【①】，你会发现这里new出了一个**`LruResourceCache`**，并把它赋值到了memoryCache这个对象上面。你没有猜错，这个就是**Glide实现内存缓存所使用的LruCache**对象了。
不过我这里并不打算展开来讲LruCache算法的具体实现，如果你感兴趣的话可以自己研究一下它的源码。

现在创建好了LruResourceCache对象只能说是把准备工作做好了，接下来我们就一步步研究Glide中的内存缓存到底是如何实现的。

刚才在Engine的load()方法中我们已经看到了生成缓存Key的代码，而内存缓存的代码其实也是在这里实现的，那么我们重新来看一下Engine类load()方法的完整源码：

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

        EngineResource<?> cached = loadFromCache(key, isMemoryCacheable);//【①】
        if (cached != null) {
            cb.onResourceReady(cached);
            if (Log.isLoggable(TAG, Log.VERBOSE)) {
                logWithTimeAndKey("Loaded resource from cache", startTime, key);
            }
            return null;
        }

        EngineResource<?> active = loadFromActiveResources(key, isMemoryCacheable);//【②】
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

        EngineJob engineJob = engineJobFactory.build(key, isMemoryCacheable);
        DecodeJob<T, Z, R> decodeJob = new DecodeJob<T, Z, R>(key, width, height, fetcher, loadProvider, transformation,
                transcoder, diskCacheProvider, diskCacheStrategy, priority);
        EngineRunnable runnable = new EngineRunnable(engineJob, decodeJob, priority);
        jobs.put(key, engineJob);
        engineJob.addCallback(cb);
        engineJob.start(runnable);

        if (Log.isLoggable(TAG, Log.VERBOSE)) {
            logWithTimeAndKey("Started new load", startTime, key);
        }
        return new LoadStatus(cb, engineJob);
    }
    ...
}
```

可以看到，这里在第17行调用了**`loadFromCache()`**方法来**获取缓存图片**：
如果**获取到**就直接调用`cb.onResourceReady()`方法进行回调；
如果**没有获取到**，则会在第26行【②】调用**`loadFromActiveResources()`**方法来**获取缓存**图片，获取到的话也直接进行回调。
只有在两个方法**都没有获取到缓存**的情况下，才会继续向下执行，从而**开启线程来加载图片**。

也就是说，Glide的图片加载过程中会调用两个方法来获取内存缓存，**loadFromCache**()和**loadFromActiveResources**()。
这两个方法中一个使用的就是**LruCache算法**，另一个使用的就是**弱引用**。我们来看一下它们的源码：

#### 内存缓存读取

#### loadFromCache()和loadFromActiveResources()

```java
/* Engine 源码 */
public class Engine implements EngineJobListener,
        MemoryCache.ResourceRemovedListener,
        EngineResource.ResourceListener {

    private final MemoryCache cache;
    private final Map<Key, WeakReference<EngineResource<?>>> activeResources;
    ...

    private EngineResource<?> loadFromCache(Key key, boolean isMemoryCacheable) {
        if (!isMemoryCacheable) {//【①】
            return null;
        }
        EngineResource<?> cached = getEngineResourceFromCache(key);//【②】
        if (cached != null) {
            cached.acquire();
            activeResources.put(key, new ResourceWeakReference(key, cached, getReferenceQueue()));//【③】
        }
        return cached;
    }

    private EngineResource<?> getEngineResourceFromCache(Key key) {
        Resource<?> cached = cache.remove(key);//【④】
        final EngineResource result;
        if (cached == null) {
            result = null;
        } else if (cached instanceof EngineResource) {
            result = (EngineResource) cached;
        } else {
            result = new EngineResource(cached, true /*isCacheable*/);
        }
        return result;
    }

    private EngineResource<?> loadFromActiveResources(Key key, boolean isMemoryCacheable) {
        if (!isMemoryCacheable) {
            return null;
        }
        EngineResource<?> active = null;
        WeakReference<EngineResource<?>> activeRef = activeResources.get(key);//【⑤】
        if (activeRef != null) {
            active = activeRef.get();
            if (active != null) {
                active.acquire();
            } else {
                activeResources.remove(key);
            }
        }
        return active;
    }
    ...
}
```

在**`loadFromCache`**()方法的一开始，首先【①】就判断了**isMemoryCacheable**是不是**false**，如果是false的话就直接返回null。如果在skipMemoryCache()这个方法中传入true，那么这里的isMemoryCacheable就会是false，表示内存**缓存已被禁用**。

我们继续住下看，接着调用了【②】**`getEngineResourceFromCache`**()方法来获取缓存。在这个方法中，会**使用缓存Key来从cache当中取值**，而这里的**cache**对象就是在**构建Glide**对象时**创建的LruResourceCache**，那么说明这里其实使用的就是LruCache算法了。

但是呢，观察第22行【④】`cache.remove(key)`，当我们从LruResourceCache中**获取到缓存图片之后**会将它**从缓存中移除**，然后在第16行【③】`activeResources.put(key, new ResourceWeakReference(key, cached, getReferenceQueue()))`将这个缓存图片**存储到activeResources当中**。

**activeResources**就是一个**弱引用**的**HashMap**，用来**缓存正在使用中的图片**。
**loadFromActiveResources**()方法就是**从activeResources**这个HashMap当中**取值**的。（【⑤】`activeResources.get(key)`）
使用**activeResources**来**缓存正在使用中的图片**，可以**保护**这些图片**不**会**被LruCache算法回收**掉。

从内存缓存中读取数据的逻辑大概就是这些了。概括一下来说，就是如果能从内存缓存当中读取到要加载的图片，那么就直接进行回调，如果读取不到的话，才会开启线程执行后面的图片加载逻辑。

#### 弱引用缓存写入

现在我们已经搞明白了内存缓存读取的原理，接下来的问题就是内存缓存是在哪里写入的呢？
上章分析，当图片加载完成之后，会在EngineJob当中通过Handler发送一条消息将执行逻辑切回到主线程当中，从而执行handleResultOnMainThread()方法。
那么我们现在重新来看一下这个方法：

```java
/* EngineJob 源码 */
class EngineJob implements EngineRunnable.EngineRunnableManager {

    private final EngineResourceFactory engineResourceFactory;
    ...

    private void handleResultOnMainThread() {
        if (isCancelled) {
            resource.recycle();
            return;
        } else if (cbs.isEmpty()) {
            throw new IllegalStateException("Received a resource without any callbacks to notify");
        }
        engineResource = engineResourceFactory.build(resource, isCacheable);//【①】
        hasResource = true;
        engineResource.acquire();//【③】
        listener.onEngineJobComplete(key, engineResource);//【②】
        for (ResourceCallback cb : cbs) {
            if (!isInIgnoredCallbacks(cb)) {
                engineResource.acquire();//【④】
                cb.onResourceReady(engineResource);
            }
        }
        engineResource.release();//【⑤】
    }

    static class EngineResourceFactory {
        public <R> EngineResource<R> build(Resource<R> resource, boolean isMemoryCacheable) {
            return new EngineResource<R>(resource, isMemoryCacheable);
        }
    }
    ...
}
```

在第13行【①】`engineResourceFactory.build(resource, isCacheable)`，这里通过EngineResourceFactory**构建**出了一个**包含图片资源的EngineResource对象**，然后会在第16行【②】`listener.onEngineJobComplete(key, engineResource)`将这个对象回调到Engine的**`onEngineJobComplete()`**方法当中，如下所示：

```java
/* Engine 源码 */
public class Engine implements EngineJobListener,
        MemoryCache.ResourceRemovedListener,
        EngineResource.ResourceListener {
    ...    
    @Override
    public void onEngineJobComplete(Key key, EngineResource<?> resource) {
        Util.assertMainThread();
        // A null resource indicates that the load failed, usually due to an exception.
        if (resource != null) {
            resource.setResourceListener(key, this);
            if (resource.isCacheable()) {
                activeResources.put(key, new ResourceWeakReference(key, resource, getReferenceQueue()));//【①】
            }
        }
        jobs.remove(key);
    }
    ...
}
```

现在就非常明显了，可以看到，在第13行【①】`activeResources.put(key, new ResourceWeakReference(key, resource, getReferenceQueue()))`，回调过来的EngineResource被put到了activeResources当中，也就是在这里**写入**的**缓存**。

那么这只是**弱引用缓存**，还有另外一种LruCache缓存是在哪里写入的呢？

#### LruCache缓存写入

这就要介绍一下EngineResource中的一个引用机制了。观察刚才的handleResultOnMainThread()方法，在第15行【③】和第19行【④】` engineResource.acquire();`有调用EngineResource的acquire()方法，在第23行【⑤】`engineResource.release();`有调用它的release()方法。其实，**`EngineResource`**是**用**一个**acquired**变量用来**记录**图片被**引用**的**次数**，调用**acquire**()方法会让变量**加1**，调用**release**()方法会让变量**减1**，代码如下所示：

```java
/* EngineResource源码 */
class EngineResource<Z> implements Resource<Z> {

    private int acquired;
    ...

    void acquire() {
        if (isRecycled) {
            throw new IllegalStateException("Cannot acquire a recycled resource");
        }
        if (!Looper.getMainLooper().equals(Looper.myLooper())) {
            throw new IllegalThreadStateException("Must call acquire on the main thread");
        }
        ++acquired;
    }

    void release() {
        if (acquired <= 0) {
            throw new IllegalStateException("Cannot release a recycled or not yet acquired resource");
        }
        if (!Looper.getMainLooper().equals(Looper.myLooper())) {
            throw new IllegalThreadStateException("Must call release on the main thread");
        }
        if (--acquired == 0) {
            listener.onResourceReleased(key, this);//【①】
        }
    }
}
```

也就是说，当**acquired**变量**大于0**的时候，说明图片**正在使用**中，也就应该**放到activeResources弱引用缓存**当中。而经过**release**()之**后**，如果**acquired**变量**等于0**了，说明图片已经**不再被使用**了，那么此时会在第24行【①】`listener.onResourceReleased(key, this)`调用listener的onResourceReleased()方法来**释放资源**，这个listener就是Engine对象，我们来看下它的onResourceReleased()方法：

```java
/* Engine 源码 */
public class Engine implements EngineJobListener,
        MemoryCache.ResourceRemovedListener,
        EngineResource.ResourceListener {

    private final MemoryCache cache;
    private final Map<Key, WeakReference<EngineResource<?>>> activeResources;
    ...    

    @Override
    public void onResourceReleased(Key cacheKey, EngineResource resource) {
        Util.assertMainThread();
        activeResources.remove(cacheKey);
        if (resource.isCacheable()) {
            cache.put(cacheKey, resource);
        } else {
            resourceRecycler.recycle(resource);
        }
    }
    ...
}
```

可以看到，这里首先会**将缓存图片从activeResources中移除**，然后**再将它put到LruResourceCache当中**。
这样也就实现了**正在使用中的图片使用弱引用来进行缓存**，**不在使用中的图片使用LruCache来进行缓存**的功能。

这就是Glide内存缓存的实现原理。

## 硬盘缓存

禁止Glide对图片进行硬盘缓存：

```
Glide.with(this)
     .load(url)
     .diskCacheStrategy(DiskCacheStrategy.NONE)
     .into(imageView);

```

调用diskCacheStrategy()方法并传入DiskCacheStrategy.NONE，就可以禁用掉Glide的硬盘缓存功能了。

### diskCacheStrategy

这个diskCacheStrategy()方法基本上就是Glide硬盘缓存功能的一切，它可以接收四种参数：

- DiskCacheStrategy.**NONE**： 表示**不缓存**任何内容。
- DiskCacheStrategy.**SOURCE**： 表示只缓存**原始图片**。
- DiskCacheStrategy.**RESULT**： 表示只缓存**转换过后的图片**（**默认**选项）。
- DiskCacheStrategy.**ALL** ： 表示**既**缓存原始图片，也缓存转换过后的图片。

当我们使用Glide去加载一张图片的时候，Glide**默认**并**不**会将**原始图片**展示出来，而是会对图片进行**压缩和转换**（我们会在后面学习这方面的内容）。总之就是经过种种一系列操作之后得到的图片，就叫转换过后的图片。而Glide默认情况下在硬盘缓存的就是转换过后的图片，我们通过调用diskCacheStrategy()方法则可以改变这一默认行为。

### 硬盘缓存源码分析

首先，和内存缓存类似，硬盘缓存的实现也是**使用**的**LruCache算法**，而且Google还提供了一个现成的工具类DiskLruCache。我之前也专门写过一篇文章对这个DiskLruCache工具进行了比较全面的分析，感兴趣的朋友可以参考一下 [Android DiskLruCache完全解析，硬盘缓存的最佳方案](http://blog.csdn.net/guolin_blog/article/details/28863651) 。当然，Glide是使用的自己编写的DiskLruCache工具类，但是基本的实现原理都是差不多的。

#### 读取

Glide开启线程来加载图片后会执行EngineRunnable的run()方法，run()方法中又会调用一个decode()方法【见onSizeReady调用图解】

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

#### EngineRunnable.decodeFromCache()

可以看到，这里会分为两种情况，一种是调用decodeFromCache()方法从硬盘缓存当中读取图片，一种是调用decodeFromSource()来读取原始图片。默认情况下Glide会优先从缓存当中读取，只有缓存中不存在要读取的图片时，才会去读取原始图片。那么我们现在来看一下decodeFromCache()方法的源码，如下所示：

```java
/* EngineRunnable 源码 */
private Resource<?> decodeFromCache() throws Exception {
    Resource<?> result = null;
    try {
        result = decodeJob.decodeResultFromCache();//【①】
    } catch (Exception e) {
        if (Log.isLoggable(TAG, Log.DEBUG)) {
            Log.d(TAG, "Exception decoding result from cache: " + e);
        }
    }
    if (result == null) {
        result = decodeJob.decodeSourceFromCache();//【②】
    }
    return result;
}
```

可以看到，【①】这里会先去调用DecodeJob的**`decodeResultFromCache()`**方法来获取缓存，如果获取不到，【②】会再调用**`decodeSourceFromCache()`**方法获取缓存，这两个方法的区别其实就是DiskCacheStrategy.**RESULT**和DiskCacheStrategy.**SOURCE**这两个参数的区别，相信不需要我再做什么解释吧。

那么我们来看一下这两个方法的源码吧，如下所示：

```java
/* DecodeJob 源码 */
public Resource<Z> decodeResultFromCache() throws Exception {
    if (!diskCacheStrategy.cacheResult()) {
        return null;
    }
    long startTime = LogTime.getLogTime();
    Resource<T> transformed = loadFromCache(resultKey);
    startTime = LogTime.getLogTime();
    Resource<Z> result = transcode(transformed);
    return result;
}

public Resource<Z> decodeSourceFromCache() throws Exception {
    if (!diskCacheStrategy.cacheSource()) {
        return null;
    }
    long startTime = LogTime.getLogTime();
    Resource<T> decoded = loadFromCache(resultKey.getOriginalKey());
    return transformEncodeAndTranscode(decoded);
}
```

可以看到，它们都是调用了`loadFromCache()`方法从缓存当中读取数据；
如果是**decodeResultFromCache**()方法就**直接**将数据**解码**并**返回**，
如果是**decodeSourceFromCache**()方法，还要**调用**一下**transformEncodeAndTranscode**()方法先将数据**转换**一下**再解码**并**返回**。

然而我们注意到，这两个方法中在调用loadFromCache()方法时传入的参数却不一样，一个传入的是resultKey，另外一个却又调用了resultKey的getOriginalKey()方法。这个其实非常好理解，刚才我们已经解释过了，Glide的缓存Key是由10个参数共同组成的，包括图片的width、height等等。
但如果我们是**缓存**的**原始图片**，其实并不需要这么多的参数，因为不用对图片做任何的变化。那么我们来看一下getOriginalKey()方法的源码：

```java
/* EngineKey 源码 */
public Key getOriginalKey() {
    if (originalKey == null) {
        originalKey = new OriginalKey(id, signature);
    }
    return originalKey;
}
```

可以看到，这里其实就是忽略了绝大部分的参数，**只使用了id和signature**这两个参数来**构成缓存Key**。而**signature**参数绝大多数情况下都是**用不到**的，因此基本上可以说就是**由id**（也就是**图片url**）来**决定**的**Original缓存Key**。

#### DecodeJob.loadFromCache(key)

搞明白了这两种缓存Key的区别，那么接下来我们看一下loadFromCache()方法的源码吧：

```java
/* DecodeJob 源码 */
private Resource<T> loadFromCache(Key key) throws IOException {
    File cacheFile = diskCacheProvider.getDiskCache().get(key);
    if (cacheFile == null) {
        return null;
    }
    Resource<T> result = null;
    try {
        result = loadProvider.getCacheDecoder().decode(cacheFile, width, height);
    } finally {
        if (result == null) {
            diskCacheProvider.getDiskCache().delete(key);
        }
    }
    return result;
}
```

这个方法的逻辑非常简单，调用`getDiskCache()`方法**获取**到的就是Glide自己编写的**DiskLruCache工具类**的实例，然后调用它的`get()`方法并把缓存Key传入，就能**得到硬盘缓存的文件**了。
如果文件为空就返回null，如果**文件**不为空则将它**解码成Resource对象**后返回即可。

这样我们就将硬盘缓存读取的源码分析完了，那么硬盘缓存又是在哪里写入的呢？

#### 写入

#### EngineRunnable.decodeFromSource()

刚才已经分析过了，在没有缓存的情况下，会调用decodeFromSource()方法来读取原始图片。那么我们来看下这个方法：

```java
/* EngineRunnable 源码 */
public Resource<Z> decodeFromSource() throws Exception {
    Resource<T> decoded = decodeSource();
    return transformEncodeAndTranscode(decoded);
}
```

这个方法中只有两行代码，**decodeSource**()顾名思义是用来**解析原图**片的，而**transformEncodeAndTranscode**()则是用来**对图片进行转换和转码**的。

#### DecodeJob.decodeSource()

我们先来看decodeSource()方法：

```java
/* DecodeJob 源码 */
private Resource<T> decodeSource() throws Exception {
    Resource<T> decoded = null;
    try {
        long startTime = LogTime.getLogTime();
        final A data = fetcher.loadData(priority);//【①】
        if (isCancelled) {
            return null;
        }
        decoded = decodeFromSourceData(data);//【②】
    } finally {
        fetcher.cleanup();
    }
    return decoded;
}

private Resource<T> decodeFromSourceData(A data) throws IOException {
    final Resource<T> decoded;
    if (diskCacheStrategy.cacheSource()) {//【③】
        decoded = cacheAndDecodeSourceData(data);
    } else {
        long startTime = LogTime.getLogTime();
        decoded = loadProvider.getSourceDecoder().decode(data, width, height);
    }
    return decoded;
}

private Resource<T> cacheAndDecodeSourceData(A data) throws IOException {
    long startTime = LogTime.getLogTime();
    SourceWriter<A> writer = new SourceWriter<A>(loadProvider.getSourceEncoder(), data);
    diskCacheProvider.getDiskCache().put(resultKey.getOriginalKey(), writer);//【④】
    startTime = LogTime.getLogTime();
    Resource<T> result = loadFromCache(resultKey.getOriginalKey());
    return result;
}
```

这里会在第5行【①】` fetcher.loadData(priority)`先调用fetcher的loadData()方法**读取图片数据**，然后在第9行【②】调用`decodeFromSourceData(data)`方法来**对图片进行解码**。接下来会在第18行【③】`diskCacheStrategy.cacheSource()`先判断**是否允许缓存原始图片**，如果允许的话又会调用`cacheAndDecodeSourceData()`方法。而在这个方法中【④】`diskCacheProvider.getDiskCache().put(resultKey.getOriginalKey(), writer)`同样调用了`getDiskCache()`方法来**获取DiskLruCache实例**，接着调用它的`put()`方法就可以**写入硬盘缓存**了，注意原始图片的缓存Key是用的resultKey.getOriginalKey()。

好的，原始图片的缓存写入就是这么简单，接下来我们分析一下transformEncodeAndTranscode()方法的源码，来看看转换过后的图片缓存是怎么写入的。代码如下所示：

```java
/* DecodeJob 源码 */
private Resource<Z> transformEncodeAndTranscode(Resource<T> decoded) {
    long startTime = LogTime.getLogTime();
    Resource<T> transformed = transform(decoded);//【①】
    writeTransformedToCache(transformed);//【②】
    startTime = LogTime.getLogTime();
    Resource<Z> result = transcode(transformed);
    return result;
}

private void writeTransformedToCache(Resource<T> transformed) {
    if (transformed == null || !diskCacheStrategy.cacheResult()) {
        return;
    }
    long startTime = LogTime.getLogTime();
    SourceWriter<Resource<T>> writer = new SourceWriter<Resource<T>>(loadProvider.getEncoder(), transformed);
    diskCacheProvider.getDiskCache().put(resultKey, writer);//写入到硬盘缓存中
}
```

这里的逻辑就更加简单明了了。先是在第3行【①】调用`transform(decoded)`方法来**对图片进行转换**，然后在【②】`writeTransformedToCache()`方法中将转换过后的图片**写入**到**硬盘缓存**中，调用的同样是DiskLruCache实例的put()方法，不过这里用的缓存Key是resultKey。

这样我们就将Glide硬盘缓存的实现原理也分析完了。虽然这些源码看上去如此的复杂，但是经过Glide出色的封装，使得我们只需要通过skipMemoryCache()和diskCacheStrategy()这两个方法就可以轻松自如地控制Glide的缓存功能了。



#### 缓存图解

##### Engine的创建图解

[![Glide_Engine的创建图解.jpg](https://upload-images.jianshu.io/upload_images/9028834-a3ad2b324f8877eb.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://github.com/Rtia33/Notes/blob/master/Pics/Glide_Engine%E7%9A%84%E5%88%9B%E5%BB%BA%E5%9B%BE%E8%A7%A3.jpg?raw=true)

##### 读取内存缓存图解

[![Glide_读取内存缓存图解.jpg](https://upload-images.jianshu.io/upload_images/9028834-3f7cd69d70a6666d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://github.com/Rtia33/Notes/blob/master/Pics/Glide_%E8%AF%BB%E5%8F%96%E5%86%85%E5%AD%98%E7%BC%93%E5%AD%98%E5%9B%BE%E8%A7%A3.jpg?raw=true)

##### 写入内存缓存图解

[![Glide_写入内存缓存图解.jpg](https://upload-images.jianshu.io/upload_images/9028834-285349a28f7cf4b4.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
](https://github.com/Rtia33/Notes/blob/master/Pics/Glide_%E5%86%99%E5%85%A5%E5%86%85%E5%AD%98%E7%BC%93%E5%AD%98%E5%9B%BE%E8%A7%A3.jpg?raw=true)

##### 读取硬盘缓存图解

decodeFromCache图解
[![Glide_decodeFromCache图解.jpg](https://upload-images.jianshu.io/upload_images/9028834-b33e7bb467be78fe.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://github.com/Rtia33/Notes/blob/master/Pics/Glide_decodeFromCache%E5%9B%BE%E8%A7%A3.jpg?raw=true)

##### 写入硬盘缓存图解

cacheAndDecodeSourceData图解
[![Glide_cacheAndDecodeSourceData图解.jpg](https://upload-images.jianshu.io/upload_images/9028834-851d12cca3c1a194.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://github.com/Rtia33/Notes/blob/master/Pics/Glide_cacheAndDecodeSourceData%E5%9B%BE%E8%A7%A3.jpg?raw=true)



## 高级技巧

虽说Glide将缓存功能高度封装之后，使得用法变得非常简单，但同时也带来了一些问题。

比如之前有一位群里的朋友就跟我说过，他们项目的图片资源都是存放在七牛云上面的，而七牛云为了对图片资源进行保护，会在图片url地址的基础之上再加上一个token参数。也就是说，一张图片的url地址可能会是如下格式：

```
http://url.com/image.jpg?token=d9caa6e02c990b0a

```

而使用Glide加载这张图片的话，也就会使用这个url地址来组成缓存Key。

但是接下来问题就来了，token作为一个验证身份的参数并不是一成不变的，很有可能时时刻刻都在变化。而如果token变了，那么图片的url也就跟着变了，图片url变了，缓存Key也就跟着变了。结果就造成了，明明是同一张图片，就因为token不断在改变，导致Glide的缓存功能完全失效了。

这其实是个挺棘手的问题，而且我相信绝对不仅仅是七牛云这一个个例，大家在使用Glide的时候很有可能都会遇到这个问题。

那么该如何解决这个问题呢？我们还是从源码的层面进行分析，首先再来看一下Glide生成缓存Key这部分的代码：

```java
/* Engine 源码 */
public class Engine implements EngineJobListener,
        MemoryCache.ResourceRemovedListener,
        EngineResource.ResourceListener {

    public <T, Z, R> LoadStatus load(Key signature, int width, int height, DataFetcher<T> fetcher,
            DataLoadProvider<T, Z> loadProvider, Transformation<Z> transformation, ResourceTranscoder<Z, R> transcoder,
            Priority priority, boolean isMemoryCacheable, DiskCacheStrategy diskCacheStrategy, ResourceCallback cb) {
        Util.assertMainThread();
        long startTime = LogTime.getLogTime();

        final String id = fetcher.getId();
        EngineKey key = keyFactory.buildKey(id, signature, width, height, loadProvider.getCacheDecoder(),//【①】
                loadProvider.getSourceDecoder(), transformation, loadProvider.getEncoder(),
                transcoder, loadProvider.getSourceEncoder());

        ...
    }
    ...
}
```

来看一下第11行【①】这个id其实就是图片的url地址。那么，这里是通过调用fetcher.getId()方法来获取的图片url地址，而我们在上一篇文章中已经知道了，fetcher就是HttpUrlFetcher的实例，我们就来看一下它的getId()方法的源码吧，如下所示：

```java
/* HttpUrlFetcher 源码 */
public class HttpUrlFetcher implements DataFetcher<InputStream> {

    private final GlideUrl glideUrl;
    ...

    public HttpUrlFetcher(GlideUrl glideUrl) {
        this(glideUrl, DEFAULT_CONNECTION_FACTORY);
    }

    HttpUrlFetcher(GlideUrl glideUrl, HttpUrlConnectionFactory connectionFactory) {
        this.glideUrl = glideUrl;
        this.connectionFactory = connectionFactory;
    }

    @Override
    public String getId() {
        return glideUrl.getCacheKey();
    }

    ...
}
```

可以看到，getId()方法中又调用了GlideUrl的getCacheKey()方法。那么这个GlideUrl对象是从哪里来的呢？其实就是我们在load()方法中传入的图片url地址，然后Glide在内部把这个url地址包装成了一个GlideUrl对象。

很明显，接下来我们就要看一下GlideUrl的getCacheKey()方法的源码了，如下所示：

```java
/* GlideUrl 源码 */
public class GlideUrl {

    private final URL url;
    private final String stringUrl;
    ...

    public GlideUrl(URL url) {
        this(url, Headers.DEFAULT);
    }

    public GlideUrl(String url) {
        this(url, Headers.DEFAULT);
    }

    public GlideUrl(URL url, Headers headers) {
        ...
        this.url = url;
        stringUrl = null;
    }

    public GlideUrl(String url, Headers headers) {
        ...
        this.stringUrl = url;
        this.url = null;
    }

    public String getCacheKey() {
        return stringUrl != null ? stringUrl : url.toString();
    }

    ...
}
```

这里我将代码稍微进行了一点简化，这样看上去更加简单明了。GlideUrl类的构造函数接收两种类型的参数，一种是url字符串，一种是URL对象。然后getCacheKey()方法中的判断逻辑非常简单，如果传入的是url字符串，那么就直接返回这个字符串本身，如果传入的是URL对象，那么就返回这个对象toString()后的结果。

其实看到这里，我相信大家已经猜到解决方案了，因为getCacheKey()方法中的逻辑太直白了，直接就是将图片的url地址进行返回来作为缓存Key的。那么其实我们只需要**重写这个getCacheKey()方法**，加入一些自己的逻辑判断，就能轻松解决掉刚才的问题了。

创建一个MyGlideUrl继承自GlideUrl，代码如下所示：

```
public class MyGlideUrl extends GlideUrl {

    private String mUrl;

    public MyGlideUrl(String url) {
        super(url);
        mUrl = url;
    }

    @Override
    public String getCacheKey() {
        return mUrl.replace(findTokenParam(), "");
    }

    private String findTokenParam() {
        String tokenParam = "";
        int tokenKeyIndex = mUrl.indexOf("?token=") >= 0 ? mUrl.indexOf("?token=") : mUrl.indexOf("&token=");
        if (tokenKeyIndex != -1) {
            int nextAndIndex = mUrl.indexOf("&", tokenKeyIndex + 1);
            if (nextAndIndex != -1) {
                tokenParam = mUrl.substring(tokenKeyIndex + 1, nextAndIndex + 1);
            } else {
                tokenParam = mUrl.substring(tokenKeyIndex);
            }
        }
        return tokenParam;
    }

}

```

可以看到，这里我们重写了getCacheKey()方法，在里面加入了一段逻辑用于将图片url地址中token参数的这一部分移除掉。这样getCacheKey()方法得到的就是一个没有token参数的url地址，从而不管token怎么变化，最终Glide的缓存Key都是固定不变的了。

当然，定义好了MyGlideUrl，我们还得使用它才行，将加载图片的代码改成如下方式即可：

```
Glide.with(this)
     .load(new MyGlideUrl(url))
     .into(imageView);

```

也就是说，我们需要在**load()方法中传入这个自定义的MyGlideUrl对象**，而不能再像之前那样直接传入url字符串了。不然的话Glide在内部还是会使用原始的GlideUrl类，而不是我们自定义的MyGlideUrl类。

这样我们就将这个棘手的缓存问题给解决掉了。

好了，关于Glide缓存方面的内容今天就分析到这里，现在我们不光掌握了Glide缓存的基本用法和高级技巧，还了解了它背后的实现原理，又是收获满满的一篇文章啊。下一篇文章当中，我会继续带着大家深入分析Glide的其他功能模块，讲一讲回调方面的知识，感兴趣的朋友请继续阅读 [Android图片加载框架最全解析（四），玩转Glide的回调与监听](http://blog.csdn.net/guolin_blog/article/details/70215985) 。


