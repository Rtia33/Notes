<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [【Android RxJava 实际应用】](#android-rxjava-%E5%AE%9E%E9%99%85%E5%BA%94%E7%94%A8)
- [前言](#%E5%89%8D%E8%A8%80)
- [目录](#%E7%9B%AE%E5%BD%95)
- [1\. 简介](#1%5C-%E7%AE%80%E4%BB%8B)
- [2\. 基本使用](#2%5C-%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8)
- [3\. 实际开发应用场景](#3%5C-%E5%AE%9E%E9%99%85%E5%BC%80%E5%8F%91%E5%BA%94%E7%94%A8%E5%9C%BA%E6%99%AF)
    - [3.1 网络请求轮询（无条件）](#31-%E7%BD%91%E7%BB%9C%E8%AF%B7%E6%B1%82%E8%BD%AE%E8%AF%A2%E6%97%A0%E6%9D%A1%E4%BB%B6)
    - [3.2 网路请求轮询（有条件）](#32-%E7%BD%91%E8%B7%AF%E8%AF%B7%E6%B1%82%E8%BD%AE%E8%AF%A2%E6%9C%89%E6%9D%A1%E4%BB%B6)
    - [3.3 网络请求出错重连](#33-%E7%BD%91%E7%BB%9C%E8%AF%B7%E6%B1%82%E5%87%BA%E9%94%99%E9%87%8D%E8%BF%9E)
    - [3.4 网络请求嵌套回调](#34-%E7%BD%91%E7%BB%9C%E8%AF%B7%E6%B1%82%E5%B5%8C%E5%A5%97%E5%9B%9E%E8%B0%83)
    - [3.5 从磁盘 / 内存缓存中 获取缓存数据](#35-%E4%BB%8E%E7%A3%81%E7%9B%98--%E5%86%85%E5%AD%98%E7%BC%93%E5%AD%98%E4%B8%AD-%E8%8E%B7%E5%8F%96%E7%BC%93%E5%AD%98%E6%95%B0%E6%8D%AE)
    - [3.6 合并数据源](#36-%E5%90%88%E5%B9%B6%E6%95%B0%E6%8D%AE%E6%BA%90)
    - [3.7 联合判断](#37-%E8%81%94%E5%90%88%E5%88%A4%E6%96%AD)
    - [3.8 线程控制（切换 / 调度 ）](#38-%E7%BA%BF%E7%A8%8B%E6%8E%A7%E5%88%B6%E5%88%87%E6%8D%A2--%E8%B0%83%E5%BA%A6-)
    - [3.9 功能防抖](#39-%E5%8A%9F%E8%83%BD%E9%98%B2%E6%8A%96)
    - [3.10 联想搜索优化](#310-%E8%81%94%E6%83%B3%E6%90%9C%E7%B4%A2%E4%BC%98%E5%8C%96)
    - [3.11 控制被观察者发送事件 & 观察者接收事件速度：背压](#311-%E6%8E%A7%E5%88%B6%E8%A2%AB%E8%A7%82%E5%AF%9F%E8%80%85%E5%8F%91%E9%80%81%E4%BA%8B%E4%BB%B6--%E8%A7%82%E5%AF%9F%E8%80%85%E6%8E%A5%E6%94%B6%E4%BA%8B%E4%BB%B6%E9%80%9F%E5%BA%A6%E8%83%8C%E5%8E%8B)
- [4\. 总结](#4%5C-%E6%80%BB%E7%BB%93)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# 【Android RxJava 实际应用】

http://blog.csdn.net/carson_ho/article/details/79168723

# 前言

*   `Rxjava`由于其**基于事件流的链式调用、逻辑简洁 & 使用简单**的特点，深受各大 `Android`开发者的欢迎。

![Github截图](http://upload-images.jianshu.io/upload_images/9028834-cf02eb330ec13677?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 如果还不了解RxJava，请看文章：[Android：这是一篇 清晰 & 易懂的Rxjava 入门教程](http://blog.csdn.net/carson_ho/article/details/78179340)

*   今天，我将为大家带来 `Rxjava`的的基本使用 & **实际应用案例教学**，即**常见开发应用场景实现**，并结合常用相关框架如`Retrofit`等，希望大家会喜欢。 

    > 1.  本系列文章主要基于 `Rxjava 2.0` 
    >
    > 2.  接下来的时间，**我将持续推出 `Android`中 `Rxjava 2.0` 的一系列文章，包括原理、操作符、应用场景、背压等等** ，有兴趣可以继续关注[Carson_Ho的安卓开发笔记](http://blog.csdn.net/carson_ho)！！

![示意图](http://upload-images.jianshu.io/upload_images/9028834-9cf1a9dbd8c8ecdd?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* * *

# 目录

![示意图](http://upload-images.jianshu.io/upload_images/9028834-be93540f723a7876?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* * *

# 1\. 简介

`RxJava`的简介如下

![示意图](http://upload-images.jianshu.io/upload_images/9028834-7df0274e525b257f?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* * *

# 2\. 基本使用

*   `Rxjava`的使用方式有两种： 
    方式1：分步骤实现 
    ![示意图](http://upload-images.jianshu.io/upload_images/9028834-338443a024f94f05?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

    方式2：基于事件流的链式调用

*   具体使用 
    请看文章[Android RxJava：面向初学者的RxJava使用指南](http://blog.csdn.net/carson_ho/article/details/79168602)

* * *

# 3\. 实际开发应用场景

*   `RxJava`的实际开发应用场景 与 其对应的操作符息息相关
*   常见的`RxJava`实际开发应用场景有如下：

![示意图](http://upload-images.jianshu.io/upload_images/9028834-4abf8c0cfc3fdb72?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   下面，我将对每个实际开发应用场景进行实例讲解教学 

    > 下面实例皆结合常用框架如`Retrofit` 、`RxBinding`、`RxBus`等

### 3.1 网络请求轮询（无条件）

*   需求场景 
    ![示意图](http://upload-images.jianshu.io/upload_images/9028834-0d34789f00843e99?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   具体实现 
    [Android RxJava 实际应用讲解：（无条件）网络请求轮询](http://www.jianshu.com/p/11b3ec672812)

*   * *

### 3.2 网路请求轮询（有条件）

*   需求场景 
    ![示意图](http://upload-images.jianshu.io/upload_images/9028834-917b1f6f8d3f007d?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   具体实现 
    [Android RxJava 实际应用讲解：（有条件）网络请求轮询](http://blog.csdn.net/carson_ho/article/details/78256466)

*   * *

### 3.3 网络请求出错重连

*   需求场景 
    [图片上传中...(image-4f705e-1520954503322-4)]

*   功能需求说明

![示意图](http://upload-images.jianshu.io/upload_images/9028834-b0f477d197e9d6b7?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   功能逻辑

![示意图](http://upload-images.jianshu.io/upload_images/9028834-343e809838d8eaea?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   具体实现 
    [Android RxJava 实际应用讲解：网络请求出错重连（结合Retrofit）](http://blog.csdn.net/carson_ho/article/details/78651602)

### 3.4 网络请求嵌套回调

*   背景 
    需要进行嵌套网络请求：即在第1个网络请求成功后，继续再进行一次网络请求

    > 如 先进行 **用户注册** 的网络请求, 待注册成功后回再继续发送 **用户登录** 的网络请求

*   冲突 
    嵌套实现网络请求较为复杂，即嵌套调用函数

    > 下面展示的是结合 `Retrofit` 与 `RxJava`的基本用法，即未用操作符前

```
// 发送注册网络请求的函数方法
    private void register() {
        api.register(new RegisterRequest())
                .subscribeOn(Schedulers.io())               //在IO线程进行网络请求
                .observeOn(AndroidSchedulers.mainThread())  //回到主线程去处理请求结果
                .subscribe(new Consumer<RegisterResponse>() {
                    @Override
                    public void accept(RegisterResponse registerResponse) throws Exception {
                        Toast.makeText(MainActivity.this, "注册成功", Toast.LENGTH_SHORT).show();
                        login();   //注册成功, 调用登录的方法
                    }
                }, new Consumer<Throwable>() {
                    @Override
                    public void accept(Throwable throwable) throws Exception {
                        Toast.makeText(MainActivity.this, "注册失败", Toast.LENGTH_SHORT).show();
                    }
                });
    }

// 发送登录网络请求的函数方法
private void login() {
        api.login(new LoginRequest())
                .subscribeOn(Schedulers.io())               //在IO线程进行网络请求
                .observeOn(AndroidSchedulers.mainThread())  //回到主线程去处理请求结果
                .subscribe(new Consumer<LoginResponse>() {
                    @Override
                    public void accept(LoginResponse loginResponse) throws Exception {
                        Toast.makeText(MainActivity.this, "登录成功", Toast.LENGTH_SHORT).show();
                    }
                }, new Consumer<Throwable>() {
                    @Override
                    public void accept(Throwable throwable) throws Exception {
                        Toast.makeText(MainActivity.this, "登录失败", Toast.LENGTH_SHORT).show();
                    }
                });
    }

```

*   解决方案 
    结合 `RxJava2`中的变换操作符`FlatMap（）`实现嵌套网络请求

*   具体实现 
    [Android RxJava 实际应用讲解：网络请求嵌套回调](http://blog.csdn.net/carson_ho/article/details/78315696)

*   * *

### 3.5 从磁盘 / 内存缓存中 获取缓存数据

*   需求场景 
    ![示意图](http://upload-images.jianshu.io/upload_images/9028834-996047ffbaa0d69c?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   功能说明 
    对于从磁盘 / 内存缓存中 获取缓存数据 的功能逻辑如下： 
    [图片上传中...(image-166670-1520954503321-2)]

*   具体实现 
    [Android RxJava 实际应用讲解：从磁盘 / 内存缓存中 获取缓存数据](http://blog.csdn.net/carson_ho/article/details/78455449)

*   * *

### 3.6 合并数据源

*   需求场景

![示意图](http://upload-images.jianshu.io/upload_images/9028834-ad864a5c101ed3b3?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   功能说明 
    即，同时向2个数据源获取数据 -> 合并数据 -> 统一展示到客户端

*   具体实现 
    [Android RxJava 实际应用讲解：合并数据源](http://blog.csdn.net/carson_ho/article/details/78455544)

*   * *

### 3.7 联合判断

*   需求场景 
    需要同时对多个事件进行联合判断

    > 如，填写表单时，需要表单里所有信息（姓名、年龄、职业等）都被填写后，才允许点击 “提交” 按钮

*   功能说明 
    此处采用 填写表单 作为联合判断功能展示，即，**表单里所有信息（姓名、年龄、职业等）都被填写后，才允许点击 “提交” 按钮**

*   具体实现 
    [Android RxJava 实际应用讲解：联合判断](http://blog.csdn.net/carson_ho/article/details/78455624)

* * *

### 3.8 线程控制（切换 / 调度 ）

*   需求场景 
    即，新开工作线程执行耗时操作；待执行完毕后，切换到主线程实时更新 `UI`
*   具体实现 
    [Android RxJava：细说 线程控制（切换 / 调度 ）（含Retrofit实例讲解）](http://www.jianshu.com/p/5225b2baaecd)

*   * *

### 3.9 功能防抖

*   需求场景 
    [图片上传中...(image-1a1886-1520954503321-1)]

*   功能说明

![示意图](http://upload-images.jianshu.io/upload_images/9028834-bcf8153941de98fd?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   具体实现 
    [Android RxJava 实际应用讲解：功能防抖](http://blog.csdn.net/carson_ho/article/details/78849689)

*   * *

### 3.10 联想搜索优化

*   需求场景 
    [图片上传中...(image-5c82fa-1520954503321-0)]

*   功能说明

![示意图](http://upload-images.jianshu.io/upload_images/9028834-26fadc0acb146b0d?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   具体实现 
    [Android RxJava 实际应用讲解：联想搜索优化](http://blog.csdn.net/carson_ho/article/details/78849661)

*   * *

### 3.11 控制被观察者发送事件 & 观察者接收事件速度：背压

**a. 背景**

*   观察者 & 被观察者 之间存在2种订阅关系：同步 & 异步。具体如下：

![示意图](http://upload-images.jianshu.io/upload_images/9028834-da02bac8f3c693ea?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   对于异步订阅关系，存在 **被观察者发送事件速度 与观察者接收事件速度 不匹配的情况** 

    > 1.  发送 & 接收事件速度 = 单位时间内 发送&接收事件的数量 
    >
    > 2.  大多数情况，主要是 **被观察者发送事件速度 ＞ 观察者接收事件速度**

**b. 冲突** 

*   被观察者 发送事件速度太快，而观察者 来不及接收所有事件，从而导致**观察者无法及时响应 / 处理所有发送过来事件的问题，最终导致缓存区溢出、事件丢失 & OOM**

*   如，点击按钮事件：连续过快的点击按钮10次，则只会造成点击2次的效果；*   解释：因为点击速度太快了，所以按钮来不及响应下面再举个例子：

*   被观察者的发送事件速度 = 10ms / 个
*   观察者的接收事件速度 = 5s / 个

即出现发送 & 接收事件严重不匹配的问题

```
 Observable.create(new ObservableOnSubscribe<Integer>() {
            // 1\. 创建被观察者 & 生产事件
            @Override
            public void subscribe(ObservableEmitter<Integer> emitter) throws Exception {

                for (int i = 0; ; i++) {
                    Log.d(TAG, "发送了事件"+ i );
                    Thread.sleep(10);
                    // 发送事件速度：10ms / 个 
                    emitter.onNext(i);

                }

            }
        }).subscribeOn(Schedulers.io()) // 设置被观察者在io线程中进行
                .observeOn(AndroidSchedulers.mainThread()) // 设置观察者在主线程中进行
             .subscribe(new Observer<Integer>() {
            // 2\. 通过通过订阅（subscribe）连接观察者和被观察者

            @Override
            public void onSubscribe(Disposable d) {
                Log.d(TAG, "开始采用subscribe连接");
            }

            @Override
            public void onNext(Integer value) {

                try {
                    // 接收事件速度：5s / 个 
                    Thread.sleep(5000);
                    Log.d(TAG, "接收到了事件"+ value  );
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

            }

            @Override
            public void onError(Throwable e) {
                Log.d(TAG, "对Error事件作出响应");
            }

            @Override
            public void onComplete() {
                Log.d(TAG, "对Complete事件作出响应");
            }

        });
```

*   结果 
    由于被观察者发送事件速度 > 观察者接收事件速度，所以出现流速不匹配问题，从而导致`OOM` 
    ![示意图](http://upload-images.jianshu.io/upload_images/9028834-deec51aac29956de?imageMogr2/auto-orient/strip)

**c. 解决方案** 
采用 背压策略

*   具体实现 
    [Android ：全面解析RxJava 背压策略](http://blog.csdn.net/carson_ho/article/details/79081407)

至此，关于`RxJava`常见的实际开发应用场景讲解完毕。

* * *

# 4\. 总结

*   本文主要对 `RxJava2` 中常用的实际开发应用场景讲解进行了详细介绍，下面用1张图进行总结

![示意图](http://upload-images.jianshu.io/upload_images/9028834-815d47dbebd55bc9?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   接下来，**我将持续推出 `Android`中 `Rxjava 2.0` 的一系列文章，包括原理、操作符、应用场景、背压等等** ，有兴趣可以继续关注[Carson_Ho的安卓开发笔记](http://blog.csdn.net/carson_ho)！！

![示意图](http://upload-images.jianshu.io/upload_images/9028834-a030b07ac717f212?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)