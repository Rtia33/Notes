

# 【Android 控件 RemoteViews】
# [完全理解Android中的RemoteViews](https://blog.csdn.net/u014628886/article/details/51930762)

一、什么是RemoteViews
RemoteViews翻译过来就是远程视图.顾名思义,RemoteViews不是当前进程的View,是属于SystemServer进程.应用程序与RemoteViews之间依赖Binder实现了进程间通信.
二、RemoteViews的用法
RemoteViews使用最多的场合是通知栏和桌面小插件. 以通知栏为例,讲解下它的用法.
1、新建一个Notification
这里要注意是在android3.0之前都是使用如下的形式构建一个Notification
```
        // 1.新建一个Notification对象
        Notification mNotification = new Notification();
        // 2.添加属性,比如标题、内容、优先级、图片等
        mNotification.tickerText = "这是通知栏的标题";
        mNotification.icon = R.drawable.ic_launcher;
        mNotification.flags=Notification.FLAG_NO_CLEAR;
        mNotification.setLatestEventInfo(this, "这是内容", "这是标题", null);
```
在3.0之后官方推荐使用建造者模式创建Notification.
```
Notification mNotification = new Notification.Builder(this)
        .setContentTitle("这是标题 ")    
        .setContentText("这是内容")        
        .setSmallIcon(R.drawable.ic_launcher)                
        .build();
```

Notification有很多属性,这里列举一些
```
 - setContentTitle       设置标题
 - setContentText        设置内容
 - setLargeIcon          设置通知栏大图标
 - setSmallIcon          设置通知栏小图标
 - setContent            设置RemoteViews
 - setContentIntent      当通知条目被点击，就执行这个被设置的Intent.
 - setDeleteIntent       当用户点击"Clear All Notifications"按钮区删除所有的通知的时候，这个被设置的Intent被执行
 - setLights             设置闪光灯
 - setSound              设置声音
 - setPriority           设置优先级
```
2、设置Notification的RemoteViews
如果要给通知栏使用自定义布局就要使用RemoteViews了,传入包名和相应的布局.
```
RemoteViews mRemoteViews=new RemoteViews("com.example.remoteviewdemo", R.layout.remoteview_layout);
```

然后通过setContent()传入RemoteViews 对象即可.
这里顺便讲一下PendingIntent,PendingIntent是”延迟意图”的意思,就是当满足某一条件时出触发这个Intent.通过PendingIntent的getActivity、getBroadcast、getService等分别构建一个打开对应组件的延迟Intent. 
传入四个参数，context、intent、requestCode(自定义)、flag.
```
Intent intent=new Intent(MainActivity.this,MainActivity.class);
PendingIntent mPendingIntent=PendingIntent.getActivity(MainActivity.this, 0, intent, PendingIntent.FLAG_UPDATE_CURRENT);
```

PendingIntent有4种flag.
```
 - FLAG_ONE_SHOT                只执行一次
 - FLAG_NO_CREATE               若描述的Intent不存在则返回NULL值
 - FLAG_CANCEL_CURRENT          如果描述的PendingIntent已经存在，则在产生新的Intent之前会先取消掉当前的
 - FLAG_UPDATE_CURRENT          总是执行,这个flag用的最多
```
3、获取通知管理者
```
NotificationManager manager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
```

4、弹出通知
调用notify方法,传入一个id(自定义)和通知实例即可.
```
manager.notify(1, mNotification);
```

5、例子
我用一个按钮弹出通知,点击这个通知时进入到该Activity
```
public class MainActivity extends Activity {
 
    private NotificationManager manager;
    private Notification mNotification;
 
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        //1.创建RemoteViews实例
        RemoteViews mRemoteViews=new RemoteViews("com.example.remoteviewdemo", R.layout.remoteview_layout);
 
        //2.构建一个打开Activity的PendingIntent
        Intent intent=new Intent(MainActivity.this,MainActivity.class);
        PendingIntent mPendingIntent=PendingIntent.getActivity(MainActivity.this, 0, intent, PendingIntent.FLAG_UPDATE_CURRENT);
 
        //3.创建一个Notification
        mNotification = new Notification.Builder(this)
        .setSmallIcon(R.drawable.ic_launcher)
        .setContentIntent(mPendingIntent)
        .setContent(mRemoteViews)
        .build();
 
        //4.获取NotificationManager
        manager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
 
        Button button1 = (Button) findViewById(R.id.button1);
        button1.setOnClickListener(new OnClickListener() {
 
            @Override
            public void onClick(View v) {
                //弹出通知
                manager.notify(1, mNotification);
            }
        });
 
    }
}
```

