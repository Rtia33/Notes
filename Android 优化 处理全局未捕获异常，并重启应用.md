<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [【Android 优化 处理全局未捕获异常，并重启应用】](#android-%E4%BC%98%E5%8C%96-%E5%A4%84%E7%90%86%E5%85%A8%E5%B1%80%E6%9C%AA%E6%8D%95%E8%8E%B7%E5%BC%82%E5%B8%B8%E5%B9%B6%E9%87%8D%E5%90%AF%E5%BA%94%E7%94%A8)
    - [1、创建一个CrashHandler类](#1%E5%88%9B%E5%BB%BA%E4%B8%80%E4%B8%AAcrashhandler%E7%B1%BB)
    - [2、在Application的onCreate()里实例化该crashHandler](#2%E5%9C%A8application%E7%9A%84oncreate%E9%87%8C%E5%AE%9E%E4%BE%8B%E5%8C%96%E8%AF%A5crashhandler)
- [引用：](#%E5%BC%95%E7%94%A8)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# 【Android 优化 处理全局未捕获异常，并重启应用】

### 1、创建一个CrashHandler类

```java
public class CrashHandler implements Thread.UncaughtExceptionHandler {
    private static final String TAG = CrashHandler.class.getSimpleName();
    private static CrashHandler instance; // 单例模式
    private Context context; // 程序Context对象
    private Thread.UncaughtExceptionHandler defalutHandler; // 系统默认的UncaughtException处理类

    private CrashHandler() {

    }

    /** 获取CrashHandler实例 */
    public static CrashHandler getInstance() {
        if (instance == null) {
            synchronized (CrashHandler.class) {
                if (instance == null) {
                    instance = new CrashHandler();
                }
            }
        }
        return instance;
    }

    /** 异常处理初始化 */
    public void init(Context context) {
        this.context = context;
        // 获取系统默认的UncaughtException处理器
        defalutHandler = Thread.getDefaultUncaughtExceptionHandler();
        // 设置该CrashHandler为程序的默认处理器
        Thread.setDefaultUncaughtExceptionHandler(this);
    }

    /** 当UncaughtException发生时会转入该函数来处理 */
    @Override
    public void uncaughtException(Thread thread, Throwable ex) {
        // 自定义错误处理
        boolean res = handleException(ex);
        if (!res && defalutHandler != null) {
            // 如果用户没有处理则让系统默认的异常处理器来处理
            defalutHandler.uncaughtException(thread, ex);

        } else {
            try {
                Thread.sleep(1500);//1.5秒后关闭并重启应用
            } catch (InterruptedException e) {
                LogUtil.e(TAG, "error : ", e);
            }
//          关闭并重启应用
            System.gc();
            _restart();
            android.os.Process.killProcess(android.os.Process.myPid());
        }
    }
    /** 重启应用 */
    public void _restart() {
        Intent intent = context.getPackageManager().getLaunchIntentForPackage(context.getPackageName());
        intent.setFlags(Intent.FLAG_ACTIVITY_CLEAR_TASK | Intent.FLAG_ACTIVITY_NEW_TASK);
        context.startActivity(intent);
    }

    /** 自定义错误处理,收集错误信息 发送错误报告等操作均在此完成.
     *
     * @param ex
     * @return true:如果处理了该异常信息;否则返回false.
     */
    private boolean handleException(final Throwable ex) {
        if (ex == null) {
            return false;
        }

        new Thread() {

            @Override
            public void run() {
                Looper.prepare();
                ex.printStackTrace();
                ToastUtils.showToast("应用出现异常，即将重启");
                Looper.loop();
            }

        }.start();

        // 收集设备参数信息 \日志信息
        return true;
    }
}
```

### 2、在Application的onCreate()里实例化该crashHandler

```java
public class MyApplication extends MultiDexApplication {
    @Override
    public void onCreate() {
        super.onCreate();
        ...
        CrashHandler.getInstance().init(this);
        ...
    }
}
```



# 引用：

 [62、在app遇到全局异常时避免直接退出，如何让app接管异常处理？](https://www.cnblogs.com/kunyashaw/p/4934952.html)