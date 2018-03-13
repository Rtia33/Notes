<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [【Android 开发模式 MVP】](#android-%E5%BC%80%E5%8F%91%E6%A8%A1%E5%BC%8F-mvp)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->



# 【Android 开发模式 MVP】
https://mp.weixin.qq.com/s/-dTuUMUoFzJpWsdfvEnObw
https://www.2cto.com/kf/201608/535447.html
本篇来自 **study_zhxu** 的投稿。正如文末所表达的，有些朋友会说这么简单的例子如此设计毫无意义。的确，仅凭一篇文章是不能够体现MVP模式的精髓的（之前的Data Binding亦是如此）。所以本文旨在帮助那些还未入门的以及还在传统模式中挣扎的朋友（尤其是多人合作开发）。

**study_zhxu** 的博客地址：http://blog.csdn.net/study_zhxu

前言

随着 **MVP **概念的兴起和发展，**MVP **使用越来越广泛，当然 **MVP **的优势也越来越被认同，在合作开发功能模块细分中 **MVP **有着得天独厚的优势。本篇文章就来简单的说说如何使用 **MVP**。

什么是MVP

**MVP **是 **MVC** 的变种，其实是一种升级。要说 **MVP **就要说说 **MVC**，在 **MVC **中 **Activity **其实是 **View**层级，但是通常在使用中 **Activity即是View也是Controller**，并没有将 **View层 **和 **Controller层 **进行分离， 耦合度大大提高，非常不利于项目的管理。这时候 **MVP **就应运而生了。

**MVP分为三层**

*   Model

*   View

*   Presenter

**MVP模式的核心思想**

**MVP **把 **Activity **中的 **UI逻辑 **抽象成 **View接口**，把 **业务逻辑 **抽象成 **Presenter****接口**，**Mode****l类 **还是原来的 **Model**。

在 **MVP **模式中 **Activity **的功能就是响应生命周期和显示界面，具体其他的工作都丢到了 **Presenter****层 **中进行完成，**Presenter **其实是 **Model层** 和** View层 **的桥梁。

具体的 **MVP **的好处还有很多，就不详细介绍了，下面看看如何使用 **MVP**。

![image](http://upload-images.jianshu.io/upload_images/9028834-5bed7f21348f55a5?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上面一张简单的 **MVP **模式的 UML图，从图中可以看出，使用 **MVP**，至少需要经历以下步骤：

1.  创建 **IPresenter** 接口，把所有业务逻辑的接口都放在这里，并创建它的实现 **PresenterCompl**（在这里可以方便地查看业务功能，由于接口可以有多种实现所以也方便写单元测试）。

2.  创建 **IView **接口，把所有视图逻辑的接口都放在这里，其实现类是当前的 **Activity/Fragment**。

3.  由UML图可以看出，**Activity **里包含了一个 **IPresenter**，而 **PresenterCompl **里又包含了一个 **IView **并且依赖了 **Model**。**Activity **里只保留对 **IPresenter **的调用，其它工作全部留到 **PresenterCompl **中实现。

4.  **Model **并不是必须有的，但是一定会有 **View **和 **Presenter**。

通过上面的介绍，**MVP **的主要特点就是把 **Activity **里的许多逻辑都抽离到 **View **和 **Presenter **接口中去，并由具体的实现类来完成。这种写法多了许多 **IView **和 **IPresenter** 的接口。

在某种程度上加大了开发的工作量，刚开始使用 **MVP **的小伙伴可能会觉得这种写法比较别扭，而且难以记住。其实一开始想太多也没有什么卵用，只要在具体项目中多写几次，就能熟悉 **MVP****模式 **的写法，理解意图，以及享受其带来的好处。

MVP的使用

理论说了这么多其实没有什么卵用，下面就来在代码中看看如何使用吧。

先来看看项目中代码结构，结构图如下：

![image](http://upload-images.jianshu.io/upload_images/9028834-22e0fe3f5a0ce85b?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过代码结构图可以看到看出 **MVP **结构层级分明（在项目中使用 **MVP **最好通过模块进行分层这样便于管理且结构清晰）。

**View层代码**

**ILoginView **接口代码如下：

<pre class="" style="margin: 0px 0px 16px 15px; padding: 10px 20px; max-width: 100%; box-sizing: border-box; word-wrap: normal; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-style: initial; text-decoration-color: initial; font-family: Consolas, &quot;Liberation Mono&quot;, Menlo, Courier, monospace; font-size: 13.6px; font-stretch: normal; line-height: 1.45; overflow: auto; border-radius: 3px; color: rgb(51, 51, 51); background-color: rgb(247, 247, 247);">public interface ILoginView {
    public void onClearText();
    public void onLoginResult(Boolean result, int code);
}</pre>

可以看出 **ILoginView **只是一个接口，简单的定义了两个方法。

看看 **ILoginView **的实现类也就是我们的 **LoginActivity**：

```java
public class LoginActivity extends AppCompatActivity implements ILoginView,View.OnClickListener{
 
    private Button mLogin ;
    private Button mClear ;
    private EditText mName ;
    private EditText mPassWord ;
 
    ILoginPresenter loginPresenter ;
 
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
 
        mLogin = (Button) findViewById(R.id.btn_login);
        mClear = (Button) findViewById(R.id.btn_clear);
        mName = (EditText) findViewById(R.id.et_name);
        mPassWord = (EditText) findViewById(R.id.et_password);
 
        mLogin.setOnClickListener(this);
        mClear.setOnClickListener(this);
 
        loginPresenter = new LoginPresenterCompl(this) ;
 
    }
 
    @Override
    public void onClearText() {
        mName.setText("");
        mPassWord.setText("");
        Toast.makeText(this,"clear",Toast.LENGTH_SHORT).show();
    }
 
    @Override
    public void onLoginResult(Boolean result, int code) {
        mLogin.setEnabled(true);
        mClear.setEnabled(true);
 
        if(result){
            Toast.makeText(this,"登陆成功: "+code,Toast.LENGTH_SHORT).show();
        }else{
            Toast.makeText(this,"登陆失败："+code,Toast.LENGTH_SHORT).show();
        }
    }
 
    @Override
    public void onClick(View v) {
        int id = v.getId() ;
        String name = mName.getText().toString() ;
        String password = mPassWord.getText().toString() ;
 
        switch (id){
            case R.id.btn_login :
                loginPresenter.doLogin(name,password);
                break ;
            case R.id.btn_clear :
                loginPresenter.clear();
                break;
        }
    }
}
```

![image](http://upload-images.jianshu.io/upload_images/9028834-1b5a1e51451d32a1?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在 **LoginActivity **中我们可以看到，**LoginActivity **实现了 **ILoginView **接口，实现了未实现的方法，在代码中可以看出 **LoginActivity **并没有做一些逻辑处理工作，数据处理的工作都是调用 **ILoginPresenter **完成的。

**Presenter代码**

下面就来看看 **ILoginPresenter**：

<pre class="" style="margin: 0px 0px 16px 15px; padding: 10px 20px; max-width: 100%; box-sizing: border-box; word-wrap: normal; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-style: initial; text-decoration-color: initial; font-family: Consolas, &quot;Liberation Mono&quot;, Menlo, Courier, monospace; font-size: 13.6px; font-stretch: normal; line-height: 1.45; overflow: auto; border-radius: 3px; color: rgb(51, 51, 51); background-color: rgb(247, 247, 247);">public interface ILoginPresenter {
    public void clear();
    public void doLogin(String name, String password);
}</pre>

也是简单的接口定义两个未实现的方法。

看看其实现类 **LoginPresenterCompl**：

```
public class LoginPresenterCompl implements ILoginPresenter {
 
    private ILoginView loginView ;
    private User user ;
 
    @Inject
    public LoginPresenterCompl(ILoginView view){
        loginView = view ;
        user = new User("张三","123456") ;
    }
 
    @Override
    public void clear() {
        loginView.onClearText();
    }
 
    @Override
    public void doLogin(String name, String password) {
        boolean result = false ;
        int code = 0 ;
        if(name.equals(user.getName()) && password.equals(user.getPassword())){
            result = true ;
            code = 1 ;
        }else{
            result = false ;
            code = 0 ;
        }
 
        loginView.onLoginResult(result,code);
    }
}
```

![image](http://upload-images.jianshu.io/upload_images/9028834-6d20c40de5e94199?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

该实现类也比较简单，定义了用户名是张三，密码是123456的一个登陆用户，然后进行登陆和清除的操作。

**Model层代码**

**User **的代码如下：

<pre class="" style="margin: 0px 0px 16px 15px; padding: 10px 20px; max-width: 100%; box-sizing: border-box; word-wrap: normal; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-style: initial; text-decoration-color: initial; font-family: Consolas, &quot;Liberation Mono&quot;, Menlo, Courier, monospace; font-size: 13.6px; font-stretch: normal; line-height: 1.45; overflow: auto; border-radius: 3px; color: rgb(51, 51, 51); background-color: rgb(247, 247, 247);">public class User {
    private String name;
    private String password;
    
    public User(String name,String password){
        this.name = name;
        this.password = password;
    }
    
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        this.name = name;
    }
    
    public String getPassword() {
        return password;
    }
    
    public void setPassword(String password) {
        this.password = password;
    }
}</pre>

OK，这就完成了最简单的 **MVP****模式** 了。

看完上述 **MVP **的代码后可能有的同学会说一个简单的登陆就是简单的验证用户名和密码为什么要设计的这么复杂，可能有人会觉得还没有以前的写法简单呢。但是这只是一个简单的实现 **MVP **的例子。

当项目越来越大，代码越来越多的时候就能够真正的体现出 **MVP****模式 **的优势了。

知道了什么是 **MVP **也知道了如何在项目中使用 **MVP**，那MVP的优势到底有哪些呢？

MVP的优势

**使Activity代码更加简洁**

在传统的项目中 **Activity **兼顾着 **Controller **和 **View**，这使得其代码分分钟上千行（本人深受其害），这使得代码难以理解难以维护，看到这样的 **Activity **就想吐。

使用 **MVP **之后，**Activity **就能瘦身许多了，基本上只有 **FindView、SetListener **以及 **Init **的代码。其他的就是对 **Presenter **的调用，还有对 **View****接口 **的实现。这种情形下阅读代码就容易多了。

而且你只要看 **Presenter **的接口，就能明白这个模块都有哪些业务，很快就能定位到具体代码。**Activity **变得容易看懂，容易维护，以后要调整业务、删减功能也就变得简单许多。

**方便进行单元测试**

一般单元测试都是用来测试某些新加的业务逻辑有没有问题，如果采用传统的代码风格，我们可能要先在 **Activity **里写一段测试代码，测试完了再把测试代码删掉换成正式代码，这时如果发现业务有问题又得换回测试代码，咦，测试代码已经删掉了！好吧重新写吧…… 

**MVP **中，由于业务逻辑都在 **Presenter **里，我们完全可以写一个 **PresenterTest **的实现类继承 **Presenter **的接口，现在只要在 **Activity **里把 **Presenter **的创建换成 **PresenterTest**，就能进行单元测试了，测试完再换回来即可。万一发现还得进行测试，那就再换成 **PresenterTest **吧。

**避免Activity内存泄露**

APP发生 **OOM **的最大原因就是出现内存泄露造成APP的内存不够用，而造成内存泄露的两大原因之一就是 **Activity泄露（Activity Leak）**（另一个原因是 **Bitmap泄露（Bitmap Leak）**）。

Java一个强大的功能就是其虚拟机的内存回收机制，这个功能使得Java用户在设计代码的时候，不用像 C++ 用户那样考虑对象的回收问题。然而，Java用户总是喜欢随便写一大堆对象， 然后幻想着虚拟机能帮他们处理好内存的回收工作。可是虚拟机在回收内存的时候，只会回收那些没有被引用的对象，被引用着的对象因为还可能会被调用，所以不能回收。

**Activity** 是有生命周期的，用户随时可能切换 **Activity**，当APP的内存不够用的时候，系统会回收处于后台的Activity的资源以避免 **OOM**。

采用传统的模式，一大堆异步任务和对UI的操作都放在 **Activity **里面，比如你可能从网络下载一张图片，在下载成功的回调里把图片加载到 **Activity **的 **ImageView **里面，所以异步任务保留着对 **Activity **的引用。

这样一来，即使 **Activity **已经被切换到后台（**onDestroy** 已经执行），这些 **异步任务 **仍然保留着对 **Activity **实例的引用， 所以系统就无法回收这个 **Activity **实例了，结果就是 **Activity Leak**。

**Android** 的组件中，**Activity** 对象往往是在堆（Java Heap）里占最多内存的，所以系统会优先回收 **Activity** 对象， 如果有 **Activity Leak**，APP很容易因为内存不够而 **OOM**。

采用 **MVP****模式**，只要在当前的 **Activity **的 **onDestroy **里，分离异步任务对**Activity **的引用，就能避免 **Activity Leak**。

总结

以上就是 **MV****P的简单实现**，可能示例代码太简单无法体现 **MVP****的优势**，但是理解了 **MVP****的思路 **在项目中使用 **MVP **就才能够真正体验到 **MVP **带来的好处优势。



