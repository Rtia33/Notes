

# 【Android 网络库 Retrofit原理】
## 前言

估计很多人和我一样，在接触retrofit这个库的时候就被它强大的功能所吸引住了。它不同于传统的网络请求方式的是，retrofit巧妙的采用接口方式进行网络请求，每次调用接口方法，就是对应一次网络请求，这对于长期和丑陋接口做斗争的程序员来说这简直是莫大的福利啊。然而光是用肯定是不行，我们还得搞清其中的原理，知其why。一番周折之后，我发现自己在阅读源码并实现的过程中已经能作一文，于是写出来分享，算是学习中的心得体会。

## 示例

在开始之前，我们先看下一段简单的示例

```
        Retrofit retrofit = new Retrofit.Builder()
                .baseUrl("http://10.21.59.21:8080/")
                .client(new OkHttpClient())
                .addConverterFactory(GsonConverterFactory.create())
                .addCallAdapterFactory(RxJavaCallAdapterFactory.create())
                .build();

        m_weatherApi = retrofit.create(WeatherApi.class);

        findViewById(R.id.id_button).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {

                final Observable<Weather> list = m_weatherApi.getWeather("json");
                list.observeOn(AndroidSchedulers.mainThread()).subscribeOn(Schedulers.newThread())
                        .subscribe(new Subscriber<Weather>() {
                        ...
            }
        });12345678910111213141516171819
```

可以看到，在我们的例子中，我们首先创建了一个retrofit对象，这里使用的是Builder模式，创建过程中我们指定了网络请求时的uri, client， converterFactory （这也是这个库的核心之处），callAdapterFactory。 
之后通过create方法，创建WeatherApi示例， 之后每次调用WeatherApi的方法都是对应于一次请求。 
我们看下WeatherApi:

```
public interface WeatherApi {

    @GET("/{path}")//使用get方式进行Http请求
    //将上述的path替换成指定的path
    //在我们的例子里面 这里会被替换成 "json" 这个字符串
    Observable<Weather> getWeather(@Path("path") String path); 
}
12345678
```

这里都是GET注解 指定请求方式是get,GET中的值会添加在baseUrl中， Path注解指定了具体的请求path，它将替换本文中的{path}字符串为具体的值，在我们的例子中，最后的请求uri会变成： 
<http://10.21.59.21:8080/json>

