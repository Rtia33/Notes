

# 【Android 视图 仿微信拖拽透明返回PhotoView】

转自：
[这交互炸了(四) :一分钟让你拥有微信拖拽透明返回PhotoView](https://blog.csdn.net/wingichoy/article/details/53859946)

[![仿微信拖拽透明返回PhotoView.gif](https://upload-images.jianshu.io/upload_images/9028834-79379001cc181dc0.gif?imageMogr2/auto-orient/strip)](https://upload-images.jianshu.io/upload_images/9028834-79379001cc181dc0.gif?imageMogr2/auto-orient/strip)
自我感觉实现的效果还不错哈.猛地一看自己都以为他就是微信了哈哈.

## 一分钟使用方式:

* * *

### 修改你的 `build.gradle`文件

```

//root project
allprojects {
        repositories {
            ...
            maven { url 'https://jitpack.io' }
        }
    }

//module project
        dependencies {
            compile 'com.github.githubwing:DragPhotoView:1.0.1'
    }

```

### 把它放到xml里

把它当成普通ImageView使用就行了,所有ImageView的玩法都可以在它身上玩.**注意必须要加一个onExitListener,这是在拖拽出范围的监听.**

```

    // 所有ImageView用法都可以 
    DragPhotoView photoView = (DragPhotoView)findViewById(R.id.photoView);
    photoView.setImageResource(R.drawable.ram);
    //必须添加一个onExitListener,在拖拽到底部时触发.   
    photoView.setOnExitListener()

    photoView.setOnTapListener()
```

## 这样就完成了接入,甚至比一分钟还快有没有!拒绝标题党.

* * *

上面讲解的是用法,可是用谁都会,使用一个三方库,都要了解实现对不? 下面就给大家介绍实现的思路.

## 实现

### 基于[PhotoView](https://github.com/chrisbanes/PhotoView)

第一眼看到这个效果,就有想实现的冲动,因为使用场景挺多的感觉. 所以直接找来GitHub上star最多的PhotoView来进行扩展,这里我选择直接依赖并且继承PhotoView,理由是如果PhotoView出了更新,依赖直接改动版本即可.如果我选择源码copy的方式改动,那么将得不到PhotoView的支持.

### 图片缩放,背景透明

这里需要Activity配合,将背景设置为透明.并且背景实黑色的,为了配合手势改变View背景透明度,我绘制一个超大的矩形充当背景:

```
  @Override
    protected void onDraw(Canvas canvas) {
        mPaint.setAlpha(mAlpha);
        canvas.drawRect(0, 0, 2000, 2000, mPaint);
        canvas.translate(mTranslateX, mTranslateY);
        canvas.scale(mScale, mScale, mWidth / 2, mHeight / 2);
        super.onDraw(canvas);
    }
```

画布位移和缩放还有透明度都跟着手势变化而变化.所以要处理触摸事件,一开始我尝试重写onTouchEvent方法,发现并不生效,原因是PhotoView内部使用了自己的手势处理机制. 所以手势处理束手无策里吗? 非也,我们可以在dispatchTouchEvent做处理.所以重写该方法:

首先判断是否处于缩放模式,只有非缩放模式的时候才可以拖拽

```

    @Override
    public boolean dispatchTouchEvent(MotionEvent event) {
        if (getScale() == 1) {
        }
    }
```

之后处理ACTION_MOVE事件: 
需要注意的是,要解决一下Viewpager和DragPhotoView和PhotoView的冲突.

DragPhotoView和PhotoView的冲突在于手势缩放,所以只要判断一下当前是几个触摸点即可.

下Viewpager和DragPhotoView冲突在左右滑,所以这里判断为:**如果没有向下移动过,则Y位移为0交由ViewPager处理,如果向下移动过,则改变标志位说明正在处于拖拽状态**

```
     case MotionEvent.ACTION_MOVE:

                    //in viewpager
                    if (mTranslateY == 0 && mTranslateX != 0) {

                        //如果不消费事件，则不作操作
                        if (!isTouchEvent) {
                            mScale = 1;
                            return super.dispatchTouchEvent(event);
                        }
                    }

                    //single finger drag  down
                    if (mTranslateY >= 0 && event.getPointerCount() == 1) {
                        onActionMove(event);

                        //如果有上下位移 则不交给viewpager
                        if (mTranslateY != 0) {
                            isTouchEvent = true;
                        }
                        return true;
                    }

                    //防止下拉的时候双手缩放
                    if (mTranslateY >= 0 && mScale < 0.95) {
                        return true;
                    }
                    break;
```

在ACTION_UP的时候,要判断一下是否拖拽超过阀值,如果超过了阀值则进行结束Activity操作.

这里遇到个坑就是,单指返回和双击放大手势冲突了.目前没有找到什么好的解决方法,只能开启线程计时来根据标志位判断,各位看官有好的解决方式,请联系告知我,谢谢!

```
case MotionEvent.ACTION_UP:
                    //防止下拉的时候双手缩放
                    if (event.getPointerCount() == 1) {
                        onActionUp(event);
                        isTouchEvent = false;
                        //judge finish or not
                        postDelayed(new Runnable() {
                            @Override
                            public void run() {
                                if (mTranslateX == 0 && mTranslateY == 0 && canFinish) {

                                    if (mTapListener != null) {
                                        mTapListener.onTap(DragPhotoView.this);
                                    }
                                }
                                canFinish = false;
                            }
                        }, 300);
                    }
```

这样基本上就完成了对PhotoView的扩展. 已经可以接入项目中使用了.

## 但是本文还没有完,下面说一下共享元素的全版本实现

Android自带的共享元素只有5.0以上才可以使用.那么怎么兼容到5.0以下呢.并且Demo中的拖拽共享是怎么实现的呢?

其实思路很简单,只需要在Activity A启动Activity B的时候,关闭系统专场动画,把被点击的View 大小,坐标等信息传入. B先为透明状态,把B上的View做一个位移动画,就可以实现了.

```
 public  void startPhotoActivity(Context context, ImageView imageView) {
    Intent intent = new Intent(context, DragPhotoActivity.class);
    int location[] = new int[2];

    imageView.getLocationOnScreen(location);
    intent.putExtra("left", location[0]);
    intent.putExtra("top", location[1]);
    intent.putExtra("height", imageView.getHeight());
    intent.putExtra("width", imageView.getWidth());

    context.startActivity(intent);

    //关闭系统共享元素动画
    overridePendingTransition(0,0);
  }
```

至于拖拽共享元素,原理是一样的,具体的细节就在demo中啦~~

本文到此就结束啦~

DragPhotoView项目地址[https://github.com/githubwing/DragPhotoView](https://github.com/githubwing/DragPhotoView)

如果你觉得不错,欢迎Star 
也可以加入我的Android酒馆:425983695



