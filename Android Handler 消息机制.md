# 【Android Handler 消息机制】
## 前言

在Android开发中，我们都知道**不能在主线程中执行耗时的任务**，**避免ANR**。
>Android中**主线程**也叫**UI线程**，那么从名字上我们也知道主线程主要是用来**创建、更新UI**的，而其他耗时操作，比如网络访问，或者文件处理，多媒体处理等都需要在子线程中操作。
>之所以**在子线程中操作是为了保证UI的流畅程度**，手机显示的刷新频率是60Hz，也就是一秒钟刷新60次，**每16.67毫秒刷新一次**，为了不丢帧，那么主线程处理代码最好不要超过16毫秒。

当子线程处理完数据后，为了防止UI处理逻辑的混乱，Android只允许主线程修改UI，那么这时候就需要Handler来充当子线程和主线程之间的桥梁了。**Handler**机制在Android**多线程**编程中可以说是不可或缺的角色。

### 消息机制概述

Android系统在设计的初期就已经考虑到了UI的更新问题，由于Android中的View是线程不安全的，然而程序中异步处理任务结束后更新UI元素也是必须的。这就造成了一个矛盾，最简单的解决方法肯定是给View加同步锁使其变成线程安全的。这样做不是不可以，只是会有两点坏处： 

1. 加锁会有导致效率底下
2. 由于可以在多个地方更新UI，开发就必须很小心操作，开发起来就很麻烦，一不小心就出错了。

基于以上两个缺点，谷歌设置一个线程专门处理UI控件的更新，如果其他线程也需要对UI进行更新，必须把想做的告诉那个专门处理UI线程的家伙，让它帮你做。大家各有各的任务，井水不犯河水，各司其职。

**消息机制简单概括**：其他线程通过给特定线程发送消息，将某项专职的工作，交给这个特定的线程去做。比如说其他线程都把处理UI显示的工作通过发送消息交给UI线程去做。 

**实现原理**呢，我是这么理解的，现在要做的主要工作就是**切换线程**去操作，怎么切换呢？把两条线程看作是两条并行的公路，如果要从一条公路转到另一条公路上，要怎么做呢？是不是只要找到两条公路交叉或者共用某个资源的地方，如果说交叉路口，比如说加油站。当然了，**线程**是不存在交叉的地方的，那么可以考虑他们**公用资源**的地方，不同的进程享用不同的内存空间，但是**同一个进程的不同线程享用的是同一片内存空间**，那让其他线程把要处理的消息放到这个特定的内存空间上，处理消息的线程来这个内存空间上来取消息去处理不就可以了吗。事实正是如此，在Android的消息机制中，扮演这个**特定内存空间**的对象就是**MessageQueue**对象，**发送和处理的消息**就是**Message**对象。其他的**Handler**和**Looper**都是为了**配合线程切换**用的。 
其实不仅仅是线程之间，不同进程之间进行消息传递（IPC机制），也是这个思路，找到公用的一个资源点，文件系统啊，共享内存啊等等。
# Handler
## Handler的主要用途

1. 推送未来某个时间点将要执行的Message或者Runnable到消息队列。
2. 在子线程把需要在另一个线程执行的操作加入到消息队列中去。

废话不多说，通过举例来说明Handler的两个主要用途。

### 1\. 推送未来某个时间点将要执行的Message或者Runnable到消息队列

实例：通过Handler配合Message或者Runnable实现倒计时

- 首先看一下效果图

