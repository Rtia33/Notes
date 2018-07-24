

# 【Android 控件 PhotoView】

转自：[Android PhotoView基本功能实现](https://www.cnblogs.com/zc9527/p/5599317.html)



Android开发过程中，想必都使用过[PhotoView](https://github.com/chrisbanes/PhotoView)来实现图片展示的功能。在最新版的sdk（android-23）有了一个原生的photoView，并且代码实现也很简单，逻辑也很清晰。我们在实际的工作中，遇到的需求可能与这些photoview现有功能有些细微的差别，需要修改，或者重新开发。本文简单介绍下android-23中photoview涉及到的相关技术，相信读者看完后会发现，其实很简单。以下为实现思路和步骤

- 1 自定义一个View

通过自定义视图，继承View，为外界提过public接口方法来设置drawable，或者提供自定义属性在layout文件中设置drawable。在onDraw方法中，将drawable画到canvas上。

- 2 首次显示drawable

图片首次显示时，一般要居中全部显示在屏幕内，并且至少x轴或y轴方向占满屏幕。如何实现？

对于一个drawable，可以获取其原始的宽、高：

```
int drawableWidth = mDrawable.getIntrinsicWidth();
int drawableHeight = mDrawable.getIntrinsicHeight();
```

然后将原始的宽、高，保存到一个RectF对象中：

```
private RectF mTmpRectSrc = new Rect(); 
mTmpRectSrc.set(0, 0, drawableWidth, drawableHeight);
```

自定义的视图在measure完之后，会有一个宽、高：

```
int viewWidth = getWidth();
int viewHeight = getHeight();
```

将view的宽、高也保存到一个RectF对象中：

```
private RectF mTmpRectDst = new RectF();
mTmpRectDst.set(0, 0, viewWidth, viewHeight);
```

这时候，可以计算出一个从 mTmpRectSrc 到 mTmpRectDst的变换矩阵：

```
private Matrix mDrawMatrix = new Matrix();
mDrawMatrix.setRectToRect(mTmpRectSrc, mTmpRectDst, Matrix.ScaleToFit.CENTER);
```

Matrix.scaleToFit.CENTER参数确保图片最后可以：居中全部显示在屏幕内，并且至少x轴或y轴方向占满屏幕。

最后，在onDraw方法中，结合上面到到的mDrawMatrix，即可将drawable画到canvas上：



```
@Override
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
    if (mDrawable != null) {
        int saveCount = canvas.getSaveCount();
        canvas.save();
        canvas.concat(mDrawMatrix);
        mDrawable.draw(canvas);
        canvas.restoreToCount(saveCount);
    }
}
```



- 3 如何获取drawable的实现显示区域

上面讲到了两个RectF区域，和一个matrix。分别是：

mTmpRectSrc: drawable原始矩形区域

mTmpRectDst: view显示矩形区域

mDrawMatrix: 从mTmpRectSrc到mTmpRectDst的变换矩阵

 

如何的到矩阵变换后，图片的实际显示区域？这里要注意，mTmpRectDst并非图片的显示区域，这个矩形区域仅仅是用来限定图片的显示区域，而最终的显示区域并不是他。方法很简单：

```
private RectF mTranslateRect ＝ new Rect();
mDrawMatrix.mapRect(mTranslateRect, mTmpRectSrc);
```

然后通过mTranslateRect，就可以获取最终显示图片矩形区域的letf/top/right/bottom

- 4 识别缩放手势和滑动手势

我们可以自己在onTouchEvent方法中识别出滑动、缩放等手势。但朋友们是否有感觉，这些判断逻辑自己实现起来很繁琐，很难保证逻辑的完美无缺。其实android系统提供了两个很好用的辅助类来帮我们做这些事情：

```
GestureDetector
ScaleGestureDetector
```

GestureDetector可以用来识别滑动（scroll），ScaleGestureDetector可以用来识别缩放（scale）

我们来看看如何处理scroll。

首先定一个GestureDetector:

```
mGestureDetector = new GestureDetector(context, this);
```

this是指GestureDetector.OnGestureListener的实现类，这里可以用view自己来实现。实现的onScroll方法如下：



```
@Override
public boolean onScroll(MotionEvent e1, MotionEvent e2, float distanceX, float distanceY) {
    mDrawMatrix.mapRect(mTranslateRect, mTmpRectSrc);
    distanceX = -distanceX;
    distanceY = -distanceY;
    mDrawMatrix.postTranslate(distanceX, distanceY);
    invalidate();
    return true;
}
```



直接将接口传给我们的x,y方向偏移量设置到matrix中，然后invalidate，view重新draw出来的drawable就移动了。实际应用中，我们可以先获取drawable的实际显示矩形区域，再根据x,y方向偏移量，计算得出最后的偏移量，来限制不可以将图片划出view显示区域。

 

还差一步，需要在view的onTouchEvent方法中，将MotionEvent事件传给GestureDetector，这样就可以用手指来移动图片了：

```
@Override
public boolean onTouchEvent(MotionEvent event) {
    mGestureDetector.onTouchEvent(event);
    return true;
}
```

如果少了这步，GestureDetector根据接触不到MotionEvent，也就无法帮我们识别相关手势。

 

处理缩放几本一样，首先定义detector：

```
private ScaleGestureDetector mScaleGestureDetector = new ScaleGestureDetector(context, this);
```

this是ScaleGestureDetector.OnScaleGestureListener的实现类，这里可以用view自己来实现。实现的方法为：



```
@Override
public boolean onScale(ScaleGestureDetector detector) {
    float factor = detector.getScaleFactor();

    mDrawMatrix.mapRect(mTranslateRect, mTmpRectSrc);

    int width = getWidth();
    float centerX;
    if (factor > 1 && mTranslateRect.right - mTranslateRect.left < width) {
        centerX = mTranslateRect.left * width / (mTranslateRect.left + width - mTranslateRect.right);
    } else {
        centerX = detector.getFocusX();
    }

    int height = getHeight();
    float centerY;
    if (factor > 1 && mTranslateRect.bottom - mTranslateRect.top < height) {
        centerY = mTranslateRect.top * height / (mTranslateRect.top + height - mTranslateRect.bottom);
    } else {
        centerY = detector.getFocusY();
    }
    mDrawMatrix.postScale(factor, factor, centerX, centerY);
    invalidate();
    return true;
}

@Override
public boolean onScaleBegin(ScaleGestureDetector detector) {
    return true;
}

@Override
public void onScaleEnd(ScaleGestureDetector detector) {
}
```



首先，onScaleBegin方法要返回true，否则不会回调onScale。在onScale中，可以获取缩放中心点、缩放比例，以及当前图片的实际显示区域，根据实际需求，计算出一个最终的缩放比例、中心点。最后将结果postScale到matrix中，invalidate，view重新draw之后，就可看到缩放的效果

 

最后，需要在view的onTouchEvent方法中，将触摸时间告知scaleDetector。否则，scaleDetector根本接触不到相关事件，不会回调相关方法：



```
@Override
public boolean onTouchEvent(MotionEvent event) {
    mScaleGestureDetector.onTouchEvent(event);
    mGestureDetector.onTouchEvent(event);
    return true;
}
```



 

- 5 总结

通过以上讲解，可以看出只要我们熟悉了matrix、rect的相关用法，Photoviewer的实现技术其实很简单。常见的fling、snap动画效果，photoview中也有实现，读者感兴趣的话，可以查看源码自行研究。

