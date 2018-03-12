<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [【Android Handler】](#android-handler)
  - [概述](#%E6%A6%82%E8%BF%B0)
  - [Looper、MessageQueue、Message、Handler的关系](#loopermessagequeuemessagehandler%E7%9A%84%E5%85%B3%E7%B3%BB)
    - [Looper](#looper)
    - [MessageQueue](#messagequeue)
    - [Message](#message)
  - [Handler的主要用途](#handler%E7%9A%84%E4%B8%BB%E8%A6%81%E7%94%A8%E9%80%94)
    - [1\. 推送未来某个时间点将要执行的Message或者Runnable到消息队列](#1%5C-%E6%8E%A8%E9%80%81%E6%9C%AA%E6%9D%A5%E6%9F%90%E4%B8%AA%E6%97%B6%E9%97%B4%E7%82%B9%E5%B0%86%E8%A6%81%E6%89%A7%E8%A1%8C%E7%9A%84message%E6%88%96%E8%80%85runnable%E5%88%B0%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97)
    - [2\. 在子线程把需要在另一个线程执行的操作加入到消息队列中去](#2%5C-%E5%9C%A8%E5%AD%90%E7%BA%BF%E7%A8%8B%E6%8A%8A%E9%9C%80%E8%A6%81%E5%9C%A8%E5%8F%A6%E4%B8%80%E4%B8%AA%E7%BA%BF%E7%A8%8B%E6%89%A7%E8%A1%8C%E7%9A%84%E6%93%8D%E4%BD%9C%E5%8A%A0%E5%85%A5%E5%88%B0%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%E4%B8%AD%E5%8E%BB)
      - [实例：通过Handler + Message来实现子线程加载图片，在UI线程显示图片](#%E5%AE%9E%E4%BE%8B%E9%80%9A%E8%BF%87handler--message%E6%9D%A5%E5%AE%9E%E7%8E%B0%E5%AD%90%E7%BA%BF%E7%A8%8B%E5%8A%A0%E8%BD%BD%E5%9B%BE%E7%89%87%E5%9C%A8ui%E7%BA%BF%E7%A8%8B%E6%98%BE%E7%A4%BA%E5%9B%BE%E7%89%87)
  - [Handler推送Message和Runnable的区别](#handler%E6%8E%A8%E9%80%81message%E5%92%8Crunnable%E7%9A%84%E5%8C%BA%E5%88%AB)
  - [API](#api)
    - [构造函数](#%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0)
    - [主要方法](#%E4%B8%BB%E8%A6%81%E6%96%B9%E6%B3%95)
  - [Handler引发的内存泄漏](#handler%E5%BC%95%E5%8F%91%E7%9A%84%E5%86%85%E5%AD%98%E6%B3%84%E6%BC%8F)
    - [问题](#%E9%97%AE%E9%A2%98)
    - [解决方案](#%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88)
  - [HandlerThread](#handlerthread)
  - [总结](#%E6%80%BB%E7%BB%93)
  - [HandlerThread](#handlerthread-1)
    - [创建](#%E5%88%9B%E5%BB%BA)
    - [getLooper](#getlooper)
    - [运行](#%E8%BF%90%E8%A1%8C)
    - [退出](#%E9%80%80%E5%87%BA)
  - [实战](#%E5%AE%9E%E6%88%98)
    - [利用HandlerThread](#%E5%88%A9%E7%94%A8handlerthread)
    - [在子线程中创建Handler对象](#%E5%9C%A8%E5%AD%90%E7%BA%BF%E7%A8%8B%E4%B8%AD%E5%88%9B%E5%BB%BAhandler%E5%AF%B9%E8%B1%A1)
    - [Handler内存泄漏问题](#handler%E5%86%85%E5%AD%98%E6%B3%84%E6%BC%8F%E9%97%AE%E9%A2%98)
      - [典型错误的使用示例](#%E5%85%B8%E5%9E%8B%E9%94%99%E8%AF%AF%E7%9A%84%E4%BD%BF%E7%94%A8%E7%A4%BA%E4%BE%8B)
      - [问题一：](#%E9%97%AE%E9%A2%98%E4%B8%80)
      - [问题二：](#%E9%97%AE%E9%A2%98%E4%BA%8C)
      - [解决方案1：](#%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%881)
      - [解决方案2：](#%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%882)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->



# 【Android Handler】

> **相关文章：**
> 【[Android 消息机制](http://blog.csdn.net/Rtia33/article/details/79528798)】



## 概述

我们来看下API的介绍：

> Handler是用来结合线程的消息队列来发送、处理“Message对象”和“Runnable对象”的工具。每一个Handler实例之后会关联一个线程和该线程的消息队列。当你创建一个Handler的时候，从这时开始，它就会自动关联到所在的线程/消息队列，然后它就会陆续把Message/Runnalbe分发到消息队列，并在它们出队的时候处理掉。

从官方文档中，我们不难找出其中的关键词，就是“**线程**”。我们都知道，一个涉及到网络操作，耗时操作等的Android应用，都离不开多线程操作，然而，**如**果这时我们允许**并发更新UI**，那么最终**导致控件**的**状态**都是**不可确定**的。所以，我们可以通过对控件进行加锁，在不需要用时解锁，这是一个解决方案之一，但最后很容易造成线程阻塞，效率会非常差。所以，**谷歌采用了只允许在主线程更新UI**，所以作为**线程通信桥梁**的**Handler**也就应运而生了。

## Looper、MessageQueue、Message、Handler的关系

讲到Handler，肯定离不开Looper、MessageQueue、Message这三者和Handler之间的关系，下面简略地带过，具体【见[Android 消息机制](http://blog.csdn.net/Rtia33/article/details/79528798)】。

### Looper

**每一个线程只有一个Looper**，每个线程在初始化Looper之后，然后Looper会维护好该线程的消息队列，用来存放Handler发送的Message，并处理消息队列出队的Message。
它的特点是**它跟它的线程是绑定的**，处理**消息**也是在**Looper所在的线程去处理**，所以当我们在**主线程创建Handler**时，它就会跟主线程唯一的Looper绑定，从而我们使用Handler **在子线程发消息**时，最终也是**在主线程处理**，达到了异步的效果。

那么就会有人问，为什么我们使用Handler的时候从来都不需要创建Looper呢？这是因为在主线程中，ActivityThread默认会把Looper初始化好，prepare以后，当前线程就会变成一个Looper线程。

### MessageQueue

MessageQueue是一个消息队列，用来存放Handler发送的消息。每个线程最多只有**一个MessageQueue**。
MessageQueue通常都是由Looper来管理，而主线程创建时，会创建一个默认的Looper对象，而Looper对象的创建，将自动创建一个MessageQueue。其他非主线程，不会自动创建Looper。

### Message

消息对象，就是MessageQueue里面存放的对象，一个MessageQueu可以包括多个Message。
当我们需要发送一个Message时，我们一般不建议使用new Message()的形式来创建，更**推荐使用Message.obtain()**来获取Message实例，因为在Message类里面定义了一个消息池，当消息池里存在未使用的消息时，便返回，如果没有未使用的消息，则通过new的方式创建返回，所以使用Message.obtain()的方式来获取实例可以大大**减少当有大量Message对象而产生的垃圾回收问题**。

四者关系总体如下（如有不对的地方，谢谢指出）
[![](http://upload-images.jianshu.io/upload_images/9028834-589d32945164d219.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://upload-images.jianshu.io/upload_images/9028834-589d32945164d219.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## Handler的主要用途

1.  推送未来某个时间点将要执行的Message或者Runnable到消息队列。
2.  在子线程把需要在另一个线程执行的操作加入到消息队列中去。

废话不多说，通过举例来说明Handler的两个主要用途。

### 1\. 推送未来某个时间点将要执行的Message或者Runnable到消息队列

实例：通过Handler配合Message或者Runnable实现倒计时

*   首先看一下效果图

[![image](http://upload-images.jianshu.io/upload_images/9028834-bc33d92edf8eed4f..gif?imageMogr2/auto-orient/strip)](http://upload-images.jianshu.io/upload_images/9028834-bc33d92edf8eed4f..gif?imageMogr2/auto-orient/strip)

*   方法一，通过**Handler + Message**的方式实现倒计时。代码如下：
```java
public class MainActivity extends AppCompatActivity {
    private ActivityMainBinding mBinding;
    private Handler mHandler ;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        mBinding = DataBindingUtil.setContentView(this, R.layout.activity_main);
        //设置监听事件
        mBinding.clickBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //通过Handler + Message的方式实现倒计时
                for (int i = 1; i <= 10; i++) {
                    Message message = Message.obtain(mHandler);
                    message.what = 10 - i;
                    mHandler.sendMessageDelayed(message, 1000 * i); //通过延迟发送消息，每隔一秒发送一条消息
                }
            }
        });
        mHandler = new Handler() {
            @Override
            public void handleMessage(Message msg) {
                super.handleMessage(msg);
                mBinding.time.setText(msg.what + "");   //在handleMessage中处理消息队列中的消息
            }
        };
    }
}
```


通过这个小程序，大家可以了解到Handler的一个作用就是：在主线程中，可以通过Handler来处理一些有顺序的操作，让它们在固定的时间点被执行。


*   方法二，通过**Handler + Runnable**的方式实现倒计时。代码如下：

```java
public class MainActivity extends AppCompatActivity {
    private ActivityMainBinding mBinding;
    private Handler mHandler = new Handler();
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        mBinding = DataBindingUtil.setContentView(this, R.layout.activity_main);
        //设置监听事件
        mBinding.clickBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                for (int i = 1; i <= 10; i++) {
                    final int fadedSecond = i;
                    //每延迟一秒，发送一个Runnable对象
                    mHandler.postDelayed(new Runnable() {
                        @Override
                        public void run() {
                            mBinding.time.setText((10 - fadedSecond) + "");
                        }
                    }, 1000 * i);
                }
            }
        });
    }
}
```

方法二也是通过代码让大家加深Handler处理有序事件的用途，之所以分开Runnable和Message两种方法来实现，是因为很多人都搞不清楚为什么Handler可以推送Runnable和Message两种对象。
**其实，无论Handler将Runnable还是Message加入MessageQueue，最终都只是将Message加入到MessageQueue**。Handler的post Runnable对象这个方法只是对post Message进行了一层封装，所以最终我们都是通过Handler推送了一个Message罢了。

### 2\. 在子线程把需要在另一个线程执行的操作加入到消息队列中去

#### 实例：通过Handler + Message来实现子线程加载图片，在UI线程显示图片

*   效果图如下

[![](http://upload-images.jianshu.io/upload_images/9028834-1fea35c48f0b90ba..gif?imageMogr2/auto-orient/strip)](http://upload-images.jianshu.io/upload_images/9028834-1fea35c48f0b90ba..gif?imageMogr2/auto-orient/strip)

*   代码如下(布局代码也不放出来了)

```java
public class ThreadActivity extends AppCompatActivity implements View.OnClickListener {
    private ActivityThreadBinding mBinding = null;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        mBinding = DataBindingUtil.setContentView(this, R.layout.activity_thread);
        // 设置点击事件
        mBinding.clickBtn.setOnClickListener(this);
        mBinding.resetBtn.setOnClickListener(this);
    }
    @Override
    public void onClick(View v) {
        switch (v.getId()) {
            // 响应load按钮
            case R.id.clickBtn:
                // 开启一个线程
                new Thread(new Runnable() {
                    @Override
                    public void run() {
                        // 在Runnable中进行网络读取操作，返回bitmap
                        final Bitmap bitmap = loadPicFromInternet();
                        // 在子线程中实例化Handler同样是可以的，只要在构造函数的参数中传入主线程的Looper即可
                        Handler handler = new Handler(Looper.getMainLooper());
                        // 通过Handler的post Runnable到UI线程的MessageQueue中去即可
                        handler.post(new Runnable() {
                            @Override
                            public void run() {
                                // 在MessageQueue出队该Runnable时进行的操作
                                mBinding.photo.setImageBitmap(bitmap);
                            }
                        });
                    }
                }).start();
                break;
            case R.id.resetBtn:
                mBinding.photo.setImageBitmap(BitmapFactory.decodeResource(getResources(), R.mipmap.default_pic));
                break;
        }
    }

    /* HttpUrlConnection加载图片 */
    public Bitmap loadPicFromInternet() {
        Bitmap bitmap = null;
        int respondCode = 0;
        InputStream is = null;
        try {
            URL url = new URL("https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=1421494343,3838991329&fm=23&gp=0.jpg");
            HttpURLConnection connection = (HttpURLConnection) url.openConnection();
            connection.setRequestMethod("GET");
            connection.setConnectTimeout(10 * 1000);
            connection.setReadTimeout(5 * 1000);
            connection.connect();
            respondCode = connection.getResponseCode();
            if (respondCode == 200) {
                is = connection.getInputStream();
                bitmap = BitmapFactory.decodeStream(is);
            }
        } catch (MalformedURLException e) {
            e.printStackTrace();
            Toast.makeText(getApplicationContext(), "访问失败", Toast.LENGTH_SHORT).show();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (is != null) {
                try {
                    is.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
        return bitmap;
    }
}
```

## Handler推送Message和Runnable的区别

在上文我们通过用Handler推送Message和Runnable实现相同的倒计时效果，这里我们就说一下Post(Runnable)和SendMessage(Message)的区别。

首先我们看看post方法和sendMessage方法的源码：

```java
    public final boolean post(Runnable r){
       return  sendMessageDelayed(getPostMessage(r), 0);
    }
```

```java
    public final boolean sendMessage(Message msg) {
        return sendMessageDelayed(msg, 0);
    }
```

可见，两个方法都是通过调用sendMessageDelayed方法实现的，所以可以知道它们的底层逻辑是一致的。

但是，post方法的底层调用sendMessageDelayed的时候，却是通过getPostMessage(r)来将Runnable对象来转为Message，我们点进方getPostMessage()法可以看到：

```java
    private static Message getPostMessage(Runnable r) {
        Message m = Message.obtain();
        m.callback = r;
        return m;
    }
```

其实，最终runnable最终也是转化为一个Message，而这个Message只有一个被赋值的成员变量，就是Runnable的回调函数，也就是说，这个Message在进入MessageQueue之后，它只是一个“动作”，即我们Runnbale的run方法里面的操作。

要知道，我们的Message类可是有很多参数的，所以你可以理解为它是一个非常丰富的JavaBean，可以看看它的成员变量：

*   public int what;
*   public int arg1;
*   public int arg2;
*   public Object obj;
*   ...

那么讲到这里，大家也应该有所理解为什么Google工程师为什么会封装这两种方法，我总结如为：**为了更方便开发者根据不同需要进行调用**。
当我们需要**传输很多数据**时，我们可以使用sendMessage来实现，因为通过给**Message**的不同成员变量赋值可以封装成数据非常丰富的对象，从而进行传输；
当我们**只需要进行一个动作**时，直接使用**Runnable**，在run方法中实现动作内容即可。当然我们也可以通过Message.obtain(Handler h, Runnable callback)来传入callback接口，但这样看起来就没有post(Ruannable callback)那么直观。

## API

API是我们学习最好的文档

### 构造函数

*   Handler()
*   Handler(Handler.Callback callback)：传入一个实现的Handler.Callback接口，接口只需要实现handleMessage方法。
*   Handler(Looper looper)：将Handler关联到任意一个线程的Looper，在实现子线程之间通信可以用到。
*   Handler(Looper looper, Handler.Callback callback)

### 主要方法

*   **void dispatchMessage (Message msg)**
  一般情况下不会使用，因为它的底层实现其实是作为处理系统消息的一个方法，如果真要用，效果和sendMessage(Message m)效果一样。
  ```java
  public void dispatchMessage(Message msg) {
      if (msg.callback != null) {
          // 如果有Runnbale，则直接执行它的run方法
          handleCallback(msg);
      } else {
          //如果有实现自己的callback接口
          if (mCallback != null) {
              //执行callback的handleMessage方法
              if (mCallback.handleMessage(msg)) {
                  return;
              }
          }
          //否则执行自身的handleMessage方法
          handleMessage(msg);
      }
  }
  private static void handleCallback(Message message) {
      message.callback.run();
  }
  ```

*   **void dump (Printer pw, String prefix)**
  主要Debug时使用的一个方法，dump函数只是使用了Printer对象进行了打印，打印出Handler以及Looper和Queue中的一些信息，源码如下：
  ```java
  public final void dump(Printer pw, String prefix) {
      pw.println(prefix + this + " @ " + SystemClock.uptimeMillis());
      // 如果Looper为空，输出Looper没有初始化
      if (mLooper == null) {
          pw.println(prefix + "looper uninitialized");
      } else {
          // 否则调用Looper的dump方法，Looper的dump方法也是
          mLooper.dump(pw, prefix + "  ");
      }
  }
  ```
  通过测试用例大家会了解得更清晰：
  ```java
        //测试代码
        Printer pw = new LogPrinter(Log.ERROR, "MyTag");
        mHandler.dump(pw, "prefix");
  ```
  结果：
  [![](http://upload-images.jianshu.io/upload_images/9028834-2136c80dca913662..png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://upload-images.jianshu.io/upload_images/9028834-2136c80dca913662..png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   **Looper getLooper ()**
  拿到Handler相关联的Looper

*   **String getMessageName (Message message)**
  获取Message的名字，默认名字为message.what的值。

*   **void handleMessage (Message msg)**
  处理消息。

*   **boolean hasMessages (int what)**
  判断是否有Message的what值为参数what。

*   **boolean hasMessages (int what, Object object)**
  判断是否有Message的what值为参数what，obj值为参数object。

*   **Message obtainMessage (int what, Object obj)**
  从消息池中拿到一个消息并赋值what和obj，其他重载函数同理。

*   **boolean post (Runnable r)**
  将Runnable对象加入MessageQueue。

*   **boolean post (Runnable r)**
  将Runnbale加入到消息队列的队首。但是官方不推荐这么做，因为很容易打乱队列顺序。

*   **boolean postAtTime (Runnable r, Object token, long uptimeMillis)**
  在某个时间点执行Runnable r。

*   **boolean postDelayed (Runnable r, long delayMillis)**
  当前时间延迟delayMillis个毫秒后执行Runnable r。

*   **void removeCallbacks (Runnable r, Object token)**
  移除MessageQueue中的所有Runnable对象。

*   **void removeCallbacksAndMessages (Object token)**
  移除MessageQueue中的所有Runnable和Message对象。

*   **void removeMessages (int what)**
  移除所有what值得Message对象。

*   **boolean sendEmptyMessage (int what)**
  直接拿到一个空的消息，并赋值what，然后发送到MessageQueue。

*   **boolean sendMessageDelayed (Message msg, long delayMillis)**
  在延迟delayMillis毫秒之后发送一个Message到MessageQueue。

## Handler引发的内存泄漏

在上面的例子中，为了展示方便，我都没有考虑内存泄漏的情况，但是在实际开发中，如果不考虑代码的安全性的话，尤其当一个项目到达了一定的规模之后，那么对于代码的维护和系统的调试都是非常困难的。而Handler的内存泄漏在Android中也是一个非常经典的案例。

详细可以参考：[How to Leak a Context: Handlers & Inner Classes](http://www.androiddesignpatterns.com/2013/01/inner-class-handler-memory-leak.html)

或参考翻译文：[Android中Handler引起的内存泄露](http://droidyue.com/blog/2014/12/28/in-android-handler-classes-should-be-static-or-leaks-might-occur/)

通常我们都会在一个Activity内部定义一个Handler的内部类：

```java
public class MainActivity extends AppCompatActivity {
    private Handler mHandler = new Handler(){
        @Override
        public void handleMessage(Message msg) {
            super.handleMessage(msg);
            switch (msg.what){
                 ...
            }
        }
    };
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        mBinding = DataBindingUtil.setContentView(this, R.layout.activity_main);
        mHandler.postDelayed(new Runnable() {
            @Override
            public void run() {
                ...
            }
        }, 1000000);
    }
}
```
### 问题
1. 外部类**Activity中定义了一个非静态内部类Handler**，**非静态内部类默认持有对外部类的引用**。
  如果外部**Activity**突然**关闭**了，**但**是**MessageQueue**中的消息还**没处理完**，那么**Handler**就会**一直持有**对外部**Activty的引用**，垃圾回收器**无法回收Activity**，从而导致**内存泄漏**。

2. 如上代码，在postDelayed中，我们在**参数中传入一个非静态内部类Runnable**，这同样会造成**内存泄漏**。
  假如此时关闭了Activity，那么垃圾回收器在接下来的1000000ms内都无法回收Activity，造成内存泄漏。

### 解决方案
1. 将**非静态内部类Handler和Runnable转为静态内部类**，因为非静态内部类(匿名内部类)都会默认持有对外部类的强引用。

2. 改成静态内部类后，**对外部类的引用设为弱引用**，因为在垃圾回收时，会自动将弱引用的对象回收。

避免内存泄漏的例子：
```java
public class HandlerActivity extends AppCompatActivity {
    private final MyHandler mHandler = new MyHandler(this);
    private static final Runnable mRunnable = new Runnable() {
        @Override
        public void run() {
            // 操作
        }
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_fourth);
        mHandler.postDelayed(mRunnable, 1000*10);
        finish();   
    }

    private static class MyHandler extends Handler {
        WeakReference<HandlerActivity> mWeakActivity;
        public MyHandler(HandlerActivity activity) {
            this.mWeakActivity = new WeakReference<HandlerActivity>(activity);
        }

        @Override
        public void handleMessage(Message msg) {
            super.handleMessage(msg);
            final HandlerActivity mActivity = mWeakActivity.get();
            if (mActivity != null) {
                // 处理消息
            }
        }
    }
}
```


## HandlerThread

思考一下，假如我们需要同时下载A和B，下载A需要6s，下载B需要5s，在它们下载完成后Toast信息出来即可，此时HandlerThread便是一种解决方式之一。那么HandlerThread到底是什么？

*   HandlerThread就是一种线程。
*   HandlerThread和普通的Thread之间的区别就**是HandlerThread在创建的时候会提供自己该线程的Looper对象**。

我们在Actvity创建时系统会自动帮我们初始化好主线程的Looper，然后这个Looper就会管理主线程的消息队列。但是在我们创建子线程时，系统并不会帮我们创建子线程的Looper，需要我们自己手动创建，如下：
```java
    new Thread(){
        @Override
        public void run() {
            super.run();
            Looper.prepare();
            Handler mHandler = new Handler(Looper.myLooper());
            Looper.loop();
        }
    }.start();
```

所以HandlerThread就在内部帮我们封装了Looper的创建过程，从源码可以看到，HandlerThread集成于Thread，然后覆写run方法，进行Looper的创建，从而通过getLooper方法暴露出该线程的Looper对象

```java
public class HandlerThread extends Thread {
    int mPriority;
    int mTid = -1;
    Looper mLooper;
    ...
    @Override
    public void run() {
        mTid = Process.myTid();
        Looper.prepare();
        synchronized (this) {
            mLooper = Looper.myLooper();
            notifyAll();
        }
        Process.setThreadPriority(mPriority);
        onLooperPrepared();
        Looper.loop();
        mTid = -1;
    }
    public Looper getLooper() {
        if (!isAlive()) {
            return null;
        }
        // If the thread has been started, wait until the looper has been created.
        synchronized (this) {
            while (isAlive() && mLooper == null) {
                try {
                    wait();
                } catch (InterruptedException e) {
                }
            }
        }
        return mLooper;
    }
    ...
}    
```

所以通过HandlerThread，我们可以轻松创建一个包含了Looper的子线程：

```java
final HandlerThread mHandlerThread = new HandlerThread("HandlerThread");
mHandlerThread.start();
Handler mHandler = new Handler(mHandlerThread.getLooper());
```

使用HandlerThread同时下载A和B的Demo：

[![image](http://upload-images.jianshu.io/upload_images/9028834-53e647d5ebc1355e..gif?imageMogr2/auto-orient/strip)](http://upload-images.jianshu.io/upload_images/9028834-53e647d5ebc1355e..gif?imageMogr2/auto-orient/strip)

代码：
```java
public class HandlerThreadActivity extends AppCompatActivity {
    private TextView tv_A, tv_B;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_handler_thread);
        tv_A = (TextView) findViewById(R.id.txt_dlA);
        tv_B = (TextView) findViewById(R.id.txt_dlB);
        
        final Handler mainHandler = new Handler();

        final HandlerThread downloadAThread = new HandlerThread("downloadAThread");
        downloadAThread.start();
        Handler downloadAHandler = new Handler(downloadAThread.getLooper());

        // 通过postDelayed模拟耗时操作
        downloadAHandler.postDelayed(new Runnable() {
            @Override
            public void run() {
                Toast.makeText(getApplicationContext(), "下载A完成", Toast.LENGTH_SHORT).show();
                mainHandler.post(new Runnable() {
                    @Override
                    public void run() {
                        tv_A.setText("A任务已经下载完成");
                    }
                });
            }
        }, 1000 * 5);

        final HandlerThread downloadBThread = new HandlerThread("downloadBThread");
        downloadBThread.start();
        Handler downloadBHandler = new Handler(downloadBThread.getLooper());

        // 通过postDelayed模拟耗时操作
        downloadBHandler.postDelayed(new Runnable() {
            @Override
            public void run() {
                Toast.makeText(getApplicationContext(), "下载B完成", Toast.LENGTH_SHORT).show();
                mainHandler.post(new Runnable() {
                    @Override
                    public void run() {
                        tv_B.setText("B任务已经下载完成");
                    }
                });

            }
        }, 1000 * 7);
    }
}
```

## 总结

由于Android的UI更新只能在主线程，所以Handler是Android中一套非常重要的更新UI线程机制，虽然在很多框架的帮助下我们可以减少了很多Handler的代码编写，但实际上很多框架的底层实现都是通过Handler来更新UI的，所以可见掌握好Handler对我们来说是多么重要，所以这也是很多面试官在面试中的高频考点之一。虽然Handler对开发者来说是一个非常方便的存在，但是我们也不能否认它也是存在缺点的，如处理不当，Handler所造成的的内存泄漏对开发者来说也是一个非常头疼的难题。















## HandlerThread

上文已经讲解了消息机制，那么对于Handler的用法，往往是在一个线程中运行Looper，其他线程通过Handler来发送消息到Looper所在线程，这里涉及线程间的通信。既然涉及多个线程的通信，会有同步的问题，Android对此直接提供了HandlerThread类，下面来讲讲HandlerThread类的设计。

### 创建

HandlerThread 继承于 Thread类

```java
public HandlerThread(String name) {
    super(name);
    mPriority = Process.THREAD_PRIORITY_DEFAULT; //默认优先级
}

public HandlerThread(String name, int priority) {
    super(name);
    mPriority = priority;
}
```

### getLooper
获取HandlerThread线程中的Looper对象

```java
public Looper getLooper() {
    // 当线程没有启动或者已经结束时，则返回null
    if (!isAlive()) {
        return null;
    }

    //当线程已经启动，则等待直到looper创建完成
    synchronized (this) {
        while (isAlive() && mLooper == null) {
            try {
                wait(); //休眠等待
            } catch (InterruptedException e) {
            }
        }
    }
    return mLooper;
}
```

### 运行

```java
@Override
public void run() {
    mTid = Process.myTid();  //获取线程的tid
    Looper.prepare();   // 创建Looper对象
    synchronized (this) {
        mLooper = Looper.myLooper(); //获取looper对象
        notifyAll(); //唤醒等待线程
    }
    Process.setThreadPriority(mPriority);
    onLooperPrepared();  // 该方法可通过覆写，实现自己的逻辑
    Looper.loop();   //进入循环模式
    mTid = -1;
}
```

### 退出
```java
public boolean quit() {
    Looper looper = getLooper();
    if (looper != null) {
        looper.quit(); //普通退出
        return true;
    }
    return false;
}

public boolean quitSafely() {
    Looper looper = getLooper();
    if (looper != null) {
        looper.quitSafely(); //安全退出
        return true;
    }
    return false;
}
```

quit()与quitSafely()的区别，仅仅在于是否移除当前正在处理的消息。移除当前正在处理的消息可能会出现不安全的行为。

## 实战

### 利用HandlerThread

很多时候，在HandlerThread线程中运行Loop()方法，在其他线程中通过Handler发送消息到HandlerThread线程。通过wait/notifyAll的方式，有效地解决了多线程的同步问题。

示例代码：

```java
// Step 1: 创建并启动HandlerThread线程，内部包含Looper
HandlerThread handlerThread = new HandlerThread("gityuan.com");
handlerThread.start();

// Step 2: 创建Handler
Handler handler = new Handler(handlerThread.getLooper());

// Step 3: 发送消息
handler.post(new Runnable() {
        @Override
        public void run() {
            System.out.println("thread id="+Thread.currentThread().getId());
        }
    });
```

或者 handler.postDelayed(Runnable r, long delayMillis)用于延迟执行。

### 在子线程中创建Handler对象

示例代码：

```java
class LooperThread extends Thread {
    public Handler mHandler;

    public void run() {
        Looper.prepare();
        // Step 1: 创建Handler
        mHandler = new Handler() {
            public void handleMessage(Message msg) {
                //TODO 处理即将发送过来的消息
            }
        };
        Looper.loop();
    }
}

// Step 2: 创建并启动LooperThread线程，内部包含Looper
LooperThread looperThread = new LooperThread("gityuan.com");
looperThread.start();

// Step 3: 发送消息
LooperThread.mHandler.sendEmptyMessage(10);
```



### Handler内存泄漏问题

#### 典型错误的使用示例
```java
public class LeakActivity extends AppCompatActivity {
    private int a = 10;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_leak);
        mHandler.sendEmptyMessageDelayed(0, 5000);
    }

    //也是匿名内部类，也会引用外部
    private Handler mHandler = new Handler() {
        @Override
        public void handleMessage(Message msg) {
            switch (msg.what) {
            case 0:
                a = 20;
                break;
            }
        }
    };
}
```

分析：这是我们用得最多的用法，**Handler隐式地引用了Activity（通过变量a）**。**Handler的生命周期有可能与Activity的生命周期不一致**，比如栗子中的sendEmptyMessageDelayed，在5000毫秒之后才发送消息，但是很有可能这时候Activity被返回了，这样会造成**Handler比Activity还要长寿**，这样会**导致Activity发生暂时性的内存泄漏**。

#### 问题一：
为了解决这个问题，我们可以**把Handler改为static的**，但是这样会造成**Handler无法访问Activity的非静态变量**a。
```java
private static Handler mHandler = new Handler() {
    @Override
    public void handleMessage(Message msg) {
        switch (msg.what) {
        case 0:
            //a = 20;   //不能访问得到
            break;
        }
    }
};
```

#### 问题二：
通过把**Activity作为Handler成员变量**，在Handler构造的时候传进来即可。这时候我们不能使用匿名内部类了，**需**要把**Handler单独抽取成一个类**，这样就可以访问Activity的非静态变量了。但是我们的问题又回来了，这时候**Handler持有了Activity的强引用**了，这样不就是回到我们的原点了吗？（**内存泄漏问题依然没有解决**）

```java
private static class UIHandler extends Handler {
    private LeakActivity mActivity;//外部类的强引用
    public UIHandler(LeakActivity activity) {
        mActivity = activity;
    }
    @Override
    public void handleMessage(Message msg) {
        super.handleMessage(msg);
        mActivity.a = 20;
    }
}
```

#### 解决方案1：
把**Activity通过弱引用来作为成员变量**。虽然我们把Activity作为弱引用，但是Activity不一定就是会在GC的时候被回收，因为可能还有其他对象引用了Activity。在处理消息的时候就要**注意**了，**当Activity回收或者正在finish的时候，就不能继续处理消息**了。

```java
private static class UIHandler extends Handler {
    private WeakReference<LeakActivity> mActivityRef;//GC的时候会回收
    public UIHandler(LeakActivity activity) {
        mActivityRef = new WeakReference<>(activity);//弱引用
    }

    @Override
    public void handleMessage(Message msg) {
        super.handleMessage(msg);
        //当使用弱引用的时候，会回收Activity吗？
        //虽然用的是弱引用，但是并不代表不存在其他的对象没有引用Activity，因此不一定会被回收
        LeakActivity activity = mActivityRef.get();//获取Activity
        if (activity == null || activity.isFinishing()) {//当Activity回收或者正在finish的时候，不能继续处理消息
            return;
        }
        mActivityRef.get().a = 20;
    }
}
```

#### 解决方案2：
创建一个静态Handler内部类，然后对Handler持有的对象使用弱引用，这样在GC可以顺利回收Handler持有的对象，这样就避免了Activity的泄漏。
但是，每次在Activity里面使用Handler都这么写会不会很累啊。
其实还有一个更加简单方法：**mHandler.removeCallbacksAndMessages(null);**是移除消息队列中所有Message。清空消息队列也就意味着这个Handler的对象被打回原形，GC可以很顺利一棍子将其打死。
所以，**我们平时在Activity或Fragment使用Handler避免潜在的内存泄露的正确姿势就是onDestroy方法或onStop方法中调用一下Handler的removeCallbacksAndMessages(null);就可以了**。

```java
public class TestActivity extends AppCompatActivity {
    @Override
    protected void onDestroy() {
        super.onDestroy();
        mHandler.removeCallbacksAndMessages(null);
    }
}
```












**引用：**
★★★★[Android基础夯实--你了解Handler有多少？](http://www.cnblogs.com/ryanleee/p/8204450.html)
★★★[Android消息机制3-Handler(实战)](http://gityuan.com/2016/01/01/handler-message-usage/)
★★[Android 源码分析之旅3.1--消息机制源码分析](https://www.jianshu.com/p/ac50ba6ba3a2)