好了，这里只是简单的看下示例，也是为我们整个全文做个铺垫，读者在阅读过程中要时常记得返回到此，才能加深理解。当然如果您还不了解该库具体的使用方式，可以参考：[retrofit官网] 
(<http://square.github.io/retrofit/>)

我在编写博文的过程中就已经思考过了，要想懂这个库为什么如此设计，光看源码肯定不行。首先要做的就是理解它的Description information， 如果你点击了上面的链接，就可以看到，在它官网首页，就标注了retrofit是一个类型安全的Http客户端（A type-safe HTTP client for Android and Java）。什么叫类型安全呢？为什么要类型安全？这的确有点难懂啊

## Type Safe

从上面的示例代码我们就看到了，在进行网络请求的时候，我们制定了它的客户端——OkHttpClient。也就是说，真正的网络请求都是通过okhttp实现的。然而，我们都使用过okhttp，所以我们都应该知道，在获得服务器请求之后，我们都是通过Response对象来获得服务器返回的数据的，比如这样：

```
        Request request = ...;
        OkHttpClient okHttpClient = new OkHttpClient();
        com.squareup.okhttp.Call call = okHttpClient.newCall(request);
        call.enqueue(new Callback() {
            @Override
            public void onFailure(Request request, IOException e) {

            }

            /**
             * 这里获得服务器的数据
             * @param response
             * @throws IOException
             */
            @Override
            public void onResponse(Response response) throws IOException {
                ResponseBody responseBody = response.body();

                //二进制
                responseBody.bytes();
                //字符串
                responseBody.string();
            }
        });123456789101112131415161718192021222324
```

获得的数据无非就是二进制类型或者字符串类型的。如果服务器返回的是json字符串，我们还得通过gson把它转换成实体类对象。这显然是不够友好的，所以我们要改变。用户无需知道服务器返回的数据具体格式，我们只要知道它最终的类型就好了Book? Person？etc。屏蔽这些细节，专心于业务实现岂不是更好。

而我们的retrofit如何做到的呢？那就是通过converterFactory 来实现了，它主要是负责将服务器返回的数据转换成具体的实例类对象。比如在我们这个例子里面，它的作用就是将服务器返回的json字符串转换成Book实体类对象。

## 分析实现方式

那么我们现在就根据上面的示例实现一个自己的retrofit，不过在开始之前还是需要分析一下实现方式

1：通过Retrofit.Builder对象new 一个Retrofit对象，期间需要配置的有： 
（1）：client，因为有时候我们可能需要在请求头加一个token头，用来作为访问服务器时验证其身份有效性的凭证。 
（2）：baseUrl，所有的请求Uri都是基于它的 
（3）：ConverterFactory， 一种工厂对象，用于根据用户指定的返回值类型，确定最终将服务器返回数据转换成对应实体类对象的Converter类型。在我们的例子里面GsonConverterFactory将选用GsonConverter来转换 
（4）：CallAdapterFactory，网络请求之后返回的是对应的Call< T >类型(这里的T对应于本文的Weather)，然而如果我们结合RxJava使用的话，需要把它再做一次修饰，转换成Observable< T >类型。 
2：注解：http请求是采用Get,post,还是delete，都需要根据描述接口方法的注解来确定，如果是Get的话，我们就需要生成get方式的Request对象，同理于Post Delete方式。所以我们需要一个RequestFactory，它根据方法的注解描述生成对于的Request对象。 
3：通过动态代理，生成接口对应的对象，之后的每次方法Invoke都是上述组建之间互相配合

## 效果

我们先看下最终要到达的效果：

```
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        //新建一个retrofit3 对象 不过我们没有指定CallAdapterFactory
        //因为我们的接口方法直接返回的Call<T>类型 无需修饰
        Retrofit3 retrofit3 = new Retrofit3.Builder()
                .baseUri("http://192.168.1.195:8080")
                .converterFactory(GsonConverterFactory.create()).build();

        //新建一个BookApi对象
        final BookApi bookApi = (BookApi) retrofit3.create(BookApi.class);

        findViewById(R.id.id_button).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {

                new Thread(new Runnable() {
                    @Override
                    public void run() {

                        //这里对应一次网络请求
                        Call<Book> bookCall = bookApi.getBook("json", "100");
                        try {

                            //同步调用 而不是采用异步的方式
                            Book book = bookCall.execute();

                            //获取到了Book对象 之后同步到UI线程展示一下它
                            Message message = m_handler.obtainMessage();
                            message.obj = book;
                            m_handler.sendMessage(message);
                        } catch (IOException e) {
                            e.printStackTrace();
                        }
                    }
                }).start();
            }
        });
    }


    /**
     * 
     */
    private Handler m_handler = new Handler() {
        @Override
        public void handleMessage(Message msg) {
            Book book = (Book) msg.obj;
            Toast.makeText(MainActivity.this, book.getName(), Toast.LENGTH_SHORT).show();
        }
    };
}1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950515253545556
```

再看下BookApi:

```
/**
 * Created by chan on 16/6/3.
 */
public interface BookApi {

    @Get("/{book}")
    Call<Book> getBook(@Path("book") String path, @Query("price") String price);
}12345678
```

## 实现

这里我们先实现创建接口对象，这里使用动态代理技术，如果你还不懂什么是动态代理技术，那么请查阅[动态代理](http://blog.csdn.net/u013022222/article/details/51111814)：

```
public class Retrofit3 {
    /**
     * 生成对应的api
     * @param clazz
     * @param <T>
     * @return
     */
    public <T> T create(Class<T> clazz) {
        Utils.checkNotNull(clazz, "clazz must not be null");

        return (T) Proxy.newProxyInstance(getClass().getClassLoader(), new Class<?>[]{clazz}, new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

                //这里是原版的一个简化
                //原版这里有个很精妙的类 Platform 有兴趣的读者可以自行查阅

                //如果是toString这类函数 则调用当前匿名类自己的Object的方法
                if (method.getDeclaringClass() == Object.class) {
                    return method.invoke(this, args);
                }

                //否则获取method handler 对象，并且调用方法
                return getMethodHandler(method).invoke(args);
            }
        });
    }
}1234567891011121314151617181920212223242526272829
```

还是在Retrofit3中，我们看下getMethodHandler方法：

```
    /**
     * 获得对应的Method handler
     * @param method
     * @return
     */
    private MethodHandler<?> getMethodHandler(Method method) {

        MethodHandler<?> result = null;

        //MethodHandler还是比较耗资源的 所以我们做个缓存，以method为key
        synchronized (m_methodHandlerHashMap) {
            //先从缓存中查找MethodHandler 如果没找到 那么就要new一个出来
            result = m_methodHandlerHashMap.get(method);

            if (result == null) {
                //调用create方法 生成新的method 并缓存
                result = MethodHandler.create(this, method);
                m_methodHandlerHashMap.put(method, result);
            }
        }

        return result;
    }1234567891011121314151617181920212223
```

到这里我们看一看到 ，之后的方法调用其实都是在MethodHandler对象中进行了，我们移步到那个类之中去。

```
/**
 * Created by chan on 16/6/3.
 */
public class MethodHandler<T> {

    private Retrofit3 m_retrofit3;
    private RequestFactory m_requestFactory;
    private ICallAdapter<T> m_callAdapter;
    private IConverter<T> m_converter;

    public MethodHandler(Retrofit3 retrofit3, RequestFactory requestFactory, ICallAdapter<T> callAdapter, IConverter converter) {
        m_retrofit3 = retrofit3;
        m_requestFactory = requestFactory;
        m_callAdapter = callAdapter;
        m_converter = converter;
    }

    /**
     * 方法调用
     * @param args 参数
     * @return 获取到服务器返回值后 返回的java对象
     * @throws IOException
     */
    public Object invoke(Object... args) throws IOException {

        //到这里可以看到callAdapter用于修饰call
        return m_callAdapter.adapt(new OkHttpCall<>(m_retrofit3, m_requestFactory, m_converter, args));
    }

    public static MethodHandler<?> create(Retrofit3 retrofit3, Method method) {

        //用于适配返回值 把Call<T>类型的返回值换成别的类型 比如 T类型  或者  Observable<T>类型
        ICallAdapter<?> callAdapter = retrofit3.getCallAdapter(method);

        //获取Call<T>的模板参数类型T 这个T 将被Converter用于生成对应的实体类对象
        Type responseType = callAdapter.getResponseType();

        //用于把服务器对象生成对应的java实体类对象 比如根据json字符串 生成java实体类对象
        IConverter<?> iConverter = retrofit3.responseConverter(responseType, method.getAnnotations());

        //用于根据每次方法调用 生成对应的okhttp request
        RequestFactory requestFactory = RequestFactory.parse(method, retrofit3);

        //返回它
        return new MethodHandler<>(retrofit3, requestFactory, callAdapter, iConverter);
    }
}
123456789101112131415161718192021222324252627282930313233343536373839404142434445464748
```

可以看到在create函数中我们调用getCallAdapter获得CallAdapter对象，之后这个对象只是用于修饰返回值，把Call< T >类型的返回值变成其他类型，但是，我们为了简便，我们只是简单的返回一下就完成适配工作，如下：

```
    public ICallAdapter<?> getCallAdapter(Method method) {

        //获取方法所有的注解
        Annotation[] annotations = method.getAnnotations();

        //根据方法返回类型  注解 Retrofit3对象 找到合适的CallAdapter
        ICallAdapter<?> callAdapter = m_callAdapterFactory.get(method.getGenericReturnType(), annotations, this);

        if (callAdapter == null) {
            throw new IllegalStateException("can not get call adapter");
        }

        return callAdapter;
    }1234567891011121314
```

在我们的例子里面m_callAdapterFactory实际上是SimpleCallAdapterFactory对象，我们看下源码：

```
/**
 * Created by chan on 16/6/3.
 */
public class SimpleCallAdapterFactory implements ICallAdapter.CallAdapterFactory {

    private SimpleCallAdapterFactory() {
    }

    /**
     * 获取对应的CallAdapter
     * @param returnType 方法的返回类型
     * @param annotations 注解
     * @param retrofit 
     * @return
     */
    @Override
    public ICallAdapter<?> get(Type returnType, Annotation[] annotations, Retrofit3 retrofit) {

        //判断下返回类型是否是Parameterized Type的
        //符合这种类型的有 Set<String> List<String>这种的
        if (!(returnType instanceof ParameterizedType)) {
            return null;
        }

        return getCallAdapter(returnType);
    }

    /**
     * 根据返回值类型获得call adapter
     * @param returnType
     * @return
     */
    private ICallAdapter<?> getCallAdapter(Type returnType) {

        //获得它的原始类型 就是去掉模板参数之后的
        Class<?> clazz = Utils.getRawType(returnType);

        //看是否是Call.class  如果不是 那么当前的返回值 是不能用这个CallAdapter进行修饰的
        if(clazz != Call.class) {
            return null;
        }

        //生成简单的CallAdapter
        return new SimpleCallAdapter(
                Utils.getCallResponseType((ParameterizedType) returnType));
    }

    public static ICallAdapter.CallAdapterFactory create() {
        return new SimpleCallAdapterFactory();
    }
}123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051
```

可以看到这里做了很多检测，看返回值是否没被当前CallAdapter修饰，或者判断返回值类型是否为ParameterizedType

移步SimpleCallAdapter中：

```
/**
 * Created by chan on 16/6/3.
 */
public class SimpleCallAdapter implements ICallAdapter<Call<?>> {

    private Type m_responseType;

    public SimpleCallAdapter(Type responseType) {
        m_responseType = responseType;
    }

    /**
     * @return
     */
    @Override
    public Type getResponseType() {
        return m_responseType;
    }

    /**
     * 只是简单的返回
     */
    @Override
    public <R> Call<R> adapt(Call<R> call) throws IOException {
        return call;
    }
}12345678910111213141516171819202122232425262728
```

构造函数对应的Type是Call< T >中T的实际类型，比如Call< Book >对应的Type就是Book 之后传入到CTOR之中。

再回到之前的代码，在MethodHandler的invoke中，我们new了一个OkHttpCall传入到adapt方法之中：

```
   /**
     * 方法调用
     * @param args 参数
     * @return 获取到服务器返回值后 返回的java对象
     * @throws IOException
     */
    public Object invoke(Object... args) throws IOException {

        //到这里可以看到callAdapter用于修饰call
        return m_callAdapter.adapt(new OkHttpCall<>(m_retrofit3, m_requestFactory, m_converter, args));
    }1234567891011
```

我们看下OkHttpCall:

```
import com.chan.retrofit3lib.Retrofit3;
import com.chan.retrofit3lib.core.facotry.RequestFactory;
import com.chan.retrofit3lib.core.interfaces.Call;
import com.chan.retrofit3lib.core.interfaces.IConverter;
import com.squareup.okhttp.Response;

import java.io.IOException;

/**
 * Created by chan on 16/6/4.
 */
public class OkHttpCall<T> implements Call<T> {

    private IConverter<T> m_converter;
    private Object[] m_args;
    private Retrofit3 m_retrofit3;
    private RequestFactory m_requestFactory;

    public OkHttpCall(Retrofit3 retrofit3, RequestFactory requestFactory, IConverter<T> iConverter, Object... args) {
        m_args = args;
        m_retrofit3 = retrofit3;
        m_converter = iConverter;
        m_requestFactory = requestFactory;
    }

    /**
     * 这里对应我们示例之中的同步网络请求
     * @return
     * @throws IOException
     */
    public T execute() throws IOException {

        //获取OkHttp的call对象
        com.squareup.okhttp.Call call = getRawCall();

        //解析出服务器的返回值
        return parseResponse(call.execute());
    }

    private com.squareup.okhttp.Call getRawCall() {
        //通过request factory创建对应的request 
        //然后返回对应的OKHttp call
        return m_retrofit3.getOkHttpClient().newCall(m_requestFactory.newRequest(m_args));
    }

    /**
     * 解析出服务器返回的对象
     * @param response okhttp的response
     * @return
     * @throws IOException
     */
    private T parseResponse(Response response) throws IOException {

        //通过convert将response转换成java实体类对象
        return m_converter.convert(response);
    }
}123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657
```

这里是核心了。execute代表一次同步调用， 其中涉及的两步我都做了注释。我们可以分别看下requestFacotry是如何生成一个okhttp request对象的 和 converter是如何把服务器返回值转换成java实体类对象的。

## RequsetFactory

```
/**
 * Created by chan on 16/6/3.
 */
public class RequestFactory {

    private Retrofit3 m_retrofit3;
    private Method m_method;

    private Annotation m_httpMethod;
    private String m_uri;

    /**
     * method对应的是接口中的方法
     * @param retrofit3
     * @param method
     */
    private RequestFactory(Retrofit3 retrofit3, Method method) {
        m_retrofit3 = retrofit3;
        m_method = method;

        //获得base uri
        m_uri = m_retrofit3.getBaseUri();

        //获得方法中的注解
        Annotation[] annotations = method.getAnnotations();

        for (int i = 0; annotations != null && i < annotations.length; ++i) {
            if(annotations[i] instanceof Get) {
                m_httpMethod = annotations[i];
            }
            //下面可以是post  put delete
        }
    }

    /**
     * 创建一个ok http 的 request对象
     * @param args
     * @return
     */
    public Request newRequest(Object... args) {

        //扫描method中的参数注解
        Annotation[][] annotations = m_method.getParameterAnnotations();

        String uri = m_uri;

        //如果是有注解的
        if (annotations != null && annotations.length != 0) {
            String path = "";

            //获得Get中的值 它将被添加到base uri后
            if (m_httpMethod instanceof Get) {
                path = ((Get) m_httpMethod).value();
            }

            //一次替换path中的字符串  Query注解对应于在字符串后面添加 诸如 ?A=B&C=D的字符串
            //Path 注解对应于 把诸如 /a/{b} 替换成 /a/b这样的代码
            boolean hasAppendQuestion = false;
            for (int i = 0; i < annotations.length; ++i) {
                Annotation[] array = annotations[i];
                for (int j = 0; j < array.length; ++j) {
                    if (array[j] instanceof Path) {
                        Path p = (Path) array[j];
                        if (TextUtils.isEmpty(p.value())) {
                            throw new IllegalStateException("null path");
                        }

                        String target = "\\{" + p.value() + "\\}";
                        path = path.replaceAll(target, (String) args[i]);
                    } else if (array[j] instanceof Query) {
                        Query query = (Query) array[j];
                        if (TextUtils.isEmpty(query.value())) {
                            throw new IllegalStateException("null query");
                        }

                        if (hasAppendQuestion) {
                            path += "&";
                        } else {
                            path += "?";
                            hasAppendQuestion = true;
                        }
                        path += query.value();
                        path += "=";
                        path += args[i];
                    }
                }
            }

            if (!TextUtils.isEmpty(path)) {
                uri += path;
            }
        }

        //创建一个okhttp request对象
        Request.Builder builder = new Request.Builder();
        builder.url(uri);

        if (m_httpMethod instanceof Get) {
            builder.get();
        }

        return builder.build();
    }

    public static RequestFactory parse(Method method, Retrofit3 retrofit) {
        return new RequestFactory(retrofit, method);
    }
}123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263646566676869707172737475767778798081828384858687888990919293949596979899100101102103104105106107108
```

可以看到这里的业务逻辑还是不复杂的 都是简单的字符串替换，添加，值得注意的是，我们要替换字符串中被大括号包围的字符串 比如 “/a/{b}”，我们必能简单的通过string.replaceAll(“{b}”, “b”)替换，因为replaceAll第一个参数是regexp，要通过”\{b\}”进行转义

## Converter

```
/**
 * Created by chan on 16/6/3.
 */
public class GsonConverter<T> implements IConverter<T> {
    private Gson m_gson;
    private Type m_type;

    public GsonConverter(Gson gson, Type type) {
        m_gson = gson;
        m_type = type;
    }

    @Override
    public T convert(Response response) throws IOException {
        return (T) m_gson.fromJson(response.body().charStream(), m_type);
    }
}1234567891011121314151617
```

这里只简单的调用gson的api就可以得到对应的java实体类对象了。

效果图： 

[![0.gif](https://upload-images.jianshu.io/upload_images/9028834-08ba2b7fd55de954.gif?imageMogr2/auto-orient/strip)](https://upload-images.jianshu.io/upload_images/9028834-08ba2b7fd55de954.gif?imageMogr2/auto-orient/strip)