[![](http://upload-images.jianshu.io/upload_images/9028834-bc33d92edf8eed4f..gif?imageMogr2/auto-orient/strip)](http://upload-images.jianshu.io/upload_images/9028834-bc33d92edf8eed4f..gif?imageMogr2/auto-orient/strip)

- 方法一，通过**Handler + Message**的方式实现倒计时。代码如下：

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

- 方法二，通过**Handler + Runnable**的方式实现倒计时。代码如下：

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

- 效果图如下

[![](http://upload-images.jianshu.io/upload_images/9028834-1fea35c48f0b90ba..gif?imageMogr2/auto-orient/strip)](http://upload-images.jianshu.io/upload_images/9028834-1fea35c48f0b90ba..gif?imageMogr2/auto-orient/strip)

- 代码如下(布局代码也不放出来了)

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

- public int what;
- public int arg1;
- public int arg2;
- public Object obj;
- ...

那么讲到这里，大家也应该有所理解为什么Google工程师为什么会封装这两种方法，我总结如为：**为了更方便开发者根据不同需要进行调用**。
当我们需要**传输很多数据**时，我们可以使用**sendMessage**来实现，因为通过给**Message**的不同成员变量赋值可以封装成数据非常丰富的对象，从而进行传输；
当我们**只需要进行一个动作**时，直接使用**Runnable**，在run方法中实现动作内容即可。当然我们也可以通过Message.obtain(Handler h, Runnable callback)来传入callback接口，但这样看起来就没有post(Ruannable callback)那么直观。

## API

API是我们学习最好的文档

### 构造函数

- Handler()
- Handler(Handler.Callback callback)：传入一个实现的Handler.Callback接口，接口只需要实现handleMessage方法。
- Handler(Looper looper)：将Handler关联到任意一个线程的Looper，在实现子线程之间通信可以用到。
- Handler(Looper looper, Handler.Callback callback)

### 主要方法

- **void dispatchMessage (Message msg)**
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

- **void dump (Printer pw, String prefix)**
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

- **Looper getLooper ()**
  拿到Handler相关联的Looper

- **String getMessageName (Message message)**
  获取Message的名字，默认名字为message.what的值。

- **void handleMessage (Message msg)**
  处理消息。

- **boolean hasMessages (int what)**
  判断是否有Message的what值为参数what。

- **boolean hasMessages (int what, Object object)**
  判断是否有Message的what值为参数what，obj值为参数object。

- **Message obtainMessage (int what, Object obj)**
  从消息池中拿到一个消息并赋值what和obj，其他重载函数同理。

- **boolean post (Runnable r)**
  将Runnable对象加入MessageQueue。

- **boolean post (Runnable r)**
  将Runnbale加入到消息队列的队首。但是官方不推荐这么做，因为很容易打乱队列顺序。

- **boolean postAtTime (Runnable r, Object token, long uptimeMillis)**
  在某个时间点执行Runnable r。

- **boolean postDelayed (Runnable r, long delayMillis)**
  当前时间延迟delayMillis个毫秒后执行Runnable r。

- **void removeCallbacks (Runnable r, Object token)**
  移除MessageQueue中的所有Runnable对象。

- **void removeCallbacksAndMessages (Object token)**
  移除MessageQueue中的所有Runnable和Message对象。

- **void removeMessages (int what)**
  移除所有what值得Message对象。

- **boolean sendEmptyMessage (int what)**
  直接拿到一个空的消息，并赋值what，然后发送到MessageQueue。

- **boolean sendMessageDelayed (Message msg, long delayMillis)**
  在延迟delayMillis毫秒之后发送一个Message到MessageQueue。



## Handler内存泄漏问题


在上面的例子中，为了展示方便，我都没有考虑内存泄漏的情况，但是在实际开发中，如果不考虑代码的安全性的话，尤其当一个项目到达了一定的规模之后，那么对于代码的维护和系统的调试都是非常困难的。而Handler的内存泄漏在Android中也是一个非常经典的案例。

详细可以参考：[How to Leak a Context: Handlers & Inner Classes](http://www.androiddesignpatterns.com/2013/01/inner-class-handler-memory-leak.html)

或参考翻译文：[Android中Handler引起的内存泄露](http://droidyue.com/blog/2014/12/28/in-android-handler-classes-should-be-static-or-leaks-might-occur/)

### 典型错误的使用示例

```java
public class LeakActivity extends AppCompatActivity {
    private int a = 10;
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
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_leak);
        mHandler.sendEmptyMessageDelayed(0, 5000);
        mHandler.postDelayed(new Runnable() {
            @Override
            public void run() {
                ...
            }
        }, 1000000);
    }
}
```

分析问题：
1. 外部类**Activity中定义了一个非静态内部类Handler**，**非静态内部类默认持有对外部类的引用**。
   如果外部**Activity**突然**关闭**了，**但**是**MessageQueue**中的消息还**没处理完**，那么**Handler**就会**一直持有**对外部**Activty的引用**，垃圾回收器**无法回收Activity**，从而导致**内存泄漏**。
   **Handler的生命周期有可能与Activity的生命周期不一致**，比如栗子中的sendEmptyMessageDelayed，在5000毫秒之后才发送消息，但是很有可能这时候Activity被返回了，这样会造成**Handler比Activity还要长寿**，这样会**导致Activity发生暂时性的内存泄漏**。
1. 如上代码，在postDelayed中，我们在**参数中传入一个非静态内部类Runnable**，这同样会造成**内存泄漏**。
   假如此时关闭了Activity，那么垃圾回收器在接下来的1000000ms内都无法回收Activity，造成内存泄漏。

### 问题一：

为了解决这个问题，我们可以**把Handler改为static的**， 改成静态内部类后，**对外部类的引用设为弱引用**，因为在垃圾回收时，会自动将弱引用的对象回收。
但是这样会造成**Handler无法访问Activity的非静态变量**a。

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

### 问题二：

通过把**Activity作为Handler成员变量**，在Handler构造的时候传进来即可。这时候我们不能使用匿名内部类了，**需**要把**Handler单独抽取成一个类**，这样就可以访问Activity的非静态变量了。但是我们的问题又回来了，这时候**Handler持有了Activity的强引用**了，这样不就是回到我们的原点了吗？（**内存泄漏问题依然没有解决**）

```java
private static class MyHandler extends Handler {
    private LeakActivity mActivity;//外部类的强引用
    public MyHandler(LeakActivity activity) {
        mActivity = activity;
    }
    @Override
    public void handleMessage(Message msg) {
        super.handleMessage(msg);
        mActivity.a = 20;
    }
}
```

### 解决方案1：

把**Activity通过弱引用来作为成员变量**。虽然我们把Activity作为弱引用，但是Activity不一定就是会在GC的时候被回收，因为可能还有其他对象引用了Activity。在处理消息的时候就要**注意**了，**当Activity回收或者正在finish的时候，就不能继续处理消息**了。

```java
public class LeakActivity extends AppCompatActivity {
    private int a = 10;
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
	        //当使用弱引用的时候，会回收Activity吗？
	        //虽然用的是弱引用，但是并不代表不存在其他的对象没有引用Activity，因此不一定会被回收
	        LeakActivity activity = mActivityRef.get();//获取Activity
	        if (activity == null || activity.isFinishing()) {//当Activity回收或者正在finish的时候，不能继续处理消息
	            return;
	        }
	        mActivityRef.get().a = 20;
        }
    }
}
```


## HandlerThread

思考一下，假如我们需要同时下载A和B，下载A需要6s，下载B需要5s，在它们下载完成后Toast信息出来即可，此时HandlerThread便是一种解决方式之一。那么HandlerThread到底是什么？

- HandlerThread就是一种线程。
- HandlerThread和普通的Thread之间的区别就**是HandlerThread在创建的时候会提供自己该线程的Looper对象**。

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
/* HandlerThread 源码 */
public class HandlerThread extends Thread {
    int mPriority;
    int mTid = -1;
    Looper mLooper;
    ...
    public HandlerThread(String name) {
        super(name);
        mPriority = Process.THREAD_PRIORITY_DEFAULT;//默认优先级
    }
    public HandlerThread(String name, int priority) {
        super(name);
        mPriority = priority;
    }
    @Override
    public void run() {
        mTid = Process.myTid(); //获取线程的tid
        Looper.prepare();// 创建Looper对象
        synchronized (this) {
            mLooper = Looper.myLooper();//获取looper对象
            notifyAll();//唤醒等待线程
        }
        Process.setThreadPriority(mPriority);
        onLooperPrepared();// 该方法可通过覆写，实现自己的逻辑
        Looper.loop();//进入循环模式
        mTid = -1;
    }
    public Looper getLooper() {//获取HandlerThread线程中的Looper对象
        if (!isAlive()) {// 当线程没有启动或者已经结束时，则返回null
            return null;
        }
        // If the thread has been started, wait until the looper has been created.
        //当线程已经启动，则等待直到looper创建完成
        synchronized (this) {
            while (isAlive() && mLooper == null) {
                try {
                    wait();//休眠等待
                } catch (InterruptedException e) {
                }
            }
        }
        return mLooper;
    }
    //退出 
    //quit()与quitSafely()的区别，仅仅在于是否移除当前正在处理的消息。移除当前正在处理的消息可能会出现不安全的行为。
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
    ...
}    
```

### 应用
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

#### 实例：使用HandlerThread同时下载A和B

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



# 消息机制
## MessageQueue
每个线程最多只有**一个MessageQueue**。
MessageQueue通常都是由Looper来管理，而主线程创建时，会创建一个默认的Looper对象，而Looper对象的创建，将自动创建一个MessageQueue。其他非主线程，不会自动创建Looper。

消息队列，用来**保存handler.sendMessage(msg)或者handler.post(r，delayTime)时的产生消息**，虽然名字叫做队列，但是在实现中，这只是一个**单链表的结构**，学过数据结构的朋友都知道，链表用于插入和删除很方便。
要注意的是，MessageQueue **只负责保存**消息，并**不**会**处理**消息。

**MessageQueue**只包含**两个主要**的**操作**：
### enqueueMessage
**插入**一条消息
```java
boolean enqueueMessage(Message msg, long when)
```

### next
**取出**一条消息并且把这条消息从消息队列中移除
```java
Message next()
```
```java
/* MessageQueue源码 */
Message next() {
    ...
    for (;;) {
        ...
    }
}
```
可以看出，**next函数是一个无限循环的函数**，如果消息队列中没有消息，那么next函数会一直阻塞在这里，直到有消息过来。





## Looper
**每一个线程只有一个Looper**，每个线程在初始化Looper之后，然后Looper会维护好该线程的消息队列，用来存放Handler发送的Message，并处理消息队列出队的Message。
它的特点是**它跟它的线程是绑定的**，处理**消息**也是在**Looper所在的线程去处理**，所以当我们在**主线程创建Handler**时，它就会跟主线程唯一的Looper绑定，从而我们使用Handler **在子线程发消息**时，最终也是**在主线程处理**，达到了异步的效果。

为什么我们使用Handler的时候从来都不需要创建Looper呢？这是因为在主线程中，ActivityThread默认会把Looper初始化好，prepare以后，当前线程就会变成一个Looper线程。

### 子线程创建Handler
```java
//如下代码会报异常：java.lang.RuntimeException: Can't create handler inside thread that has not called Looper.prepare()
new Thread(new Runnable() {    
    @Override
    public void run() {        
        Handler handler = new Handler();
        handler.sendEmptyMessageDelayed(0,1000);
    }
}).start();
```
我们在主线程中使用Handler并没有问题，但如上代码在**子线程**里面创建了一个**handler**，使用这个handler去处理事情，就会出现**异常**：`java.lang.RuntimeException: Can't create handler inside thread that has not called Looper.prepare()`。因为**Handler的消息是由Looper进行处理**的，但是你并没有创建Looper对象，所以会产生这个异常。
在主线程中使用了Handler没问题，是因为在**主线程**中，**系统**会**默认**为我们**创建一个Looper对象**。


上面的代码**如何解决**呢？我们创建一个Looper并调用loop即可。
```java
//子线程中创建Handler正确方式
new Thread(new Runnable() {    
    @Override
    public void run() {        
        Looper.prepare();//创建Looper
        Handler handler = new Handler();
        handler.sendEmptyMessageDelayed(0,1000);
        Looper.loop();//一定要调用loop()方法,否则我们只是单单创建了一个Looper
    }
}).start();
```

### parpare
**创建looper对象**并不是使用Looper loop = new Looper()，而是直接调用函数**`Looper.parpare()`**即可。我们跟踪这个函数看看：
```java
/* Looper.parpare() */
public static void prepare() {
    prepare(true);
}
private static void prepare(boolean quitAllowed) {    
    if (sThreadLocal.get() != null) {        
        throw new RuntimeException("Only one Looper may be created per thread");
    }
    sThreadLocal.set(new Looper(quitAllowed));
}
private Looper(boolean quitAllowed) {    
    //在这里创建了一个消息队列
    mQueue = new MessageQueue(quitAllowed);
    mThread = Thread.currentThread();
}
```

在**Looper构造函数里**面Looper会**创建一个MessageQueue**对象。

### loop
**Looper**类中的函数**`loop()`**不断的**调用MessageQueue**的**`next()`**函数来查看**是否有新消息**，如果**有**马上去**处理**这个**消息**。
既然MessageQueue的**next()**函数有**可能**会**堵塞**，所以这就导致了**loop()**函数**堵塞**。
```java
/* Looper.parpare() */
public static void loop() {
    ...
    for (;;) {
        Message msg = queue.next(); // might block
        if (msg == null) {
            // No message indicates that the message queue is quitting.
            return;
        }
        ...
            msg.target.dispatchMessage(msg);//处理消息
        ...
    }
}
```
看loop函数的源码可知，当MessageQueue的next返回新消息,**Looper**就会**处理**这条**消息** : **`msg.target.dispatchMessage(msg);`**
这里的**msg.target就是发送这条消息的Handler**，这样子Handler发送的消息最终又要交给它的dispatchMessage函数来处理。

接下来我们就分析一下Handler的内部原理。
## Handler
一般,我们通过Handler的**post**或者**send**的一系列函数来发送消息，但是其实不管是send还是post，**最终都是通过send**函数来实现的。
**send**系列函数有很多个，但是**最终**都是**调用`sendMessageAtTime`**(Message msg, long uptimeMillis)
```java
/* Handler源码 */
//post最终都是调用send
public final boolean post(Runnable r){
   return  sendMessageDelayed(getPostMessage(r), 0);
}
public final boolean postAtTime(Runnable r, long uptimeMillis){
    return sendMessageAtTime(getPostMessage(r), uptimeMillis);
}
public final boolean postAtTime(Runnable r, Object token, long uptimeMillis){
    return sendMessageAtTime(getPostMessage(r, token), uptimeMillis);
}

public final boolean postDelayed(Runnable r, long delayMillis){
    return sendMessageDelayed(getPostMessage(r), delayMillis);
}
```
```java
public final boolean sendMessage(Message msg){
    return sendMessageDelayed(msg, 0);
}
public final boolean sendEmptyMessage(int what){
    return sendEmptyMessageDelayed(what, 0);
}
public final boolean sendEmptyMessageDelayed(int what, long delayMillis) {
    Message msg = Message.obtain();
    msg.what = what;
    return sendMessageDelayed(msg, delayMillis);
}
public final boolean sendEmptyMessageAtTime(int what, long uptimeMillis) {
    Message msg = Message.obtain();
    msg.what = what;
    return sendMessageAtTime(msg, uptimeMillis);
}
public final boolean sendMessageDelayed(Message msg, long delayMillis){
    if (delayMillis < 0) { delayMillis = 0; }
    return sendMessageAtTime(msg, SystemClock.uptimeMillis() + delayMillis);
}
//最终都是调用这个方法
public boolean sendMessageAtTime(Message msg, long uptimeMillis) {
	...
}
```

### sendMessageAtTime
```java
/* Handler源码 */
public boolean sendMessageAtTime(Message msg, long uptimeMillis) {
    MessageQueue queue = mQueue;
    if (queue == null) {
        RuntimeException e = new RuntimeException(
                this + " sendMessageAtTime() called with no mQueue");
        Log.w("Looper", e.getMessage(), e);
        return false;
    }
    //关键在这里
    return enqueueMessage(queue, msg, uptimeMillis);
}
private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
    msg.target = this;//证实了msg.target就是发送条消息的Handler
    if (mAsynchronous) {
        msg.setAsynchronous(true);
    }
    return queue.enqueueMessage(msg, uptimeMillis);//往消息队列里面插入一条消息
}
```

如上源码可知，**send**函数**其实就是往消息队列**里面**插入**一条**消息**。

同时**Looper的loop函数又会调用MessageQueue的next函数去获取消息**，**最后在交给Handler的dispatchMessage函数处理**。
这样子我们就把Handler, MessageQueue, Looper三者串起来了。

那么接下来我们赶紧来看看dispatchMessage是什么样子的。
### dispatchMessage
```java
/* Handler源码 */
public void dispatchMessage(Message msg) {    
    if (msg.callback != null) {//msg.callback
        handleCallback(msg);//①
    } else {        
        if (mCallback != null) {//mCallback
            if (mCallback.handleMessage(msg)) {//②
                return;
            }
        }
        handleMessage(msg);//③
    }
}
```

这里有两个callBack，我们一一来分析一下是什么意思。
#### ① msg.callback
msg.callback是你在调用Handler.post....()函数时候创建出来的, **msg.callback就是这里的runnbale匿名类**:
```java
handler.postDelayed(new Runnable() {//postDelayed具体实现见下面源码
    @Override
    public void run() {      
        //.....
    }
}, 1000);
```
```java
/* Handler源码 */
public final boolean postDelayed(Runnable r, long delayMillis){
    return sendMessageDelayed(getPostMessage(r), delayMillis);//getPostMessage
}
private static Message getPostMessage(Runnable r) {
    Message m = Message.obtain();
    m.callback = r;//msg.callback就是postDelayed传进来的runnbale匿名类
    return m;
}
```

#### ① handleCallback(msg)
由下源码可知，**handleCallback(msg)就是去执行runnbale中的run函数**。没错,这里**将开启一个线程**。
所以我们就明白了最开始我们提到的使用Handler处理耗时操作和UI操作的原理了。
```java
/* Handler源码 */
private static void handleCallback(Message message) {
    message.callback.run();
}
```

那么，如果你是调用handler.sendMessage()函数的话，callBack为null。

#### ② mCallback
如下，由Handler的构造函数可知，如果**mCallback**是在**创建Handler时传递进来callback**。
```java
/* Handler源码 */
public Handler(boolean async) { this(null, async); }
public Handler(Callback callback, boolean async) {
    ...
    mLooper = Looper.myLooper();
    if (mLooper == null) {
        throw new RuntimeException(
            "Can't create handler inside thread that has not called Looper.prepare()");
    }
    mQueue = mLooper.mQueue;
    mCallback = callback;//mCallback = 创建Handler时传递进来callback
    mAsynchronous = async;
}
```

#### ② mCallback.handleMessage(msg)
追溯源码，发现Callback只是一个接口:
```java
/* Handler源码 */
public interface Callback {    
    public boolean handleMessage(Message msg);
}
```

于是我们应该就会想起我们在创建Handler的时候有时候会写这样子的代码:
```
private Handler mhandler=new Handler(new Handler.Callback() {
    @Override
    //mCallback.handleMessage(msg)其实调用的就是这个方法
    public boolean handleMessage(Message msg) {
        ...//这个时候Handler的dispatchMessage就会来执行这里的函数逻辑
        return true;
    }
});
```

#### ③ handleMessage(msg)
当dispatchMessage做完一系列判断，走到了handleMessage(msg)这一步，调用的是Handler自己handleMessage方法。
```java
/* Handler源码 */
public void handleMessage(Message msg) {
	//空实现，让子类实现
}
```

让我们想起了**创建Handler常用写法**：
```java
Handler handler = new Handler(){    
    @Override
    public void handleMessage(Message msg) {
        ...//这个时候Handler的dispatchMessage就会来执行这里的函数逻辑
    }
};
```
不管是**send**还是**post**，最终都是**在Handler创建时所在的线程执行**。



## Message

消息对象，就是MessageQueue里面存放的对象，一个MessageQueu可以包括多个Message。
当我们需要发送一个Message时，我们一般不建议使用new Message()的形式来创建，更**推荐使用Message.obtain()**来获取Message实例，因为在Message类里面定义了一个消息池，当消息池里存在未使用的消息时，便返回，如果没有未使用的消息，则通过new的方式创建返回，所以使用Message.obtain()的方式来获取实例可以大大**减少当有大量Message对象而产生的垃圾回收问题**。

四者关系总体如下（如有不对的地方，谢谢指出）
[![](http://upload-images.jianshu.io/upload_images/9028834-589d32945164d219.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://upload-images.jianshu.io/upload_images/9028834-589d32945164d219.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 总结
- 首先**Looper.prepare()创建Looper**并**初始化**Looper持有的消息队列**MessageQueue**，创建好后**将Looper保存到ThreadLocal**中方便Handler直接获取。

- 然后**Looper.loop()开启循环**，**从MessageQueue**里面**取消息**并**调用handler的 dispatchMessage**(msg) 方法**处理消息**。如果MessageQueue里**没有消息**，循环就会阻塞进入**休眠**状态，等有消息的时候被唤醒处理消息。

- 再然后我们new Handler()的时候，**Handler构造方法中获取Looper**并且**拿到Looper的MessageQueue对象**。然后Handler内部就可以直接**往MessageQueue**里面**插入消息**了，插入消息即发送消息，这时候有消息了就会**唤醒Looper循环**去处理消息。**处理消息**就是调用**dispatchMessage**(msg) 方法，最终调用到我们重写的Handler的handleMessage()方法。

最后用一张图，来表示整个消息机制

![handler_java](http://upload-images.jianshu.io/upload_images/9028834-b4040b28d6b7cae1..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



**图解：**
- Handler通过sendMessage()发送Message到MessageQueue队列；
- Looper通过loop()，不断提取出达到触发条件的Message，并将Message交给target来处理；
- 经过dispatchMessage()后，交回给Handler的handleMessage()来进行相应地处理。
- 将Message加入MessageQueue时，处往管道写入字符，可以会唤醒loop线程；如果MessageQueue中没有Message，并处于Idle状态，则会执行IdelHandler接口中的方法，往往用于做一些清理性地工作。

**消息分发的优先级：**
1. Message的回调方法：`message.callback.run()`，优先级最高；
2. Handler的回调方法：`Handler.mCallback.handleMessage(msg)`，优先级仅次于1；
3. Handler的默认方法：`Handler.handleMessage(msg)`，优先级最低。

### 架构图
![handler_java](http://upload-images.jianshu.io/upload_images/9028834-82ef8abde6366e0f..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **Looper**有一个MessageQueue消息队列；
- **MessageQueue**有一组待处理的Message；
- **Message**中有一个用于处理消息的Handler；
- **Handler**中有Looper和MessageQueue。

### UML图
消息主要设计到下面几个类：

- **`Handler`**：这是**消息**的**发出**的地方，也是消息**处理**的地方。
 - Handler拥有Looper的引用，通过得到Looper对象获得Looper中保存的MessageQueue对象
 - Handler拥有MessageQueue的引用，使Handler得以拥有发送消息（将Message放入MessageQueue）的能力
 - Handler拥有Handler.Callback的引用，使得Handler可以方便的进行消息的处理。
- **`Looper`**：这是**检测消息**的地方。
- **`MessageQueue`**:这是**存放消息**的地方，Handler把消息发到了这里，Looper从这里取出消息交给Handler进行处理。
- **`Message`**：消息。
- **`Thread`**：我在这里专门指代的是，**处理消息的线程**。消息的发送是在别的线程。

![](https://upload-images.jianshu.io/upload_images/9028834-8e74119f623d8ab3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)







# 源码分析
## 主线程默认创建Looper对象
**应用程序的入口**是在**ActivityThread**的**main**方法中的（当应用程序启动的时候，会通过底层的C/C++去调用main方法），这个方法在ActivityThread类的最后一个函数里面，核心代码如下：
```java
/* ActivityThread源码 */
public static void main(String[] args) {
    ...
    Environment.initForCurrentUser();
    ...
    Looper.prepareMainLooper();//Looper
    ...
    Looper.loop();//Looper
    ...
}
```
```
/* Looper源码 */
public static void prepareMainLooper() {    
    prepare(false);//false意思不允许我们程序员退出（面向我们开发者），因为这是在主线程里面
    synchronized (Looper.class) {
        if (sMainLooper != null) {//每个线程只允许执行一次
            throw new IllegalStateException("The main Looper has already been prepared.");
        }        
        sMainLooper = myLooper();//将当前的Looper保存为主Looper
    }
}
```


在main方法里面，首先初始化了我们的Environment对象，然后创建了Looper，然后开启消息循环。
根据我们的常识知道，如果程序没有死循环的话，执行完main函数（比如构建视图等等代码）以后就会立马退出了。**之所以**我们**的APP能够一直运行**着，就是**因为Looper.loop()里面是一个死循环**：
```java
public static void loop() {
    for (;;) {
    }
}
```
>之所以用for (;;)而不是用while(true)是因为防止一些人通过黑科技去修改这个循环的标志（比如通过反射的方式）。
在分析源码的时候，你可能会发现一些if(false){}之类的语句，这种写法是方便调试的，通过一个标志就可以控制某些代码是否执行，比如说是否输出一些系统的Log。
看源码一定不要慌，也不要一行一行看，要抓住核心的思路去看即可。


## 系统的Handler
在ActivityThread的成员变量里面有一个这样的大**H**（继承Handler），这个就是**系统的Handler**：
```java
final H mH = new H();
```

回顾一下ActivityThread的main方法可以知道，在new ActivityThread的时候，系统的Handler就就初始化了，这是一种饿加载的方法，也就是在类被new的时候就初始化成员变量了。
```java
/* ActivityThread源码 */
public static void main(String[] args) {
    ...
    Environment.initForCurrentUser();
    ...
    Looper.prepareMainLooper();
    
    ActivityThread thread = new ActivityThread();
    thread.attach(false);
    if (sMainThreadHandler == null) {
        sMainThreadHandler = thread.getHandler();//初始化系统的Handler
    }
    ...
    Looper.loop();
    ...
}
final Handler getHandler() {
    return mH;
}
final H mH = new H();
```
### H
下面看系统Handler的定义（看的时候可以跳过一些case，粗略地看即可）：
从系统的Handler中，在**handleMessage**我们可以看到很多关于**四大组件的生命周期操作**，比如创建、销毁、切换、跨进程通信，也包括了整个**Application进程**的销毁等等。
```java
/* ActivityThread源码 */
private class H extends Handler {
	...
    public void handleMessage(Message msg) {
        switch (msg.what) {
            case LAUNCH_ACTIVITY: { ... } break;
            case RELAUNCH_ACTIVITY: { ... } break;
            case PAUSE_ACTIVITY: { ... } break;
            case PAUSE_ACTIVITY_FINISHING: { ... } break;
            case STOP_ACTIVITY_SHOW: { ... } break;
            case STOP_ACTIVITY_HIDE: { ... } break;
            case SHOW_WINDOW: { ... } break;
            case HIDE_WINDOW: { ... } break;
            case RESUME_ACTIVITY: { ... } break;
            case SEND_RESULT: { ... } break;
            case DESTROY_ACTIVITY: { ... } break;
            case BIND_APPLICATION: { ... } break;
            case EXIT_APPLICATION://应用程序的退出过程
                if (mInitialApplication != null) {
                    mInitialApplication.onTerminate();
                }
                Looper.myLooper().quit();
                break;
            case NEW_INTENT: { ... } break;
            case RECEIVER: { ... } break;
            case CREATE_SERVICE: { ... } break;
            case BIND_SERVICE: { ... } break;
            case UNBIND_SERVICE: { ... } break;
            case SERVICE_ARGS: { ... } break;
            case STOP_SERVICE: { ... } break;
            case CONFIGURATION_CHANGED: { ... } break;
            case CLEAN_UP_CONTEXT: { ... } break;
            case GC_WHEN_IDLE: { ... } break;
            case DUMP_SERVICE: { ... } break;
            case LOW_MEMORY: { ... } break;
            case ACTIVITY_CONFIGURATION_CHANGED: { ... } break;
            case PROFILER_CONTROL: { ... } break;
            case CREATE_BACKUP_AGENT: { ... } break;
            case DESTROY_BACKUP_AGENT: { ... } break;
            case SUICIDE://我们可以通过发SUICIDE消息自杀，这样来退出应用程序
                Process.killProcess(Process.myPid());
                break;
            case REMOVE_PROVIDER: { ... } break;
            case ENABLE_JIT: { ... } break;
            case DISPATCH_PACKAGE_BROADCAST: { ... } break;
            case SCHEDULE_CRASH: { ... } break;
            case DUMP_HEAP: { ... } break;
            case DUMP_ACTIVITY: { ... } break;
            case DUMP_PROVIDER: { ... } break;
            case SLEEPING: { ... } break;
            case SET_CORE_SETTINGS: { ... } break;
            case UPDATE_PACKAGE_COMPATIBILITY_INFO: { ... } break;
            case TRIM_MEMORY: { ... } break;
            case UNSTABLE_PROVIDER_DIED: { ... } break;
            case REQUEST_ASSIST_CONTEXT_EXTRAS: { ... } break;
            case TRANSLUCENT_CONVERSION_COMPLETE: { ... } break;
            case INSTALL_PROVIDER: { ... } break;
            case ON_NEW_ACTIVITY_OPTIONS: { ... } break;
            case CANCEL_VISIBLE_BEHIND: { ... } break;
            case BACKGROUND_VISIBLE_BEHIND_CHANGED: { ... } break;
            case ENTER_ANIMATION_COMPLETE: { ... } break;
            case START_BINDER_TRACKING: { ... } break;
            case STOP_BINDER_TRACKING_AND_DUMP: { ... } break;
            case MULTI_WINDOW_MODE_CHANGED: { ... } break;
            case PICTURE_IN_PICTURE_MODE_CHANGED: { ... } break;
            case LOCAL_VOICE_INTERACTION_STARTED: { ... } break;
        }
        Object obj = msg.obj;
        if (obj instanceof SomeArgs) {
            ((SomeArgs) obj).recycle();
        }
        if (DEBUG_MESSAGES) Slog.v(TAG, "<<< done: " + codeToString(msg.what));
    }
	...
}
```
### 应用程序的退出过程
实际上我们要**退出应用程序**的话，就是让主线程结束，换句话说就是要**让Looper的循环结束**。
这里是直接结束Looper循环，因此我们四大组件的生命周期方法可能就不会执行了，因为四大组件的生命周期方法就是通过Handler去处理的，Looper循环都没有了，四大组件还玩毛线！因此我们平常写程序的时候就要注意了，onDestroy方法是不一定能够回调的。
```java
/* ActivityThread源码 */
//H的handleMessage中部分代码
case EXIT_APPLICATION:
    if (mInitialApplication != null) {
        mInitialApplication.onTerminate();
    }
    //退出Looper的循环
    Looper.myLooper().quit();
    break;
```

这里实际上是调用了MessageQueue的quit，清空所有Message。
```java
/* Looper源码 */
public void quit() {
    mQueue.quit(false);
}
```



## 1、Looper
### ThreadLocal
**ThreadLocal**： **线程本地存储区**（Thread Local Storage，简称为TLS），每个线程都有自己的**私有**的本地存储区域，不同线程之间彼此不能访问对方的TLS区域。
它是一个用来存储数据的类，类似HashMap、ArrayList等集合类。它的特点是可以在指定的线程中存储数据，然后取数据**只能取到当前线程的数据**。

ThreadLocal是JDK提供的一个**解决线程不安全**的类，线程不安全问题归根结底主要涉及到变量的多线程访问问题，例如变量的临界问题、值错误、并发问题等。

ThreadLocal 内部维护了一个针对每一个线程的数组Entry[]，它的初始容量是16，我们在设置value的时候将当前的value值封装Entry类里面，在然后再根据当前的ThreadLocal的索引去查中对应的Entry值，最终根据Entry对象取出value值，很明显每个线程的数组是不相同，所以就可以取出不同的Entry。

ThreadLocal保证每个线程都保持对其线程局部变量副本的隐式引用，只要线程是活动的并且 ThreadLocal 实例是可访问的；在线程消失之后，其线程局部实例的所有副本都会被垃圾回收（除非存在对这些副本的其他引用）。更多ThreadLocal的讲解参考：[Android线程管理之ThreadLocal理解及应用场景](https://link.jianshu.com/?t=https%3A%2F%2Fwww.cnblogs.com%2Fwhoislcj%2Fp%2F5811989.html)

TLS常用的操作方法：
#### set()
**`ThreadLocal.set(T value)`**：**将value存储到当前线程的TLS区域**，源码如下：
map.set(this, value)：通过把**键-ThreadLocal自身**&**值-Looper** 放到了一个Map里面，如果再放一个的话，就会覆盖（因为map不允许键值对中的键是重复的），因此ThreadLocal**绑定了线程以及Looper**。
```java
/* ThreadLocal源码 */
public void set(T value) {
	    Thread t = Thread.currentThread();//获取当前线程
	    ThreadLocalMap map = getMap(t);
	    if (map != null)
	        map.set(this, value);//把键-ThreadLocal自身&值-Looper 放到了一个Map里面
	    else
	        createMap(t, value);
}
ThreadLocalMap getMap(Thread t) {
		return t.threadLocals;//由Thread源码知，t.threadLocals类型为ThreadLocal.ThreadLocalMap
}
void createMap(Thread t, T firstValue) {
	    t.threadLocals = new ThreadLocalMap(this, firstValue);
}

static class ThreadLocalMap {
        ...
        private static final int INITIAL_CAPACITY = 16;
        ...
        /* ThreadLocalMap的构造方法 */
        ThreadLocalMap(ThreadLocal firstKey, Object firstValue) {
            table = new Entry[INITIAL_CAPACITY];//构造一个数组
            int i = firstKey.threadLocalHashCode & (INITIAL_CAPACITY - 1);//通过按位运算得到一个变量i值
            //构造一个Entry对象将ThreadLocal和Value放进去，将设置进去的值包装成一个对象存进数组里边
            table[i] = new Entry(firstKey, firstValue);
            size = 1;
            setThreshold(INITIAL_CAPACITY);
        }
        //Entry这个实体继承WeakReference(就是我们常说的弱引用)
        static class Entry extends WeakReference<ThreadLocal> {
            /** The value associated with this ThreadLocal. */
            Object value;
            Entry(ThreadLocal k, Object v) {//v就是我们设置进去的value
                super(k);
                value = v;
            }
        }
}
```

```java
/* Thread源码 */
ThreadLocal.ThreadLocalMap threadLocals = null;
```


#### get()
**`ThreadLocal.get()`**：**获取当前线程TLS区域的数据**，源码如下：

```java
public T get() {
        Thread t = Thread.currentThread();//获取当前线程
        ThreadLocalMap map = getMap(t);
        if (map != null) {
            ThreadLocalMap.Entry e = map.getEntry(this);
            if (e != null)
                return (T)e.value;//返回当前线程储存区中的数据
        }
        return setInitialValue();//目标线程存储区没有查询到则返回null
}
private T setInitialValue() {
        T value = initialValue();
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null)
            map.set(this, value);
        else
            createMap(t, value);
        return value;
}
protected T initialValue() {
        return null;
}
```

ThreadLocal的get()和set()方法操作的类型都是泛型。

### 1.1 prepare()

对于无参的情况，默认调用`prepare(true)`，表示的是这个Looper运行退出，而对于false的情况则表示当前Looper不运行退出。
```java
/* Looper源码 */
private static void prepare(boolean quitAllowed) {
    //每个线程只允许执行一次该方法，第二次执行时线程的TLS已有数据，则会抛出异常。
    if (sThreadLocal.get() != null) {
        throw new RuntimeException("Only one Looper may be created per thread");
    }
    //创建Looper对象，并保存到当前线程的TLS区域
    sThreadLocal.set(new Looper(quitAllowed));
}
//Looper的成员变量sThreadLocal
static final ThreadLocal<Looper> sThreadLocal = new ThreadLocal<Looper>();
```
这里的`sThreadLocal`变量是ThreadLocal类型，其定义如下：
```java
static final ThreadLocal<Looper> sThreadLocal = new ThreadLocal<Looper>();
```

可见`sThreadLocal`的get()和set()操作的类型都是`Looper`类型。
这里**利用ThreadLocal绑定了Looper以及线程**，就可以避免其他线程去访问当前线程的Looper了。


**Looper.prepare()**
Looper.prepare()在**每个线程只允许执行一次**，该方法会创建Looper对象，Looper的构造方法中会创建一个MessageQueue对象，再将Looper对象保存到当前线程TLS。

对于**Looper**类型的**构造方法**如下：
```java
private Looper(boolean quitAllowed) {
    mQueue = new MessageQueue(quitAllowed);  //创建MessageQueue对象. //【见3.1 创建MessageQueue】
    mThread = Thread.currentThread();  //记录当前线程.
}
```

另外，与prepare()相近功能的，还有一个**`prepareMainLooper()`**方法，该方法主要在ActivityThread类中使用。
```java
public static void prepareMainLooper() {
    prepare(false); //设置不允许退出的Looper
    synchronized (Looper.class) {
        if (sMainLooper != null) {//每个线程只允许执行一次
            throw new IllegalStateException("The main Looper has already been prepared.");
        }
        sMainLooper = myLooper();//将当前的Looper保存为主Looper
    }
}
public static @Nullable Looper myLooper() {
    return sThreadLocal.get();
}
static final ThreadLocal<Looper> sThreadLocal = new ThreadLocal<Looper>();
```




### 1.2 loop()
loop()进入循环模式，不断重复下面的操作，直到没有消息时退出循环
- 读取MessageQueue的下一条Message；
- 把Message分发给相应的target；
- 再把分发后的Message回收到消息池，以便重复利用。

这是这个消息处理的核心部分。

另外，源码中可以看到有logging方法，这是用于debug的，默认情况下`logging == null`，通过设置setMessageLogging()用来开启debug工作。
```java
/* Looper源码 */
public static void loop() {
    final Looper me = myLooper();  //获取TLS存储的Looper对象 //【见1.4.1 myLooper】
    if (me == null) {
        throw new RuntimeException("No Looper; Looper.prepare() wasn't called on this thread.");
    }
    final MessageQueue queue = me.mQueue;  //获取Looper对象中的消息队列

    Binder.clearCallingIdentity();
    //确保在权限检查时基于本地进程，而不是基于最初调用进程。
    final long ident = Binder.clearCallingIdentity();

    for (;;) { //进入loop的主循环方法
        Message msg = queue.next(); //可能会阻塞 //【见3.2 next()】
        if (msg == null) { //没有消息，则退出循环
            return;
        }

        Printer logging = me.mLogging;  //默认为null，可通过setMessageLogging()方法来指定输出，用于debug功能
        if (logging != null) {
            logging.println(">>>>> Dispatching to " + msg.target + " " +
                    msg.callback + ": " + msg.what);
        }
        final long traceTag = me.mTraceTag;
        if (traceTag != 0) {
            Trace.traceBegin(traceTag, msg.target.getTraceName(msg));
        }
        try {
            msg.target.dispatchMessage(msg);//用于分发Message //【见2.2 消息分发】
        } finally {
            if (traceTag != 0) {
                Trace.traceEnd(traceTag);
            }
        }
        if (logging != null) {
            logging.println("<<<<< Finished to " + msg.target + " " + msg.callback);
        }

        final long newIdent = Binder.clearCallingIdentity(); //确保分发过程中identity不会损坏
        if (ident != newIdent) {
             //打印identity改变的log，在分发消息过程中是不希望身份被改变的。
        }
        msg.recycleUnchecked();  //回收消息，将Message放入消息池 //【见4.2.2 recycle】
    }
}
```

### 1.3 quit()
```java
/* Looper源码 */
public void quit() {
    mQueue.quit(false); //消息移除
}

public void quitSafely() {
    mQueue.quit(true); //安全地消息移除
}
```

Looper.quit()方法的实现最终调用的是MessageQueue.quit()方法

**MessageQueue.quit()**
消息退出的方式：
- 当safe =true时，只移除尚未触发的所有消息，对于正在触发的消息并不移除；
- 当safe =flase时，移除所有的消息
```java
/* MessageQueue源码 */
void quit(boolean safe) {        
        if (!mQuitAllowed) {// 当mQuitAllowed为false，表示不运行退出，强行调用quit()会抛出异常
            throw new IllegalStateException("Main thread not allowed to quit.");
        }
        synchronized (this) {
            if (mQuitting) { //防止多次执行退出操作
                return;
            }
            mQuitting = true;
            if (safe) {
                removeAllFutureMessagesLocked(); //移除尚未触发的所有消息
            } else {
                removeAllMessagesLocked(); //移除所有的消息
            }
            //如mQuitting=false，那么认定为 mPtr != 0
            nativeWake(mPtr);
        }
    }
```
### 1.4 常用方法

#### 1.4.1 myLooper[](http://gityuan.com/2015/12/26/handler-message-framework/#241-mylooper)

用于获取TLS存储的Looper对象
```java
public static @Nullable Looper myLooper() {
        return sThreadLocal.get();
    }
```

### 
## 2、Handler

### 2.1 创建Handler

#### 2.1.1 无参构造
```java
public Handler() {
    this(null, false);
}

public Handler(Callback callback, boolean async) {
    //匿名类、内部类或本地类都必须申明为static，否则会警告可能出现内存泄露
    if (FIND_POTENTIAL_LEAKS) {
        final Class<? extends Handler> klass = getClass();
        if ((klass.isAnonymousClass() || klass.isMemberClass() || klass.isLocalClass()) &&
                (klass.getModifiers() & Modifier.STATIC) == 0) {
            Log.w(TAG, "The following Handler class should be static or leaks might occur: " +
                klass.getCanonicalName());
        }
    }
    //必须先执行Looper.prepare()，才能获取Looper对象，否则为null.
    mLooper = Looper.myLooper();  //从当前线程的TLS中获取Looper对象//【见1.1 prepare()&1.4.1 myLooper】
    if (mLooper == null) {
        throw new RuntimeException(
                "Can't create handler inside thread that has not called Looper.prepare()");
    }
    mQueue = mLooper.mQueue; //消息队列，来自Looper对象
    mCallback = callback;  //回调方法
    mAsynchronous = async; //设置消息是否为异步处理方式
}
```

对于Handler的无参构造方法，默认采用当前线程TLS中的Looper对象，并且callback回调方法为null，且消息为同步处理方式。只要执行的Looper.prepare()方法，那么便可以获取有效的Looper对象。

#### 2.1.2 有参构造
```java
public Handler(Looper looper) {
    this(looper, null, false);
}

public Handler(Looper looper, Callback callback, boolean async) {
    mLooper = looper;
    mQueue = looper.mQueue;
    mCallback = callback;
    mAsynchronous = async;
}
```

Handler类在构造方法中，可指定Looper，Callback回调方法以及消息的处理方式(同步或异步)，对于无参的handler，默认是当前线程的Looper。

### 2.2 消息分发
在Looper.loop()中，当发现有消息时，调用消息的目标handler，执行dispatchMessage()方法来分发消息。

**分发消息流程：**

1. 当`Message`的回调方法不为空时，则回调方法`msg.callback.run()`，其中callBack数据类型为Runnable,否则进入步骤2；
2. 当`Handler`的`mCallback`成员变量不为空时，则回调方法`mCallback.handleMessage(msg)`,否则进入步骤3；
3. 调用`Handler`自身的回调方法`handleMessage()`，该方法默认为空，Handler子类通过覆写该方法来完成具体的逻辑。

对于很多情况下，消息分发后的处理方法是第3种情况，即Handler.handleMessage()，一般地往往通过覆写该方法从而实现自己的业务逻辑。

#### 2.2.1 dispatchMessage
```java
/* Handler源码 */
public void dispatchMessage(Message msg) {
    if (msg.callback != null) {//这是调用handler.post(Runnable)方法发送的消息【见2.3.6 post】
        //当Message存在回调方法，回调msg.callback.run()方法；
        handleCallback(msg);
    } else {
        if (mCallback != null) {
            //当Handler存在Callback成员变量时，回调方法handleMessage()；
            if (mCallback.handleMessage(msg)) {
                return;
            }
        }
        //Handler自身的回调方法handleMessage()
        handleMessage(msg);
    }
}


private static void handleCallback(Message message) {
    message.callback.run();
}
/**
 * Callback interface you can use when instantiating a Handler to avoid
 * having to implement your own subclass of Handler.
 *
 * @param msg A {@link android.os.Message Message} object
 * @return True if no further handling is desired
 */
public interface Callback {
    public boolean handleMessage(Message msg);
}

/**
 * Subclasses must implement this to receive messages.
 */
public void handleMessage(Message msg) {
}
```

### 2.3 消息发送
发送消息调用链：
![java_sendmessage](http://upload-images.jianshu.io/upload_images/9028834-4ad8aa65e85502e6..png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从上图，可以发现所有的发消息方式，最终都是调用`MessageQueue.enqueueMessage()`;
`Handler.sendEmptyMessage()`等系列方法最终调用`MessageQueue.enqueueMessage(msg, uptimeMillis)`，将消息添加到消息队列中，其中uptimeMillis为系统当前的运行时间，不包括休眠时间。

#### 2.3.1 sendEmptyMessage
```java
public final boolean sendEmptyMessage(int what) {
    return sendEmptyMessageDelayed(what, 0);
}
```

#### 2.3.2 sendEmptyMessageDelayed
```java
public final boolean sendEmptyMessageDelayed(int what, long delayMillis) {
    Message msg = Message.obtain();
    msg.what = what;
    return sendMessageDelayed(msg, delayMillis);
}
```

#### 2.3.3 sendMessageDelayed
```java
public final boolean sendMessageDelayed(Message msg, long delayMillis) {
    if (delayMillis < 0) {
        delayMillis = 0;
    }
    return sendMessageAtTime(msg, SystemClock.uptimeMillis() + delayMillis);
}
```

#### 2.3.4 sendMessageAtTime
```java
public boolean sendMessageAtTime(Message msg, long uptimeMillis) {
    MessageQueue queue = mQueue;
    if (queue == null) {
        RuntimeException e = new RuntimeException(
                this + " sendMessageAtTime() called with no mQueue");
        Log.w("Looper", e.getMessage(), e);
        return false;
    }
    return enqueueMessage(queue, msg, uptimeMillis);
}
```

#### 2.3.5 sendMessageAtFrontOfQueue
该方法通过设置消息的触发时间为0，从而使Message加入到消息队列的队头。
```java
public final boolean sendMessageAtFrontOfQueue(Message msg) {
    MessageQueue queue = mQueue;
    if (queue == null) {
        RuntimeException e = new RuntimeException(
            this + " sendMessageAtTime() called with no mQueue");
        Log.w("Looper", e.getMessage(), e);
        return false;
    }
    return enqueueMessage(queue, msg, 0);
}
```

#### 2.3.6 post
```java
public final boolean post(Runnable r) {
   return  sendMessageDelayed(getPostMessage(r), 0);}
public final boolean postDelayed(Runnable r, long delayMillis){
    return sendMessageDelayed(getPostMessage(r), delayMillis);}
public final boolean postAtTime(Runnable r, long uptimeMillis){
    return sendMessageAtTime(getPostMessage(r), uptimeMillis);}
public final boolean postAtTime(Runnable r, Object token, long uptimeMillis){
    return sendMessageAtTime(getPostMessage(r, token), uptimeMillis);}
    
private static Message getPostMessage(Runnable r) {
    Message m = Message.obtain();//【见4.2.1 obtain】
    m.callback = r;
    return m;
}
```

#### 2.3.7 postAtFrontOfQueue
```java
public final boolean postAtFrontOfQueue(Runnable r) {
    return sendMessageAtFrontOfQueue(getPostMessage(r));
}
```

#### 2.3.8 enqueueMessage
```java
private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
    msg.target = this;//target就是Message绑定的Handler(当前Handler)
    if (mAsynchronous) {
        msg.setAsynchronous(true);
    }
    return queue.enqueueMessage(msg, uptimeMillis); //【见3.3 enqueueMessage】
}
```


### 2.4 其他方法
`Handler`是消息机制中非常重要的辅助类，更多的实现都是`MessageQueue`, `Message`中的方法，Handler的目的是为了更加方便的使用消息机制。

#### 2.4.1 obtainMessage

获取消息
```java
public final Message obtainMessage() {
    return Message.obtain(this); //【见4.2.1 obtain】
}
```

`Handler.obtainMessage()`方法，最终调用`Message.obtainMessage(this)`，其中this为当前的Handler对象。

#### 2.4.2 removeMessages
```java
public final void removeMessages(int what) {
    mQueue.removeMessages(this, what, null); //【见3.4 removeMessages】
}
```

### 
## 3、MessageQueue

MessageQueue是消息机制的**Java层和C++层的连接纽带**，大部分**核心方法都交给native层来处理**，其中MessageQueue类中涉及的native方法如下：
```java
private native static long nativeInit();
private native static void nativeDestroy(long ptr);
private native void nativePollOnce(long ptr, int timeoutMillis);
private native static void nativeWake(long ptr);
private native static boolean nativeIsPolling(long ptr);
private native static void nativeSetFileDescriptorEvents(long ptr, int fd, int events);
```

关于这些native方法的介绍，见[Android消息机制2-Handler(native篇)](http://gityuan.com/2015/12/27/handler-message-native/)。

### 3.1 创建MessageQueue
```java
MessageQueue(boolean quitAllowed) {
    mQuitAllowed = quitAllowed;
    //通过native方法初始化消息队列，其中mPtr是供native代码使用
    mPtr = nativeInit();
}
```

### 3.2 next()
提取下一条message。
消息的取出并不是直接就从队列的头部**取出**的，而是**根据**了**消息的when时间参数**有关的，因为我们可以发送延时消息、也可以发送一个指定时间点的消息。

`nativePollOnce`是阻塞操作，其中`nextPollTimeoutMillis`代表下一个消息到来前，还需要等待的时长；当nextPollTimeoutMillis = -1时，表示消息队列中无消息，会一直等待下去。

当处于空闲时，往往会执行`IdleHandler`中的方法。当nativePollOnce()返回后，next()从`mMessages`中提取一个消息。

`nativePollOnce()`在native做了大量的工作，想进一步了解可查看 [Android消息机制2-Handler(native篇)](http://gityuan.com/2015/12/27/handler-message-native/#nativepollonce)。

从源码可以看到消息的取出用到了一些native方法，这样做是为了获得更高的效率，
```java
/* MessageQueue源码 */
Message next() {
    final long ptr = mPtr;
    if (ptr == 0) { //当消息循环已经退出，则直接返回
        return null;
    }
    int pendingIdleHandlerCount = -1; // 循环迭代的首次为-1
    int nextPollTimeoutMillis = 0;
    for (; ; ) {
        if (nextPollTimeoutMillis != 0) {
            Binder.flushPendingCommands();
        }
        //阻塞操作，当等待nextPollTimeoutMillis时长，或者消息队列被唤醒，都会返回
        nativePollOnce(ptr, nextPollTimeoutMillis);//nativePollOnce
        synchronized (this) {
            final long now = SystemClock.uptimeMillis();//拿到当前的时间戳
            Message prevMsg = null;
            Message msg = mMessages;
            if (msg != null && msg.target == null) {
                //当消息Handler为空时，查询MessageQueue中的下一条异步消息msg，则退出循环。
                do {
                    prevMsg = msg;
                    msg = msg.next;
                } while (msg != null && !msg.isAsynchronous());
            }
            if (msg != null) {
                if (now < msg.when) {
                //当异步消息触发时间大于当前时间（还没有到执行的时间），则设置下一次轮询的超时时长
                    nextPollTimeoutMillis = (int) Math.min(msg.when - now, Integer.MAX_VALUE);
                } else {
                    // 到了执行时间，获取一条消息，并返回
                    mBlocked = false;
                    if (prevMsg != null) {
                        prevMsg.next = msg.next;
                    } else {
                        mMessages = msg.next;
                    }
                    msg.next = null;
                    //设置消息的使用状态，即flags |= FLAG_IN_USE
                    msg.markInUse();
                    return msg;   //成功地获取MessageQueue中的下一条即将要执行的消息
                }
            } else {
                //没有消息
                nextPollTimeoutMillis = -1;
            }
            //消息正在退出，返回null
            if (mQuitting) {
                dispose();
                return null;
            }
            //当消息队列为空，或者是消息队列的第一个消息时
            if (pendingIdleHandlerCount < 0 && (mMessages == null || now < mMessages.when)) {
                pendingIdleHandlerCount = mIdleHandlers.size();
            }
            if (pendingIdleHandlerCount <= 0) {
                //没有idle handlers 需要运行，则循环并等待。
                mBlocked = true;
                continue;
            }
            if (mPendingIdleHandlers == null) {
                mPendingIdleHandlers = new IdleHandler[Math.max(pendingIdleHandlerCount, 4)];
            }
            mPendingIdleHandlers = mIdleHandlers.toArray(mPendingIdleHandlers);
        }
        //只有第一次循环时，会运行idle handlers，执行完成后，重置pendingIdleHandlerCount为0.
        for (int i = 0; i < pendingIdleHandlerCount; i++) {
            final IdleHandler idler = mPendingIdleHandlers[i];
            mPendingIdleHandlers[i] = null; //去掉handler的引用
            boolean keep = false;
            try {
                keep = idler.queueIdle();  //idle时执行的方法
            } catch (Throwable t) {
                Log.wtf(TAG, "IdleHandler threw exception", t);
            }
            if (!keep) {
                synchronized (this) {
                    mIdleHandlers.remove(idler);
                }
            }
        }
        //重置idle handler个数为0，以保证不会再次重复运行
        pendingIdleHandlerCount = 0;
        //当调用一个空闲handler时，一个新message能够被分发，因此无需等待可以直接查询pending message.
        nextPollTimeoutMillis = 0;
    }
}
```



### 3.3 enqueueMessage
添加一条消息到消息队列
`MessageQueue`是按照Message触发时间的先后顺序排列的，队头的消息是将要最早触发的消息。当有消息需要加入消息队列时，会从队列头开始遍历，直到找到消息应该插入的合适位置，以保证所有消息的时间顺序。
```java
boolean enqueueMessage(Message msg, long when) {
    // 每一个普通Message必须有一个target（即Handler）
    if (msg.target == null) {
        throw new IllegalArgumentException("Message must have a target.");
    }
    if (msg.isInUse()) {
        throw new IllegalStateException(msg + " This message is already in use.");
    }
    synchronized (this) {
        if (mQuitting) {  //正在退出时，回收msg，加入到消息池
            msg.recycle();
            return false;
        }
        msg.markInUse();
        msg.when = when;
        Message p = mMessages;
        boolean needWake;
        if (p == null || when == 0 || when < p.when) {
            //p为null(代表MessageQueue没有消息） 或者msg的触发时间是队列中最早的， 则进入该该分支
            msg.next = p;
            mMessages = msg;
            needWake = mBlocked; //当阻塞时需要唤醒
        } else {
            //将消息按时间顺序插入到MessageQueue。一般地，不需要唤醒事件队列，
            //除非消息队头存在barrier，并且同时Message是队列中最早的异步消息。
            needWake = mBlocked && p.target == null && msg.isAsynchronous();
            //下面的代码就是遍历单链表
            Message prev;
            for (;;) {
                prev = p;
                p = p.next;
                if (p == null || when < p.when) {
                    break;//p为链表的尾部，或者时间刚好比传进来的msg晚一点
                }
                if (needWake && p.isAsynchronous()) {
                    needWake = false;
                }
            }
            msg.next = p;
            prev.next = msg;//把传进来的消息插入链表(prev和p中间)
        }
        //消息没有退出，我们认为此时mPtr != 0
        if (needWake) {
            nativeWake(mPtr);
        }
    }
    return true;
}
```



### 3.4 removeMessages
这个移除消息的方法，采用了两个while循环，第一个循环是从队头开始，移除符合条件的消息，第二个循环是从头部移除完连续的满足条件的消息之后，再从队列后面继续查询是否有满足条件的消息需要被移除。
```java
void removeMessages(Handler h, int what, Object object) {
    if (h == null) {
        return;
    }
    synchronized (this) {
        Message p = mMessages;
        //从消息队列的头部开始，移除所有符合条件的消息
        while (p != null && p.target == h && p.what == what
               && (object == null || p.obj == object)) {
            Message n = p.next;
            mMessages = n;
            p.recycleUnchecked();
            p = n;
        }
        //移除剩余的符合要求的消息
        while (p != null) {
            Message n = p.next;
            if (n != null) {
                if (n.target == h && n.what == what
                    && (object == null || n.obj == object)) {
                    Message nn = n.next;
                    n.recycleUnchecked();
                    p.next = nn;
                    continue;
                }
            }
            p = n;
        }
    }
}
```



### 3.5 postSyncBarrier
前面小节[3.3]已说明每一个普通Message必须有一个target，对于特殊的message是没有target，即同步barrier token。 这个消息的价值就是用于拦截同步消息，所以并不会唤醒Looper。
postSyncBarrier只对同步消息产生影响，对于异步消息没有任何差别。

```java
private int postSyncBarrier(long when) {
    // Enqueue a new sync barrier token.
    // We don't need to wake the queue because the purpose of a barrier is to stall it.
    synchronized (this) {
        final int token = mNextBarrierToken++;
        final Message msg = Message.obtain();
        msg.markInUse();
        msg.when = when;
        msg.arg1 = token;

        Message prev = null;
        Message p = mMessages;
        if (when != 0) {
            while (p != null && p.when <= when) {
                prev = p;
                p = p.next;
            }
        }
        if (prev != null) { // invariant: p == prev.next
            msg.next = p;
            prev.next = msg;
        } else {
            msg.next = p;
            mMessages = msg;
        }
        return token;
    }
}
```


```
public void removeSyncBarrier(int token) {
     synchronized (this) {
         Message prev = null;
         Message p = mMessages;
         //从消息队列找到 target为空,并且token相等的Message
         while (p != null && (p.target != null || p.arg1 != token)) {
             prev = p;
             p = p.next;
         }
         if (p == null) {
             throw new IllegalStateException("The specified message queue synchronization "
                     + " barrier token has not been posted or has already been removed.");
         }
         final boolean needWake;
         if (prev != null) {
             prev.next = p.next;
             needWake = false;
         } else {
             mMessages = p.next;
             needWake = mMessages == null || mMessages.target != null;
         }
         p.recycleUnchecked();

         if (needWake && !mQuitting) {
             nativeWake(mPtr);
         }
     }
 }
```



## 4、 Message
### 4.1 创建消息

每个消息用`Message`表示，`Message`主要包含以下内容：

| 数据类型     | 成员变量     | 解释     |
| -------- | -------- | ------ |
| int      | what     | 消息类别   |
| long     | when     | 消息触发时间 |
| int      | arg1     | 参数1    |
| int      | arg2     | 参数2    |
| Object   | obj      | 消息内容   |
| Handler  | target   | 消息响应方  |
| Runnable | callback | 回调方法   |

创建消息的过程，就是填充消息的上述内容的一项或多项。

### 4.2 消息池
在代码中，可能经常看到recycle()方法，咋一看，可能是在做虚拟机的gc()相关的工作，其实不然，这是用于把消息加入到消息池的作用。这样的好处是，当消息池不为空时，可以直接从消息池中获取Message对象，而不是直接创建，提高效率。

静态变量`sPool`的数据类型为Message，通过next成员变量，维护一个消息池；静态变量`MAX_POOL_SIZE`代表消息池的可用大小；消息池的默认大小为50。

消息池常用的操作方法是obtain()和recycle()。

#### 4.2.1 obtain

从消息池中获取消息
obtain()，从消息池取Message，就是把消息池表头的Message取走，再把表头指向next;
```java
public static Message obtain() {
    synchronized (sPoolSync) {
        if (sPool != null) {
            Message m = sPool;
            sPool = m.next;
            m.next = null; //从sPool中取出一个Message对象，并消息链表断开
            m.flags = 0; // 清除in-use flag
            sPoolSize--; //消息池的可用大小进行减1操作
            return m;
        }
    }
    return new Message(); // 当消息池为空时，直接创建Message对象
}
```



#### 4.2.2 recycle
把不再使用的消息加入消息池
recycle()，将Message加入到消息池的过程，就是把Message加到链表的表头。
关于消息的回收还有一点需要注意的就是，我们**平时**写Handler的时候**不需要我们手动回收**，因为谷歌的工程师已经有考虑到这方面的问题了。消息在Handler分发处理之后就会被自动回收的——见Looper的loop【见1.2 loop()】
```java
public void recycle() {
    if (isInUse()) { //判断消息是否正在使用
        if (gCheckRecycle) { //Android 5.0以后的版本默认为true,之前的版本默认为false.
            throw new IllegalStateException("This message cannot be recycled because it is still in use.");
        }
        return;
    }
    recycleUnchecked();
}
boolean isInUse() {
    return ((flags & FLAG_IN_USE) == FLAG_IN_USE);
}
//对于不再使用的消息，加入到消息池
void recycleUnchecked() {
    //将消息标示位置为IN_USE，并清空消息所有的参数。
    flags = FLAG_IN_USE;
    what = 0;
    arg1 = 0;
    arg2 = 0;
    obj = null;
    replyTo = null;
    sendingUid = -1;
    when = 0;
    target = null;
    callback = null;
    data = null;
    synchronized (sPoolSync) {
        if (sPoolSize < MAX_POOL_SIZE) { //当消息池没有满时，将Message对象加入消息池
            next = sPool;
            sPool = this;
            sPoolSize++; //消息池的可用大小进行加1操作
        }
    }
}
```


# 常见问题

### Looper 死循环为什么不会导致应用卡死？

> 线程默认没有Looper的，如果需要使用Handler就必须为线程创建Looper。我们经常提到的主线程，也叫UI线程，它就是ActivityThread，ActivityThread被创建时就会初始化Looper，这也是在主线程中默认可以使用Handler的原因。

首先我们看一段代码

```java
new Thread(new Runnable() {
    @Override
    public void run() {
        Log.e("qdx", "step 0 ");
        Looper.prepare();

        Toast.makeText(MainActivity.this, "run on Thread", Toast.LENGTH_SHORT).show();

        Log.e("qdx", "step 1 ");
        Looper.loop();

        Log.e("qdx", "step 2 ");

    }
}).start();
```

我们知道`Looper.loop();`里面维护了一个死循环方法，所以按照理论，上述代码执行的应该是 
step 0 –>step 1 
也就是说循环在`Looper.prepare();`与`Looper.loop();`之间。 
![](http://upload-images.jianshu.io/upload_images/9028834-6ed28d53a62383ae?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 在子线程中，如果手动为其创建了Looper，那么**在所有的事情完成以后应该调用quit方法来终止消息循环**，否则这个子线程就会一直处于等待（阻塞）状态，而如果退出Looper以后，这个线程就会立刻（执行所有方法并）终止，因此建议不需要的时候终止Looper。

执行结果也正如我们所说，这时候如果了解了`ActivityThread`，并且在main方法中我们会看到主线程也是通过Looper方式来维持一个消息循环。

```java
/* ActivityThread源码 */
public static void main(String[] args) {
        ``````
        Looper.prepareMainLooper();//创建Looper和MessageQueue对象，用于处理主线程的消息
        ActivityThread thread = new ActivityThread();
        thread.attach(false);//建立Binder通道 (创建新线程)
        if (sMainThreadHandler == null) {
            sMainThreadHandler = thread.getHandler();
        }
        Trace.traceEnd(Trace.TRACE_TAG_ACTIVITY_MANAGER);
        Looper.loop();

        //如果能执行下面方法，说明应用崩溃或者是退出了...
        throw new RuntimeException("Main thread loop unexpectedly exited");
    }
```

那么回到我们的问题上，这个死循环会不会导致应用卡死，即使不会的话，它会慢慢的消耗越来越多的资源吗？ 

>摘自：[Gityuan–Handler(Native层)](http://gityuan.com/2015/12/27/handler-message-native/ "optional title")

对于线程即是一段可执行的代码，当可执行代码执行完成后，线程生命周期便该终止了，线程退出。
- 而对于**主线程**，我们是绝不希望会被运行一段时间，自己就退出，那么如何**保证**能**一直存活**呢？
简单做法就是**可执行代码是能一直执行下去**的，**死循环**便能保证不会被退出（例如，binder线程也是采用死循环的方法，通过循环方式不同与Binder驱动进行读写操作，当然并非简单地死循环，无消息时会休眠。）
- 但这里可能又引发了另一个问题，既然是**死循环**又**如何去处理其他事务**呢？
通过**创建新线程**的方式。
真正会卡死主线程的操作是在回调方法onCreate/onStart/onResume等操作时间过长，会导致掉帧，甚至发生ANR，looper.loop本身不会导致应用卡死。

- 主线程的死循环一直运行是不是特别消耗**CPU资源**呢？ 
**其实不然**，这里就涉及到Linux pipe/epoll机制，简单说就是在主线程的MessageQueue没有消息时，便阻塞在loop的queue.next()中的nativePollOnce()方法里，此时主线程会释放CPU资源进入休眠状态，直到下个消息到达或者有事务发生，通过往pipe管道写端写入数据来唤醒主线程工作。这里采用的epoll机制，是一种IO多路复用机制，可以同时监控多个描述符，当某个描述符就绪(读或写就绪)，则立刻通知相应程序进行读或写操作，本质同步I/O，即读写是阻塞的。 所以说，**主线程大多数时候都是处于休眠状态**，并不会消耗大量CPU资源。 



### 主线程的消息循环机制是什么？

事实上，会在进入死循环之前便创建了新binder线程，在代码ActivityThread.main()中：

```java
public static void main(String[] args) {
        ....

        //创建Looper和MessageQueue对象，用于处理主线程的消息
        Looper.prepareMainLooper();

        //创建ActivityThread对象
        ActivityThread thread = new ActivityThread(); 

        //建立Binder通道 (创建新线程)
        thread.attach(false);

        Looper.loop(); //消息循环运行
        throw new RuntimeException("Main thread loop unexpectedly exited");
    }
```

- **Activity的生命周期都是依靠主线程的Looper.loop**，当收到不同Message时则采用相应措施：一旦退出消息循环，那么你的程序也就可以退出了。 
从消息队列中取消息可能会阻塞，取到消息会做出相应的处理。如果某个消息处理时间过长，就可能会影响UI线程的刷新速率，造成卡顿的现象。

- **`thread.attach(false)`**方法函数中便会创建一个Binder线程（具体是指`ApplicationThread`，Binder的服务端，用于接收系统服务AMS发送来的事件），该**Binder线程通过Handler将Message发送给主线程**。「[Activity 启动过程](http://blog.csdn.net/qian520ao/article/details/78156214 "optional title")」
比如收到msg=`H.LAUNCH_ACTIVITY`，则调用`ActivityThread.handleLaunchActivity()`方法，最终会通过反射机制，创建Activity实例，然后再执行Activity.onCreate()等方法；
再比如收到msg=`H.PAUSE_ACTIVITY`，则调用`ActivityThread.handlePauseActivity()`方法，最终会执行Activity.onPause()等方法。

主线程的消息又是哪来的呢？当然是App进程中的其他线程通过Handler发送给主线程

- system_server进程
system_server进程是系统进程，java framework框架的核心载体，里面运行了大量的系统服务，比如这里提供`ApplicationThreadProxy`（简称ATP），`ActivityManagerService`（简称AMS），这个两个服务都运行在system_server进程的不同线程中，由于ATP和AMS都是基于IBinder接口，都是binder线程，binder线程的创建与销毁都是由binder驱动来决定的。

- App进程
App进程则是我们常说的应用程序，主线程主要负责Activity/Service等组件的生命周期以及UI相关操作都运行在这个线程； 另外，每个App进程中至少会有两个binder线程 `ApplicationThread`(简称AT)和`ActivityManagerProxy`（简称AMP），除了图中画的线程，其中还有很多线程

- Binder
**Binder用于不同进程之间通信**，由一个进程的Binder客户端向另一个进程的服务端发送事务，比如图中线程2向线程4发送事务；
而handler用于同一个进程中不同线程的通信，比如图中线程4向主线程发送消息。

![](http://upload-images.jianshu.io/upload_images/9028834-d0b7627437af8789?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

结合图说说Activity生命周期，比如暂停Activity，流程如下：

1. 线程1的AMS中调用线程2的ATP；（由于同一个进程的线程间资源共享，可以相互直接调用，但需要注意多线程并发问题）
2. 线程2通过binder传输到App进程的线程4；
3. 线程4通过handler消息机制，将暂停Activity的消息发送给主线程；
4. 主线程在looper.loop()中循环遍历消息，当收到暂停Activity的消息时，便将消息分发给 
   ActivityThread.H.handleMessage()方法，再经过方法的调用， 
   最后便会调用到Activity.onPause()，当onPause()处理完后，继续循环loop下去。

> 补充:
> ActivityThread的main方法主要就是做消息循环，一旦退出消息循环，那么你的程序也就可以退出了。
>
> 从消息队列中取消息可能会阻塞，取到消息会做出相应的处理。如果某个消息处理时间过长，就可能会影响UI线程的刷新速率，造成卡顿的现象。


#### 总结
最后通过《Android开发艺术探索》的一段话总结 :
ActivityThread通过ApplicationThread和AMS进行进程间通讯，AMS以进程间通信的方式完成ActivityThread的请求后会回调ApplicationThread中的Binder方法，然后ApplicationThread会向H发送消息，H收到消息后会将ApplicationThread中的逻辑切换到ActivityThread中去执行，即切换到主线程中去执行，这个过程就是。主线程的消息循环模型。

另外，ActivityThread实际上并非线程，不像HandlerThread类，ActivityThread并没有真正继承Thread类。

### ActivityThread 的动力是什么？

- 进程 
每个app运行时前首先创建一个进程，该进程是由Zygote fork出来的，用于承载App上运行的各种Activity/Service等组件。进程对于上层应用来说是完全透明的，这也是google有意为之，让App程序都是运行在Android Runtime。大多数情况一个App就运行在一个进程中，除非在AndroidManifest.xml中配置Android:process属性，或通过native代码fork进程。 

- 线程 
线程对应用来说非常常见，比如每次new Thread().start都会创建一个新的线程。该线程与App所在进程之间资源共享，从Linux角度来说进程与线程除了是否共享资源外，并没有本质的区别，都是一个task_struct结构体，在CPU看来进程或线程无非就是一段可执行的代码，CPU采用CFS调度算法，保证每个task都尽可能公平的享有CPU时间片。

其实承载ActivityThread的主线程就是由Zygote fork而创建的进程。


### 子线程有哪些更新UI的方法

1. 主线程中定义Handler，子线程通过mHandler发送消息，主线程Handler的`handleMessage`更新UI。
2. 用Activity对象的runOnUiThread方法。
3. 创建Handler，传入`getMainLooper`。
4. View.post(Runnable r) 。


#### runOnUiThread

Looper在哪个线程创建，就跟哪个线程绑定，并且Handler是在他关联的Looper对应的线程中处理消息的。（敲黑板）

```java
new Thread(new Runnable() {
    @Override
    public void run() {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                //DO UI method
            }
        });
    }
}).start();
```

```
/* Activity源码 */
final Handler mHandler = new Handler();
public final void runOnUiThread(Runnable action) {
    if (Thread.currentThread() != mUiThread) {
        mHandler.post(action);//子线程（非UI线程）
    } else {
        action.run();
    }
}
```

进入Activity类里面，可以看到如果是在子线程中，通过`mHandler`发送的更新UI消息。 
而这个Handler是在Activity中创建的，也就是说在主线程中创建，所以便和我们在主线程中使用Handler更新UI没有差别。 
因为这个Looper，就是ActivityThread中创建的Looper（`Looper.prepareMainLooper()`）。

#### 创建Handler，传入`getMainLooper`

同理，我们在子线程中，是否也可以创建一个Handler，并获取`MainLooper`，从而在子线程中更新UI呢？ 
首先我们看到，在`Looper`类中有静态对象`sMainLooper`，并且这个`sMainLooper`就是在ActivityThread中创建的`MainLooper`。

```java
/* Looper源码 */
private static Looper sMainLooper;  // guarded by Looper.class
public static void prepareMainLooper() {
    prepare(false);
    synchronized (Looper.class) {
        if (sMainLooper != null) {
            throw new IllegalStateException("The main Looper has already been prepared.");
        }
        sMainLooper = myLooper();
    }
}
```

所以不用多说，我们就可以通过这个`sMainLooper`来进行更新UI操作。
```java
new Thread(new Runnable() {
    @Override
    public void run() {
        Log.e("qdx", "step 1 "+Thread.currentThread().getName());
        Handler handler=new Handler(getMainLooper());
        handler.post(new Runnable() {
            @Override
            public void run() {
                //Do Ui method
                Log.e("qdx", "step 2 "+Thread.currentThread().getName());
            }
        });
    }
}).start();
```

![](http://upload-images.jianshu.io/upload_images/9028834-5bbaf9b68d0f6492?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### View.post(Runnable r)

老样子，我们点入源码
```java
/* View源码 */
/**
 * <p>Causes the Runnable to be added to the message queue.
 * The runnable will be run on the user interface thread.</p>
 *
 * @param action The Runnable that will be executed.
 *
 * @return Returns true if the Runnable was successfully placed in to the
 *         message queue.  Returns false on failure, usually because the
 *         looper processing the message queue is exiting.
 *
 */
public boolean post(Runnable action) {
    final AttachInfo attachInfo = mAttachInfo;
    if (attachInfo != null) {
        return attachInfo.mHandler.post(action); //一般情况走这里
    }

    // Postpone the runnable until we know on which thread it needs to run.
    // Assume that the runnable will be successfully placed after attach.
    getRunQueue().post(action);
    return true;
}

/**
 * A Handler supplied by a view's {@link android.view.ViewRootImpl}. This
 * handler can be used to pump events in the UI events queue.
 */
final Handler mHandler;
```

居然也是Handler从中作祟，根据Handler的注释，也可以清楚该Handler可以处理UI事件，也就是说它的Looper也是主线程的`sMainLooper`。这就是说我们常用的更新UI都是通过Handler实现的。

另外更新UI 也可以通过`AsyncTask`来实现，这个`AsyncTask`的线程切换也是通过 Handler 。

### 子线程中Toast、showDialog的方法


```java
new Thread(new Runnable() {
    @Override
    public void run() {
        Toast.makeText(MainActivity.this, "run on thread", Toast.LENGTH_SHORT).show();//崩溃无疑
    }
}).start();
```

![](http://upload-images.jianshu.io/upload_images/9028834-1f4897146e1a1ef8?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

看到这个崩溃日志，是否有些疑惑，因为一般如果子线程不能更新UI控件是会报如下错误的（子线程不能更新UI） 

![](http://upload-images.jianshu.io/upload_images/9028834-b7782aa2b8145f21?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

所以**子线程不能更新Toast的原因就和Handler有关**了，据我们了解，每一个Handler都要有对应的Looper对象，那么。 
满足你：
```java
new Thread(new Runnable() {
    @Override
    public void run() {
        Looper.prepare();
        Toast.makeText(MainActivity.this, "run on thread", Toast.LENGTH_SHORT).show();//成功在子线程中Toast
        Looper.loop();
    }
}).start();
```

这样便能在子线程中Toast。
看一下Toast内部执行方式：
```java
/* Toast源码 */
/**
 * Show the view for the specified duration.
 */
public void show() {
    ``````
    INotificationManager service = getService();//从SMgr中获取名为notification的服务
    String pkg = mContext.getOpPackageName();
    TN tn = mTN;
    tn.mNextView = mNextView;

    try {
        service.enqueueToast(pkg, tn, mDuration);//enqueue? 难不成和Handler的队列有关?
    } catch (RemoteException e) {
        // Empty
    }
}
```

在`show`方法中，我们看到Toast的show方法和普通UI 控件不太一样，并且也是通过Binder进程间通讯方法执行Toast绘制。这其中的过程就不在多讨论了，有兴趣的可以在`NotificationManagerService`类中分析。

现在把目光放在`TN` 这个类上（难道越重要的类命名就越简洁，如`H`类），通过`TN` 类，可以了解到它是Binder的本地类。在Toast的show方法中，将这个`TN`对象传给`NotificationManagerService`就是为了通讯！并且我们也在TN中发现了它的show方法。

```java
/* Toast源码 */
private static class TN extends ITransientNotification.Stub {//Binder服务端的具体实现类
    /**
     * schedule handleShow into the right thread
     */
    @Override
    public void show(IBinder windowToken) {
        mHandler.obtainMessage(0, windowToken).sendToTarget();
    }
    final Handler mHandler = new Handler() {
        @Override
        public void handleMessage(Message msg) {
            IBinder token = (IBinder) msg.obj;
            handleShow(token);
        }
    };
}
```

看完上面代码，就知道子线程中Toast报错的原因，因为在`TN`中使用Handler，所以需要创建`Looper`对象。 
那么既然用Handler来发送消息，就可以在`handleMessage`中找到更新Toast的方法。 
在`handleMessage`看到由`handleShow`处理。

```java
/* Toast源码 */
//Toast的TN类
public void handleShow(IBinder windowToken) {
        ``````
        mWM = (WindowManager)context.getSystemService(Context.WINDOW_SERVICE);

        mParams.x = mX;
        mParams.y = mY;
        mParams.verticalMargin = mVerticalMargin;
        mParams.horizontalMargin = mHorizontalMargin;
        mParams.packageName = packageName;
        mParams.hideTimeoutMilliseconds = mDuration ==
            Toast.LENGTH_LONG ? LONG_DURATION_TIMEOUT : SHORT_DURATION_TIMEOUT;
        mParams.token = windowToken;
        if (mView.getParent() != null) {
            mWM.removeView(mView);
        }
        mWM.addView(mView, mParams);//使用WindowManager的addView方法
        trySendAccessibilityEvent();
    }
}
```

#### 总结

Toast本质是通过window显示和绘制的（操作的是window），而子线程不能更新UI 是因为ViewRootImpl的checkThread方法在Activity维护的View树的行为。 
Toast中TN类使用Handler是为了用队列和时间控制排队显示Toast，所以为了防止在创建TN时抛出异常，需要在子线程中使用Looper.prepare();和Looper.loop();（但是不建议这么做，因为它会使线程无法执行结束，导致内存泄露）


Dialog亦是如此。同时我们又多了一个知识点要去研究：Android 中Window是什么，它内部有什么机制？

### 如何处理Handler 使用不当导致的内存泄露？

首先上文在子线程中为了节目效果，使用如下方式创建Looper

```java
Looper.prepare();
...
Looper.loop();
```

实际上这是**非常危险**的一种做法 

> 在子线程中，如果手动为其创建Looper，那么在所有的事情完成以后应该调用quit方法来终止消息循环，否则这个子线程就会一直处于等待的状态，而如果退出Looper以后，这个线程就会立刻终止，因此建议不需要的时候终止Looper。(【 Looper.myLooper().quit(); 】)

那么，如果在Handler的`handleMessage`方法中（或者是run方法）处理消息，如果这个是一个延时消息，会一直保存在主线程的消息队列里，并且会影响系统对Activity的回收，造成内存泄露。

解决Handler内存泄露主要2点
1. 有延时消息，要在Activity销毁的时候移除`Messages`
2. [匿名内部类](http://www.cnblogs.com/nerxious/archive/2013/01/25/2876489.html "optional title")导致的泄露改为匿名静态内部类，并且对上下文或者Activity使用弱引用。


# 消息机制的应用

在Android中有很多消息机制的应用，如： 

- UI的更新
- HandlerThread
- IntentService

### UI的更新

【见 常见问题-子线程有哪些更新UI的方法】

### IntentService

IntentService继承自Service，运行时优先级更高，内部使用了HandlerThread作为处理消息的线程。内部有一个私有内部类ServiceHandler继承自Handler，并且会创建一个ServiceHandler对象。 
使用startService()方法启动IntentService时，不会重新创建一个服务，会调用ServiceHandler对象发送包含该Intent的Message对象，该对象通过HandlerThread处理后交给ServiceHandler重写的handleMessage方法进行处理，处理的方式是调用IntentService的onHandleIntent（Intent）方法，所以使用的方式就是创建一个继承自IntentService类的子类，并重写onHandleIntent方法，在该方法中处理startService时传递的Intent。Intent中包含有要交给Service处理的信息。















# 引用：
★★★★★[Android的消息机制](http://blog.csdn.net/c10WTiybQ1Ye3/article/details/78098843)
★★★★★[Android消息机制1-Handler(Java层)](http://gityuan.com/2015/12/26/handler-message-framework/)
★★[Android 源码分析之旅3.1--消息机制源码分析](https://www.jianshu.com/p/ac50ba6ba3a2)
★★★[Android 消息机制——你真的了解Handler？](http://blog.csdn.net/qian520ao/article/details/78262289)
[Android消息机制-ThreadLocal原理解析：数据存取](https://www.jianshu.com/p/cadf7c824677)
[Android消息机制详解](https://blog.csdn.net/code__man/article/details/79108191)
[Android Handler消息机制实现原理](https://www.jianshu.com/p/6cc4d4b4676b)
★★★★[Android基础夯实--你了解Handler有多少？](http://www.cnblogs.com/ryanleee/p/8204450.html)
★★★[Android消息机制3-Handler(实战)](http://gityuan.com/2016/01/01/handler-message-usage/)