如下图
![](https://upload-images.jianshu.io/upload_images/9028834-182729291e86d299.gif?imageMogr2/auto-orient/strip)

6、改变RemoteViews的布局
RemoteViews并不能直接获得控件实例,然后对控件进行操作.它提供了 
setTextViewText(viewId, text)、setImageViewResource(viewId, srcId)等方法进行操作,传入控件id和相应的修改内容. 
列举一下常用的属性
```
 - setTextViewText(viewId, text)                     设置文本
 - setTextColor(viewId, color)                       设置文本颜色
 - setTextViewTextSize(viewId, units, size)          设置文本大小 
 - setImageViewBitmap(viewId, bitmap)                设置图片
 - setImageViewResource(viewId, srcId)               根据图片资源设置图片
 - setViewPadding(viewId, left, top, right, bottom)  设置Padding间距
 - setOnClickPendingIntent(viewId, pendingIntent)    设置点击事件 
```
我这里就以setTextViewText改变文本的属性来讲解改变RemoteViews的原理. 
我在原来的代码上加上一个按钮点击改变内容
```
Button button2 = (Button) findViewById(R.id.button2);
button2.setOnClickListener(new OnClickListener() {
 
            @Override
            public void onClick(View v) {
                mRemoteViews.setTextViewText(R.id.remote_content, "改变了内容");
                manager.notify(1, mNotification);
            }
        });
```

看下效果
![](https://upload-images.jianshu.io/upload_images/9028834-1f542c170eac4286.gif?imageMogr2/auto-orient/strip)

三、RemoteViews的改变原理
1.setTextViewText方法代码如下
```
public class RemoteViews implements Parcelable, Filter {
    ......
public void setTextViewText(int viewId, CharSequence text) {
        setCharSequence(viewId, "setText", text);
    }
    ......
    }
```
2.调用了setCharSequence方法
```
public class RemoteViews implements Parcelable, Filter {
    ......
public void setCharSequence(int viewId, String methodName, CharSequence value) {
        addAction(new ReflectionAction(viewId, methodName, ReflectionAction.CHAR_SEQUENCE, value));
    }
     ......
    }
```

3.在setCharSequence方法里调用了addAction方法,传入一个ReflectionAction实例，ReflectionAction继承自Action,它是用反射调用的
```
private final class ReflectionAction extends Action {
    ......
    ReflectionAction(int viewId, String methodName, int type, Object value) {
            this.viewId = viewId;
            this.methodName = methodName;
            this.type = type;
            this.value = value;
        }
   ......
   }     
```

4.看下addAction方法，用了一个集合来保存Action实例,然后更新已使用内存的统计情况
```
public class RemoteViews implements Parcelable, Filter {
    ......
private void addAction(Action a) {
 
        if (mActions == null) {
            mActions = new ArrayList<Action>();
        }
        //添加Action
        mActions.add(a);
 
        // 更新已使用内存的统计情况
        a.updateMemoryUsageEstimate(mMemoryUsageCounter);
    }
    ......
   }   
```

这一步之后,会调用
```
manager.notify(1, mNotification);
```

来更新,追踪这个notify方法.
```
public class NotificationManager
{
    ......
public void notify(String tag, int id, Notification notification)
    {
         ......
         INotificationManager service = getService();
         try {
            service.enqueueNotificationWithTag(pkg, mContext.getOpPackageName(), tag, id,
                    stripped, idOut, UserHandle.myUserId());
         ......           
     }
    ......
    }
```

5.上面会调用getService方法返回INotificationManager这个系统服务,它是在SystemServer进程添加的.然后该服务调用 enqueueNotificationWithTag方法最后层层调用到
```
public class NotificationManagerService extends INotificationManager.Stub
{
    ......
StatusBarNotification n = new StatusBarNotification(pkg, id, tag, r.uid, r.initialPid, notification);
try {                      mStatusBar.updateNotification(r.statusBarKey, n) 
    }
    ......
    }
```

新建了StatusBarNotification实例,然后调用updateNotification方法. 
这个方法会进入到
```
public class PhoneStatusBar extends StatusBar {
    ......
public void updateNotification(IBinder key, StatusBarNotification notification) {
    ......
     final RemoteViews contentView = notification.notification.contentView;
    ......
    contentView.reapply(mContext, oldEntry.content);
    ......
    }
```

会调用StatusBarNotification 的notification.contentView返回RemoteViews 对象,然后调用reapply方法.
6.回到RemoteViews 的reapply方法
```
public class RemoteViews implements Parcelable, Filter {
    ......
public void reapply(Context context, View v, OnClickHandler handler) {
 
 RemoteViews rvToApply = getRemoteViewsToApply(context);
 ......
 rvToApply.performApply(v, (ViewGroup) v.getParent(), handler);
 
    }
 
private void performApply(View v, ViewGroup parent, OnClickHandler handler) {
        if (mActions != null) {
            handler = handler == null ? DEFAULT_ON_CLICK_HANDLER : handler;
            final int count = mActions.size();
            for (int i = 0; i < count; i++) {
                Action a = mActions.get(i);
                //调用apply方法
                a.apply(v, parent, handler);
            }
        }
    }
 
     ......
    }
```

最终调用apply方法,在这里加载新的布局,RemoteViews就是这么完成的.
```
public class RemoteViews implements Parcelable, Filter {
    ......
public View apply(Context context, ViewGroup parent, OnClickHandler handler) {
RemoteViews rvToApply = getRemoteViewsToApply(context);
        View result;
LayoutInflater inflater =(LayoutInflater)context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
        ......
        //加载布局
        result = inflater.inflate(rvToApply.getLayoutId(), parent, false);
 
        rvToApply.performApply(result, parent, handler);
 
        return result;
    }
     ......
    }
```

总结
RemoteViews运行在SystemServer进程,更新RemoteViews要通过Binder获取到对应的服务然后调用RemoteViews内部的apply方法加载更新布局.



# [Android神奇“控件”-----RemoteViews](https://blog.csdn.net/wrg_20100512/article/details/53940485)



