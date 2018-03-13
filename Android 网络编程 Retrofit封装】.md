<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [【Android 网络编程 Retrofit封装】](#android-%E7%BD%91%E7%BB%9C%E7%BC%96%E7%A8%8B-retrofit%E5%B0%81%E8%A3%85)
        - [*文章已授权微信公众号 guolin_blog （郭霖）独家发布](#%E6%96%87%E7%AB%A0%E5%B7%B2%E6%8E%88%E6%9D%83%E5%BE%AE%E4%BF%A1%E5%85%AC%E4%BC%97%E5%8F%B7-guolin_blog-%E9%83%AD%E9%9C%96%E7%8B%AC%E5%AE%B6%E5%8F%91%E5%B8%83)
          - [拖拖踏踏的第三篇文章，我又来造轮子了，一直纠结要不要写这个主题的文章，总感觉的自己驾驭不了RxJava这么高深的东西。本篇可能比较多的是个人的理解。](#%E6%8B%96%E6%8B%96%E8%B8%8F%E8%B8%8F%E7%9A%84%E7%AC%AC%E4%B8%89%E7%AF%87%E6%96%87%E7%AB%A0%E6%88%91%E5%8F%88%E6%9D%A5%E9%80%A0%E8%BD%AE%E5%AD%90%E4%BA%86%E4%B8%80%E7%9B%B4%E7%BA%A0%E7%BB%93%E8%A6%81%E4%B8%8D%E8%A6%81%E5%86%99%E8%BF%99%E4%B8%AA%E4%B8%BB%E9%A2%98%E7%9A%84%E6%96%87%E7%AB%A0%E6%80%BB%E6%84%9F%E8%A7%89%E7%9A%84%E8%87%AA%E5%B7%B1%E9%A9%BE%E9%A9%AD%E4%B8%8D%E4%BA%86rxjava%E8%BF%99%E4%B9%88%E9%AB%98%E6%B7%B1%E7%9A%84%E4%B8%9C%E8%A5%BF%E6%9C%AC%E7%AF%87%E5%8F%AF%E8%83%BD%E6%AF%94%E8%BE%83%E5%A4%9A%E7%9A%84%E6%98%AF%E4%B8%AA%E4%BA%BA%E7%9A%84%E7%90%86%E8%A7%A3)
    - [前言](#%E5%89%8D%E8%A8%80)
      - [初始化 Retrofit](#%E5%88%9D%E5%A7%8B%E5%8C%96-retrofit)
      - [封装服务器请求以及返回数据](#%E5%B0%81%E8%A3%85%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%AF%B7%E6%B1%82%E4%BB%A5%E5%8F%8A%E8%BF%94%E5%9B%9E%E6%95%B0%E6%8D%AE)
      - [处理ProgressDialog](#%E5%A4%84%E7%90%86progressdialog)
      - [处理数据缓存](#%E5%A4%84%E7%90%86%E6%95%B0%E6%8D%AE%E7%BC%93%E5%AD%98)
      - [Activity生命周期管理](#activity%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E7%AE%A1%E7%90%86)
          - [使用](#%E4%BD%BF%E7%94%A8)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->



# 【Android 网络编程 Retrofit封装】
[Android RxJava+Retrofit完美封装（缓存，请求，生命周期管理）](https://www.jianshu.com/p/bd758f51742e)

[![96](http://upload-images.jianshu.io/upload_images/9028834-5b100d6a025b447c?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://www.jianshu.com/u/14354bcb0e09)  

[_小河马](https://www.jianshu.com/u/14354bcb0e09) 关注

2016.11.10 17:20* 字数 2485 阅读 24248评论 96喜欢 340赞赏 1

##### *文章已授权微信公众号 guolin_blog （郭霖）独家发布

###### 拖拖踏踏的第三篇文章，我又来造轮子了，一直纠结要不要写这个主题的文章，总感觉的自己驾驭不了RxJava这么高深的东西。本篇可能比较多的是个人的理解。

### 前言

`Retrofit` 和`RxJava`已经出来很久了，很多前辈写了很多不错的文章，在此不得不感谢这些前辈无私奉献的开源精神，能让我们站在巨人的肩膀上望得更远。对于 `RxJava` 不是很了解的同学推荐你们看[扔物线](https://link.jianshu.com/?t=https://github.com/rengwuxian)大神的这篇文章[给 Android 开发者的 RxJava 详解](https://link.jianshu.com/?t=http://gank.io/post/560e15be2dca930e00da1083)一遍看不懂就看第二遍。`Retrofit`的使用可以参考[Android Retrofit 2.0使用](https://link.jianshu.com/?t=http://wuxiaolong.me/2016/01/15/retrofit/)

本文内容是基于`Retrofit + RxJava`做的一些巧妙的封装。参考了很多文章加入了一些自己的理解，请多指教。源码地址[https://github.com/Hemumu/RxSample](https://link.jianshu.com/?t=https://github.com/Hemumu/RxSample)

先放出`build.gradle`

```
    compile 'io.reactivex:rxjava:1.1.0'
    compile 'io.reactivex:rxandroid:1.1.0'
    compile 'com.squareup.retrofit2:retrofit:2.0.0-beta4'
    compile 'com.squareup.retrofit2:converter-gson:2.0.0-beta4'
    compile 'com.squareup.retrofit2:adapter-rxjava:2.0.0-beta4'

```

本文是基于`RxJava1.1.0`和`Retrofit 2.0.0-beta4`来进行的。

#### 初始化 Retrofit

新建类`Api`，此类就是初始化`Retrofit`，提供一个静态方法初始化`Retrofit`非常简单.

```java
    private static ApiService SERVICE;
    /**
     * 请求超时时间
     */
    private static final int DEFAULT_TIMEOUT = 10000;

    public static ApiService getDefault() {
        if (SERVICE == null) {
            //手动创建一个OkHttpClient并设置超时时间
            OkHttpClient.Builder httpClientBuilder = new OkHttpClient.Builder();
            httpClientBuilder.connectTimeout(DEFAULT_TIMEOUT, TimeUnit.SECONDS);
            /**
             * 对所有请求添加请求头
             */
            httpClientBuilder.addInterceptor(new Interceptor() {
                @Override
                public okhttp3.Response intercept(Chain chain) throws IOException {
                    Request request = chain.request();
                    okhttp3.Response originalResponse = chain.proceed(request);
                    return originalResponse.newBuilder().header("key1", "value1").addHeader("key2", "value2").build();
                }
            });
            SERVICE = new Retrofit.Builder()
                    .client(httpClientBuilder.build())
                    .addConverterFactory(GsonConverterFactory.create())
                    .addCallAdapterFactory(RxJavaCallAdapterFactory.create())
                    .baseUrl(Url.BASE_URL)
                    .build().create(ApiService.class);
        }
        return SERVICE;
    }

```

提供一个静态方法初始化`Retrofit`，手动创建了`OkHttpClient`设置了请求的超时时间。并在OkHttp的拦截器中增加了请求头。注意这里是为所有的请求添加了请求头，你可以单独的给请求增加请求头，例如

```java
    @Headers("apikey:b86c2269fe6588bbe3b41924bb2f2da2")
    @GET("/student/login")
    Observable<HttpResult> login(@Query("phone") String phone,  @Query("password") String psw);

```

和`Retrofit`初始化不同的地方就在我们添加了这两句话

```java
.addConverterFactory(GsonConverterFactory.create())
.addCallAdapterFactory(RxJavaCallAdapterFactory.create())

```

service的定义也从这样

```java
@GET("/student/login")
Call<HttpResult> getTopMovie(@Query("start") int start, @Query("count") int count);

```

变成了

```java
@GET("/student/login")
Observable<HttpResult> login(@Query("phone") String phone,  @Query("password") String psw);

```

返回值变成了`Observable`，这个`Observable`不就是RxJava的可观察者（即被观察者）么。

#### 封装服务器请求以及返回数据

用户在使用任何一个网络框架都只关系请求的返回和错误信息，所以对请求的返回和请求要做一个细致的封装。

我们一般请求的返回都是像下面这样

```
{
   "code":"200",
   "message":"Return Successd!",
   "data":{
         "name":"张三"
          "age":3
   }
}

```

如果你们的服务器返回不是这样的格式那你就只有坐下来请他喝茶，跟他好好说（把他头摁进显示器）了。大不了就献出你的菊花吧！

![image](http://upload-images.jianshu.io/upload_images/9028834-53fb43b3463a8496?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对于这样的数据我们肯定要对`code`做出一些判断，不同的code对应不同的错误信息。所以我们新建一个`HttpResult`类，对应上面的数据结构。

```
public class HttpResult<T> {

    private int code;
    private String message;
    private T data;

    public T getData() {
        return data;
    }

    public void setData(T data) {
        this.data = data;
    }

    public int getCode() {
        return code;
    }

    public void setCode(int code) {
        this.code = code;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

}

```

这算是所有实体的一个基类，data可以为任何数据类型。

我们要对所以返回结果进行预处理，新建一个`RxHelper`，预处理无非就是对`code`进行判断和解析，不同的错误返回不同的错误信息，这还不简单。`Rxjava`的`map`操作符不是轻松解决

```
Api.getDefault().login("name","psw")
     .map(new HttpResultFunc<UserEntity>());
     .subscribeOn(Schedulers.io())
     .unsubscribeOn(Schedulers.io())
     .subscribeOn(AndroidSchedulers.mainThread())
     .observeOn(AndroidSchedulers.mainThread())
     .subscribe(subscriber);

    private class HttpResultFunc<T> implements Func1<HttpResult<T>, T> {
        @Override
        public T call(HttpResult<T> httpResult) {
            Log.e("error", httpResult.getData().toString() + "");
            if (httpResult.getCode() != 0) {
                throw new ApiException(httpResult.getCode());
            }
            return httpResult.getData();
        }
    }

```

哟，这不是轻松愉快 so seay么！对code进行了判断，code为0就做对应更新UI或者其他后续操作，不等于0就抛出异常，在`ApiException`中队`code`做处理，根据message字段进行提示用户

```
    private static String getApiExceptionMessage(int code){
        switch (code) {
            case USER_NOT_EXIST:
                message = "该用户不存在";
                break;
            case WRONG_PASSWORD:
                message = "密码错误";
                break;
            default:
                message = "未知错误";
        }
        return message;
    }

```

撒花！！！

![image](http://upload-images.jianshu.io/upload_images/9028834-58d02713207abca4?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然而。。。`RxJava`永远比你想象的强大。`RxJava`中那么多操作符看到我身体不适，有个操作符`compose`。因为我们在每一个请求中都会处理`code`以及一些重用一些操作符，比如用`observeOn`和`subscribeOn`来切换线程。RxJava提供了一种解决方案：[Transformer](https://link.jianshu.com/?t=http://reactivex.io/RxJava/javadoc/rx/Observable.Transformer.html)（转换器），一般情况下就是通过使用操作符[Observable.compose()](https://link.jianshu.com/?t=http://reactivex.io/RxJava/javadoc/rx/Observable.html#compose(rx.Observable.Transformer))来实现。具体可以参考[避免打断链式结构：使用.compose( )操作符](https://www.jianshu.com/p/e9e03194199e)

新建一个`RxHelper`对结果进行预处理，代码

```
public class RxHelper {
    /**
     * 对结果进行预处理
     *
     * @param <T>
     * @return
     */
    public static <T> Observable.Transformer<HttpResult<T>, T> handleResult() {
        return new Observable.Transformer<HttpResult<T>, T>() {
            @Override
            public Observable<T> call(Observable<HttpResult<T>> tObservable) {
                return tObservable.flatMap(new Func1<HttpResult<T>, Observable<T>>() {
                    @Override
                    public Observable<T> call(HttpResult<T> result) {
                        LogUtils.e(result.getCode()+"");
                        if (result.getCode() == 0) {
                            return createData(result.getData());
                        } else {
                            return Observable.error(new ApiException(result.getCode()));
                        }
                    }
                }).subscribeOn(Schedulers.io()).unsubscribeOn(Schedulers.io()).subscribeOn(AndroidSchedulers.mainThread()).observeOn(AndroidSchedulers.mainThread());
            }
        };
    }

    /**
     * 创建成功的数据
     *
     * @param data
     * @param <T>
     * @return
     */
    private static <T> Observable<T> createData(final T data) {
        return Observable.create(new Observable.OnSubscribe<T>() {
            @Override
            public void call(Subscriber<? super T> subscriber) {
                try {
                    subscriber.onNext(data);
                    subscriber.onCompleted();
                } catch (Exception e) {
                    subscriber.onError(e);
                }
            }
        });
    }
}

```

`Transformer`实际上就是一个`Func1<Observable<T>, Observable<R>>`，换言之就是：可以通过它将一种类型的`Observable`转换成另一种类型的`Observable`，和调用一系列的内联操作符是一模一样的。这里我们首先使用`flatMap`操作符把`Obserable<HttpResult<T>>`,转换成为`Observable<T>`在内部对code进行了预处理。如果成功则把结果`Observable<T>`发射给订阅者。反之则把code交给`ApiException`并返回一个异常，`ApiException`中我们对`code`进行相应的处理并返回对应的错误信息

```

public class ApiException extends RuntimeException{

    public static final int USER_NOT_EXIST = 100;
    public static final int WRONG_PASSWORD = 101;
    private static String message;

    public ApiException(int resultCode) {
        this(getApiExceptionMessage(resultCode));
    }

    public ApiException(String detailMessage) {
        super(detailMessage);
    }

    @Override
    public String getMessage() {
        return message;
    }

    /**
     * 由于服务器传递过来的错误信息直接给用户看的话，用户未必能够理解
     * 需要根据错误码对错误信息进行一个转换，在显示给用户
     * @param code
     * @return
     */
    private static String getApiExceptionMessage(int code){
        switch (code) {
            case USER_NOT_EXIST:
                message = "该用户不存在";
                break;
            case WRONG_PASSWORD:
                message = "密码错误";
                break;
            default:
                message = "未知错误";
        }
        return message;
    }
}

```

最后调用了频繁使用的`subscribeOn()`和`observeOn()`以及`unsubscribeOn()`。

#### 处理ProgressDialog

在`Rxjava`中我们什么时候来显示`Dialog`呢。一开始觉得是放在`Subscriber<T>`的`onStart`中。`onStart`可以用作流程开始前的初始化。然而 `onStart()`由于在 `subscribe()`发生时就被调用了，因此不能指定线程，而是只能执行在 `subscribe()`被调用时的线程。所以`onStart`并不能保证永远在主线程运行。

怎么办呢？

![image](http://upload-images.jianshu.io/upload_images/9028834-7dd4e04dea7ec42e?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

千万不要小看了`RxJava`，与 `onStart()`相对应的有一个方法 `doOnSubscribe()`，它和 `onStart()`同样是在`subscribe()`调用后而且在事件发送前执行，但区别在于它可以指定线程。默认情况下， `doOnSubscribe()`执行在 `subscribe()`发生的线程；而如果在 `doOnSubscribe()`之后有 `subscribeOn()`的话，它将执行在离它最近的`subscribeOn()`所指定的线程。可以看到在`RxHelper`中看到我们调用了两次`subscribeOn`，最后一个调用也就是离`doOnSubscribe()`最近的一次`subscribeOn`是指定的`AndroidSchedulers.mainThread()`也就是主线程。这样我们就就能保证它永远都在主线运行了。这里不得不感概`RxJava`的强大。

这里我们自定义一个类`ProgressSubscriber`继承`Subscriber<T>`

```
public  abstract class ProgressSubscriber<T> extends Subscriber<T> implements ProgressCancelListener{

    private SimpleLoadDialog dialogHandler;

    public ProgressSubscriber(Context context) {
        dialogHandler = new SimpleLoadDialog(context,this,true);
    }

    @Override
    public void onCompleted() {
        dismissProgressDialog();
    }

    /**
     * 显示Dialog
     */
    public void showProgressDialog(){
        if (dialogHandler != null) {
            dialogHandler.obtainMessage(SimpleLoadDialog.SHOW_PROGRESS_DIALOG).sendToTarget();
        }
    }

    @Override
    public void onNext(T t) {
        _onNext(t);
    }

    /**
     * 隐藏Dialog
     */
    private void dismissProgressDialog(){
        if (dialogHandler != null) {
            dialogHandler.obtainMessage(SimpleLoadDialog.DISMISS_PROGRESS_DIALOG).sendToTarget();
            dialogHandler=null;
        }
    }
    @Override
    public void onError(Throwable e) {
        e.printStackTrace();
        if (false) { //这里自行替换判断网络的代码
            _onError("网络不可用");
        } else if (e instanceof ApiException) {
            _onError(e.getMessage());
        } else {
            _onError("请求失败，请稍后再试...");
        }
        dismissProgressDialog();
    }

    @Override
    public void onCancelProgress() {
        if (!this.isUnsubscribed()) {
            this.unsubscribe();
        }
    }
    protected abstract void _onNext(T t);
    protected abstract void _onError(String message);
}

```

初始化`ProgressSubscriber`新建了一个我们自己定义的`ProgressDialog`并且传入一个自定义接口`ProgressCancelListener`。此接口是在`SimpleLoadDialog`消失`onCancel`的时候回调的。用于终止网络请求。

```
  load.setOnCancelListener(new DialogInterface.OnCancelListener() {
                @Override
                public void onCancel(DialogInterface dialog) {
                    mProgressCancelListener.onCancelProgress();
                }
    });

```

`ProgressSubscriber`其他就很简单了，在`onCompleted()`和`onError()`的时候取消Dialog。需要的时候调用`showProgressDialog`即可。

#### 处理数据缓存

服务器返回的数据我们肯定要做缓存，所以我们需要一个`RetrofitCache`类来做缓存处理。

```
public class RetrofitCache {
    /**
     * @param cacheKey 缓存的Key
     * @param fromNetwork
     * @param isSave       是否缓存
     * @param forceRefresh 是否强制刷新
     * @param <T>
     * @return
     */
    public static <T> Observable<T> load(final String cacheKey,
                                         Observable<T> fromNetwork,
                                         boolean isSave, boolean forceRefresh) {
        Observable<T> fromCache = Observable.create(new Observable.OnSubscribe<T>() {
            @Override
            public void call(Subscriber<? super T> subscriber) {
                T cache = (T) Hawk.get(cacheKey);
                if (cache != null) {
                    subscriber.onNext(cache);
                } else {
                    subscriber.onCompleted();
                }
            }
        }).subscribeOn(Schedulers.io()).observeOn(AndroidSchedulers.mainThread());
        //是否缓存
        if (isSave) {
            /**
             * 这里的fromNetwork 不需要指定Schedule,在handleRequest中已经变换了
             */
            fromNetwork = fromNetwork.map(new Func1<T, T>() {
                @Override
                public T call(T result) {
                    Hawk.put(cacheKey, result);
                    return result;
                }
            });
        }
        //强制刷新
        if (forceRefresh) {
            return fromNetwork;
        } else {
            return Observable.concat(fromCache, fromNetwork).first();
        }
    }
}

```

几个参数注释上面已经写得很清楚了，不需要过多的解释。这里我们先取了一个`Observable<T>`对象`fromCache`,里面的操作很简单，去缓存里面找个key对应的缓存，如果有就发射数据。在`fromNetwork`里面做的操作仅仅是缓存数据这一操作。最后判断如果强制刷新就直接返回`fromNetwork`反之用`Observable.concat()`做一个合并。`concat`操作符将多个`Observable`结合成一个`Observable`并发射数据。这里又用了`first()`。`fromCache`和`fromNetwork`任何一步一旦发射数据后面的操作都不执行。

最后我们新建一个HttpUtil用来返回用户关心的数据，缓存，显示Dialog在这里面进行。

```
public class HttpUtil{
    /**
     * 构造方法私有
     */
    private HttpUtil() {
    }

    /**
     * 在访问HttpUtil时创建单例
     */
    private static class SingletonHolder {
        private static final HttpUtil INSTANCE = new HttpUtil();
    }

    /**
     * 获取单例
     */
    public static HttpUtil getInstance() {
        return SingletonHolder.INSTANCE;
    }

    //添加线程管理并订阅
    public void toSubscribe(Observable ob, final ProgressSubscriber subscriber,String cacheKey,boolean isSave, boolean forceRefresh) {
        //数据预处理
        Observable.Transformer<HttpResult<Object>, Object> result = RxHelper.handleResult();
          //重用操作符
        Observable observable = ob.compose(result)
                .doOnSubscribe(new Action0() {
                    @Override
                    public void call() {
                        //显示Dialog和一些其他操作
                        subscriber.showProgressDialog();
                    }
                });
        //缓存
        RetrofitCache.load(cacheKey,observable,isSave,forceRefresh).subscribe(subscriber);

    }

```

#### Activity生命周期管理

基本的网络请求都是向服务器请求数据，客户端拿到数据后更新UI。但也不排除意外情况，比如请求回数据途中`Activity`已经不在了，这个时候就应该取消网络请求。
要实现上面的功能其实很简单，两部分

*   随时监听Activity(Fragment)的生命周期并对外发射出去； 在我们的网络请求中，接收生命周期
*   并进行判断，如果该生命周期是自己绑定的，如Destory，那么就断开数据向下传递的过程

实现以上功能需要用到`Rxjava`的`Subject`的子类`PublishSubject`
在你的`BaseActivity`中添加如下代码

```
public class BaseActivity extends AppCompatActivity {

    public final PublishSubject<ActivityLifeCycleEvent> lifecycleSubject = PublishSubject.create();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        lifecycleSubject.onNext(ActivityLifeCycleEvent.CREATE);
        super.onCreate(savedInstanceState);
    }

    @Override
    protected void onPause() {
        lifecycleSubject.onNext(ActivityLifeCycleEvent.PAUSE);
        super.onPause();
    }

    @Override
    protected void onStop() {
        lifecycleSubject.onNext(ActivityLifeCycleEvent.STOP);
        super.onStop();
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        lifecycleSubject.onNext(ActivityLifeCycleEvent.DESTROY);
    }

```

这样的话，我们把所有生命周期事件都传给了`PublishSubject`了，或者说`PublishSubject`已经接收到了并能够对外发射各种生命周期事件的能力了。

现在我们要让网络请求的时候去监听这个`PublishSubject`，在收到相应的生命周期后取消网络请求，这又用到了我们神奇的`compose()`,我们需要修改`handleResult`代码如下

```
public static <T> Observable.Transformer<HttpResult<T>, T> handleResult(final ActivityLifeCycleEvent event,final PublishSubject<ActivityLifeCycleEvent> lifecycleSubject) {
        return new Observable.Transformer<HttpResult<T>, T>() {
            @Override
            public Observable<T> call(Observable<HttpResult<T>> tObservable) {
                Observable<ActivityLifeCycleEvent> compareLifecycleObservable =
                        lifecycleSubject.takeFirst(new Func1<ActivityLifeCycleEvent, Boolean>() {
                            @Override
                            public Boolean call(ActivityLifeCycleEvent activityLifeCycleEvent) {
                                return activityLifeCycleEvent.equals(event);
                            }
                        });
                return tObservable.flatMap(new Func1<HttpResult<T>, Observable<T>>() {
                    @Override
                    public Observable<T> call(HttpResult<T> result) {
                        if (result.getCount() != 0) {
                            return createData(result.getSubjects());
                        } else {
                            return Observable.error(new ApiException(result.getCount()));
                        }
                    }
                }) .takeUntil(compareLifecycleObservable).subscribeOn(Schedulers.io()).unsubscribeOn(Schedulers.io()).subscribeOn(AndroidSchedulers.mainThread()).observeOn(AndroidSchedulers.mainThread());
            }
        };
    }

```

调用的时候增加了两个参数一个是`ActivityLifeCycleEvent` 其实就是一些枚举表示`Activity`的生命周期

```

public enum  ActivityLifeCycleEvent {
    CREATE,
    START,
    RESUME,
    PAUSE,
    STOP,
    DESTROY
}

```

另外一个参数就是我们在`BaseActivity`添加的`PublishSubject`，这里用到了`takeUntil()`它的作用是监听我们创建的`compareLifecycleObservable`，`compareLifecycleObservable`中就是判断了如果当前生命周期和`Activity`一样就发射数据，一旦compareLifecycleObservable 对外发射了数据，就自动把当前的`Observable`(也就是网络请求的`Observable`)停掉。
当然有个库是专门针对这种情况的，叫[RxLifecycle](https://link.jianshu.com/?t=https://github.com/trello/RxLifecycle)，不过要继承他自己的`RxActivity`，当然这个库不只是针对网络请求，其他所有的`Rxjava`都可以。有需要的可以去看看。

最后新建一个`ApiService`存放我们的请求

```
public interface ApiService {
    @GET("/student/mobileRegister")
    Observable<HttpResult<UserEntity>> login(@Query("phone") String phone, @Query("password") String psw);

}

```

###### 使用

使用起来就超级简单了

```

/**
 *
 *
 //  ┏┓　　　┏┓
 //┏┛┻━━━┛┻┓
 //┃　　　　　　　┃
 //┃　　　━　　　┃
 //┃　┳┛　┗┳　┃
 //┃　　　　　　　┃
 //┃　　　┻　　　┃
 //┃　　　　　　　┃
 //┗━┓　　　┏━┛
  //   ┃　　　┃   神兽保佑
  //   ┃　　　┃   阿弥陀佛
  //   ┃　　　┗━━━┓
  //   ┃　　　　　　　┣┓
  //   ┃　　　　　　　┏┛
  //   ┗┓┓┏━┳┓┏┛
  //     ┃┫┫　┃┫┫
  //     ┗┻┛　┗┻┛
  //
  */

      //获取豆瓣电影TOP 100
        Observable ob = Api.getDefault().getTopMovie(0, 100);
        HttpUtil.getInstance().toSubscribe(ob, new ProgressSubscriber<List<Subject>>(this) {
            @Override
            protected void _onError(String message) {
                Toast.makeText(MainActivity.this, message, Toast.LENGTH_LONG).show();
            }

            @Override
            protected void _onNext(List<Subject> list) {

            }

        }, "cacheKey", ActivityLifeCycleEvent.PAUSE, lifecycleSubject, false, false);

```

具体很多东西都可以在使用的时候具体修改，比如缓存我用的`Hawk`。`Dialog`是我自己定义的一个`SimpleLoadDialog`。源码已经给出请多指教！

-------------更新--------------
评论区有人提出对于`Activity`生命周期的管理，个人疏忽大意，特地来加上。

END！

Thanks
[Rx处理服务器请求、缓存的完美封装](https://www.jianshu.com/p/cc064e3d5f21#)
[给 Android 开发者的 RxJava 详解](https://link.jianshu.com/?t=http://gank.io/post/560e15be2dca930e00da1083)
[RxJava 与 Retrofit 结合的最佳实践](https://link.jianshu.com/?t=http://gank.io/post/56e80c2c677659311bed9841)
[可能是东半球最全的RxJava使用场景小结](https://link.jianshu.com/?t=http://blog.csdn.net/theone10211024/article/details/50435325)



