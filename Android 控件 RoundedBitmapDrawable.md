<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [【Android 控件 RoundedBitmapDrawable】](#android-%E6%8E%A7%E4%BB%B6-roundedbitmapdrawable)
  - [介绍](#%E4%BB%8B%E7%BB%8D)
    - [重要方法](#%E9%87%8D%E8%A6%81%E6%96%B9%E6%B3%95)
    - [源码](#%E6%BA%90%E7%A0%81)
  - [使用](#%E4%BD%BF%E7%94%A8)
    - [创建一个简单的圆角图片](#%E5%88%9B%E5%BB%BA%E4%B8%80%E4%B8%AA%E7%AE%80%E5%8D%95%E7%9A%84%E5%9C%86%E8%A7%92%E5%9B%BE%E7%89%87)
    - [创建一个正圆形图片](#%E5%88%9B%E5%BB%BA%E4%B8%80%E4%B8%AA%E6%AD%A3%E5%9C%86%E5%BD%A2%E5%9B%BE%E7%89%87)
    - [创建一个正圆形带有边框的图片](#%E5%88%9B%E5%BB%BA%E4%B8%80%E4%B8%AA%E6%AD%A3%E5%9C%86%E5%BD%A2%E5%B8%A6%E6%9C%89%E8%BE%B9%E6%A1%86%E7%9A%84%E5%9B%BE%E7%89%87)
    - [其他](#%E5%85%B6%E4%BB%96)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->



# 【Android 控件 RoundedBitmapDrawable】



## 介绍
`RoundedBitmapDrawable` 是 `supportV4` 下的一个类，有了它，显示圆角和圆形图片的情况下就不需要额外的第三方类库了，还能和各种图片加载库配合使用。
[点击此处](http://developer.android.youdaxue.com/reference/android/support/v4/graphics/drawable/RoundedBitmapDrawable.html?_ga=1.118511817.1571171213.1463032326) 可以看到官方的介绍。

### 重要方法
| return | method                                   |
| ------ | ---------------------------------------- |
| void   | **setCircular**(boolean circular) : Sets the image shape to circular. |
| void   | **setCornerRadius**(float cornerRadius) : Sets the corner radius to be applied when drawing the bitmap. |

`setCircular(boolean circular)` : 把图片的形状设为圆形；

`setCornerRadius(float cornerRadius)` : 设置图片的圆角半径。

### 源码
这里贴一下源码，更能清晰的知道它的实现：

```java
    /**
     * Sets the image shape to circular.
     * <p>This overwrites any calls made to {@link #setCornerRadius(float)} so far.</p>
     */
    public void setCircular(boolean circular) {
        mIsCircular = circular;
        mApplyGravity = true;
        if (circular) {
            updateCircularCornerRadius();
            mPaint.setShader(mBitmapShader);
            invalidateSelf();
        } else {
            setCornerRadius(0);
        }
    }

    private void updateCircularCornerRadius() {
        final int minCircularSize = Math.min(mBitmapHeight, mBitmapWidth);
        mCornerRadius = minCircularSize / 2;
    }

    /**
     * Sets the corner radius to be applied when drawing the bitmap.
     */
    public void setCornerRadius(float cornerRadius) {
        if (mCornerRadius == cornerRadius) return;

        mIsCircular = false;
        if (isGreaterThanZero(cornerRadius)) {
            mPaint.setShader(mBitmapShader);
        } else {
            mPaint.setShader(null);
        }

        mCornerRadius = cornerRadius;
        invalidateSelf();
    }
```

至于具体的实现，阅读源码发现官方使用了 `BitmapShader` 来实现的圆角。

## 使用

**效果**

首先来看下原图和处理后效果，以及做一些扩展，如添加一个边框

![img](http://upload-images.jianshu.io/upload_images/1247734-aa39c6f78519c43e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/265)

![img](http://upload-images.jianshu.io/upload_images/1247734-0a7429b07cbd9140.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/279)

### 创建一个简单的圆角图片

```java
Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.photo);
RoundedBitmapDrawable roundedBitmapDrawable = RoundedBitmapDrawableFactory.create(getResources(), bitmap);
roundedBitmapDrawable.setCornerRadius(100);
iv.setImageDrawable(roundedBitmapDrawable );
```

通过`RoundedBitmapDrawableFactory` 传递要转换bitmap 我就可以很简单的生成一个如下图的圆角图片

![](http://upload-images.jianshu.io/upload_images/1247734-343a18a1ef302153.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/235)

### 创建一个正圆形图片

```java
Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.photo);
RoundedBitmapDrawable roundedBitmapDrawable = RoundedBitmapDrawableFactory.create(getResources(), bitmap);
roundedBitmapDrawable.setCircular(true);
```

可以看到我们仅仅只是改了一个属性就实现了如下图正圆形的转换，但你可能已经发现图像有一些变形，因为内部在接收到`circular == true`后先是对图像进行了转换为正方形的操作，这个操作是一个伸缩放操作，而不是裁剪，所以图像发生了变形，所以在使用`setCircular`时最好能保证你的原图时一个正方形的，如果不是的话，下面我们也会给出相应解决方案

![](http://upload-images.jianshu.io/upload_images/1247734-84a128a9bd2254f5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/266)

### 创建一个正圆形带有边框的图片
```java
private Drawable createRoundImageWithBorder(Bitmap bitmap){        
    //原图宽度
    int bitmapWidth = bitmap.getWidth();
    //原图高度
    int bitmapHeight = bitmap.getHeight();
    //边框宽度 pixel
    int borderWidthHalf = 20;

    //转换为正方形后的宽高
    int bitmapSquareWidth = Math.min(bitmapWidth,bitmapHeight);

    //最终图像的宽高
    int newBitmapSquareWidth = bitmapSquareWidth+borderWidthHalf;

    Bitmap roundedBitmap = Bitmap.createBitmap(newBitmapSquareWidth,newBitmapSquareWidth,Bitmap.Config.ARGB_8888);
    Canvas canvas = new Canvas(roundedBitmap);
    int x = borderWidthHalf + bitmapSquareWidth - bitmapWidth;
    int y = borderWidthHalf + bitmapSquareWidth - bitmapHeight;

    //裁剪后图像,注意X,Y要除以2 来进行一个中心裁剪
    canvas.drawBitmap(bitmap, x/2, y/2, null);
    Paint borderPaint = new Paint();
    borderPaint.setStyle(Paint.Style.STROKE);
    borderPaint.setStrokeWidth(borderWidthHalf);
    borderPaint.setColor(Color.WHITE);

    //添加边框
    canvas.drawCircle(canvas.getWidth()/2, canvas.getWidth()/2, newBitmapSquareWidth/2, borderPaint);

    RoundedBitmapDrawable roundedBitmapDrawable = RoundedBitmapDrawableFactory.create(getResources(),roundedBitmap);
    roundedBitmapDrawable.setGravity(Gravity.CENTER);
    roundedBitmapDrawable.setCircular(true);
    return roundedBitmapDrawable;
}

```

我们自己进行对bitmap的裁剪来转换成正方形，就解决了上面提到的拉伸问题，再绘制边框就实现了一个如下带边框的正圆形图片

![img](http://upload-images.jianshu.io/upload_images/1247734-0a7429b07cbd9140.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/279)

### 其他

RoundedBitmapDrawable 也可以直接设置转换过程的

- setAlpha 透明度
- setAntiAlias 抗锯齿
- setDither 防抖动

这些操作，来更好的工作

到这个里我们就可以把项目中的圆角图片的控件更换一下，平时记得多留意一下系统提供的一些API,可能会帮我们节省不少时间。



**引用：**
★★★[Android一些容易被忽略的类-RoundedBitmapDrawable](https://www.jianshu.com/p/ed42d8c90a45)
★★[Android 必知必会-使用 supportV4 的 RoundedBitmapDrawable 实现圆角](https://blog.csdn.net/ys743276112/article/details/52316588)

