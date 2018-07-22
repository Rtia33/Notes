<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [【Android 自定义View 圆形圆角图片】](#android-%E8%87%AA%E5%AE%9A%E4%B9%89view-%E5%9C%86%E5%BD%A2%E5%9C%86%E8%A7%92%E5%9B%BE%E7%89%87)
- [基于Xfermode 实现](#%E5%9F%BA%E4%BA%8Exfermode-%E5%AE%9E%E7%8E%B0)
  - [1、概述](#1%E6%A6%82%E8%BF%B0)
  - [2、原理](#2%E5%8E%9F%E7%90%86)
  - [3、Xfermode实战](#3xfermode%E5%AE%9E%E6%88%98)
    - [1、自定义属性](#1%E8%87%AA%E5%AE%9A%E4%B9%89%E5%B1%9E%E6%80%A7)
    - [2、构造中获取属性](#2%E6%9E%84%E9%80%A0%E4%B8%AD%E8%8E%B7%E5%8F%96%E5%B1%9E%E6%80%A7)
    - [3、onMeasure](#3onmeasure)
    - [4、onDraw](#4ondraw)
  - [4、布局文件及效果图](#4%E5%B8%83%E5%B1%80%E6%96%87%E4%BB%B6%E5%8F%8A%E6%95%88%E6%9E%9C%E5%9B%BE)
- [基于BitmapShader实现](#%E5%9F%BA%E4%BA%8Ebitmapshader%E5%AE%9E%E7%8E%B0)
  - [1、概述](#1%E6%A6%82%E8%BF%B0-1)
  - [2、效果图](#2%E6%95%88%E6%9E%9C%E5%9B%BE)
  - [3、浅谈BitmapShader](#3%E6%B5%85%E8%B0%88bitmapshader)
  - [4、BitmapShader实战](#4bitmapshader%E5%AE%9E%E6%88%98)
    - [①自定义属性](#%E2%91%A0%E8%87%AA%E5%AE%9A%E4%B9%89%E5%B1%9E%E6%80%A7)
    - [②获取自定义属性](#%E2%91%A1%E8%8E%B7%E5%8F%96%E8%87%AA%E5%AE%9A%E4%B9%89%E5%B1%9E%E6%80%A7)
    - [③onMeasure](#%E2%91%A2onmeasure)
    - [④设置BitmapShader](#%E2%91%A3%E8%AE%BE%E7%BD%AEbitmapshader)
    - [⑤绘制](#%E2%91%A4%E7%BB%98%E5%88%B6)
  - [5、状态的存储与恢复](#5%E7%8A%B6%E6%80%81%E7%9A%84%E5%AD%98%E5%82%A8%E4%B8%8E%E6%81%A2%E5%A4%8D)
  - [6、调用](#6%E8%B0%83%E7%94%A8)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->



# 【Android 自定义View 圆形圆角图片】

# 基于Xfermode 实现

## 1、概述
在很久以前也写过一个利用Xfermode 实现圆形、圆角图片的（[Android 完美实现图片圆角和圆形（对实现进行分析）](https://blog.csdn.net/lmj623565791/article/details/24555655)），但是那个继承的是View，其实继承ImageView能方便点，最起码省去了onMeasure里面自己去策略，以及不需要自己去提供设置图片的方法，最主要的是大家对ImageView的API会比较熟悉，用起来会比较顺手。

## 2、原理
![原理](https://upload-images.jianshu.io/upload_images/9028834-e65a1c0fcd359c01.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
原理就不多说了，这张图在我博客里出现的次数大概有3次以上了，我们这次使用的模式**DST_IN**；也就是先绘制形状，再绘制图片，结果是图片和形状重合的图片内容~~

## 3、Xfermode实战
### 1、自定义属性
首先依然是自定义属性，和上篇一致。
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
 
    <attr name="borderRadius" format="dimension" />
    <attr name="type">
        <enum name="circle" value="0" />
        <enum name="round" value="1" />
    </attr>
  
 
    <declare-styleable name="RoundImageViewByXfermode">
        <attr name="borderRadius" />
        <attr name="type" />
    </declare-styleable>
 
</resources>
```
### 2、构造中获取属性
```java
public class RoundImageViewByXfermode extends ImageView{
 
	private Paint mPaint;
	private Xfermode mXfermode = new PorterDuffXfermode(Mode.DST_IN);
	private Bitmap mMaskBitmap;
 
	private WeakReference<Bitmap> mWeakBitmap;
 
	/** 图片的类型，圆形or圆角 */
	private int type;
	public static final int TYPE_CIRCLE = 0;
	public static final int TYPE_ROUND = 1;
	/** 圆角大小的默认值 */
	private static final int BODER_RADIUS_DEFAULT = 10;
	/** 圆角的大小 */
	private int mBorderRadius;
 
	public RoundImageViewByXfermode(Context context)	{
		this(context,null);
 
		mPaint = new Paint();
		mPaint.setAntiAlias(true);
	}
 
	public RoundImageViewByXfermode(Context context, AttributeSet attrs)	{
		super(context, attrs);
 
		mPaint = new Paint();
		mPaint.setAntiAlias(true);
 
		TypedArray a = context.obtainStyledAttributes(attrs,
				R.styleable.RoundImageViewByXfermode);
 
		mBorderRadius = a.getDimensionPixelSize(
				R.styleable.RoundImageViewByXfermode_borderRadius, (int) TypedValue
						.applyDimension(TypedValue.COMPLEX_UNIT_DIP,
								BODER_RADIUS_DEFAULT, getResources()
										.getDisplayMetrics()));// 默认为10dp
		Log.e("TAG", mBorderRadius+"");
		type = a.getInt(R.styleable.RoundImageViewByXfermode_type, TYPE_CIRCLE);// 默认为Circle
 
		a.recycle();
	}
```
获取自定义属性，然后还写些成员变量~~

### 3、onMeasure
```java
@Override
	protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec)	{
		super.onMeasure(widthMeasureSpec, heightMeasureSpec);
 
		/** 如果类型是圆形，则强制改变view的宽高一致，以小值为准 */
		if (type == TYPE_CIRCLE)		{
			int width = Math.min(getMeasuredWidth(), getMeasuredHeight());
			setMeasuredDimension(width, width);
		}
 
	}
```
在onMeasure中，如果是圆形则重新设置view大小。

### 4、onDraw
```java
	@SuppressLint("DrawAllocation")
	@Override
	protected void onDraw(Canvas canvas)	{
		//在缓存中取出bitmap
		Bitmap bitmap = mWeakBitmap == null ? null : mWeakBitmap.get();
 
		if (null == bitmap || bitmap.isRecycled())		{
			//拿到Drawable
			Drawable drawable = getDrawable();
			//获取drawable的宽和高
			int dWidth = drawable.getIntrinsicWidth();
			int dHeight = drawable.getIntrinsicHeight();
 
			if (drawable != null)			{
				//创建bitmap
				bitmap = Bitmap.createBitmap(getWidth(), getHeight(),
						Config.ARGB_8888);
				float scale = 1.0f;
				//创建画布
				Canvas drawCanvas = new Canvas(bitmap);
				//按照bitmap的宽高，以及view的宽高，计算缩放比例；因为设置的src宽高比例可能和imageview的宽高比例不同，这里我们不希望图片失真；
				if (type == TYPE_ROUND)				{
					// 如果图片的宽或者高与view的宽高不匹配，计算出需要缩放的比例；缩放后的图片的宽高，一定要大于我们view的宽高；所以我们这里取大值；
					scale = Math.max(getWidth() * 1.0f / dWidth, getHeight()
							* 1.0f / dHeight);
				} else				{
					scale = getWidth() * 1.0F / Math.min(dWidth, dHeight);
				}
				//根据缩放比例，设置bounds，相当于缩放图片了
				drawable.setBounds(0, 0, (int) (scale * dWidth), (int) (scale * dHeight));
				drawable.draw(drawCanvas);
				if (mMaskBitmap == null || mMaskBitmap.isRecycled()) {
					mMaskBitmap = getBitmap();
				}
				// Draw Bitmap.
				mPaint.reset();
				mPaint.setFilterBitmap(false);
				mPaint.setXfermode(mXfermode);
				//绘制形状
				drawCanvas.drawBitmap(mMaskBitmap, 0, 0, mPaint);
				mPaint.setXfermode(null);
				//将准备好的bitmap绘制出来
				canvas.drawBitmap(bitmap, 0, 0, null);
				//bitmap缓存起来，避免每次调用onDraw，分配内存
				mWeakBitmap = new WeakReference<Bitmap>(bitmap);
			}
		}
		//如果bitmap还存在，则直接绘制即可
		if (bitmap != null) {
			mPaint.setXfermode(null);
			canvas.drawBitmap(bitmap, 0.0f, 0.0f, mPaint);
			return;
		} 
	}
	/** 绘制形状 */
	public Bitmap getBitmap()	{
		Bitmap bitmap = Bitmap.createBitmap(getWidth(), getHeight(),
				Bitmap.Config.ARGB_8888);
		Canvas canvas = new Canvas(bitmap);
		Paint paint = new Paint(Paint.ANTI_ALIAS_FLAG);
		paint.setColor(Color.BLACK);
 
		if (type == TYPE_ROUND) {
			canvas.drawRoundRect(new RectF(0, 0, getWidth(), getHeight()),
					mBorderRadius, mBorderRadius, paint);
		} else {
			canvas.drawCircle(getWidth() / 2, getWidth() / 2, getWidth() / 2,
					paint);
		} 
		return bitmap;
	}
```
在onDraw中拿到drawable，然后获取drawable的宽和高，根据当前的type和view的宽和高，进行适当的缩放。具体原理：参考上篇的matrix的scale计算，原理一致。
然后就是设置Xfermode，getBitmap会根据type返回图形，直接绘制到内存中的bitmap上即可。最后把bitmap缓存起来，避免每次onDraw都分配内存和重启绘图。
大家可以进行扩展type，绘制心形、三角形什么的，直接在getBitmap里面添加分支就可以。
最后记得invalidate中做一些处理：
```java
	@Override
	public void invalidate() {
		mWeakBitmap = null;
		if (mMaskBitmap != null) {
			mMaskBitmap.recycle();
			mMaskBitmap = null;
		}
		super.invalidate();
	}
```
主要是因为我们缓存了，当调用invalidate时，将缓存清除。

## 4、布局文件及效果图
```xml
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:zhy="http://schemas.android.com/apk/res/com.zhy.variousshapeimageview"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" >
 
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical" >
 
 
        <com.zhy.view.RoundImageViewByXfermode
            android:layout_width="130dp"
            android:layout_height="130dp"
            android:layout_margin="10dp"
            android:src="@drawable/qiqiu" >
        </com.zhy.view.RoundImageViewByXfermode>
 
        <com.zhy.view.RoundImageViewByXfermode
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_margin="10dp"
            android:src="@drawable/aa"
            zhy:borderRadius="30dp"
            zhy:type="round" >
        </com.zhy.view.RoundImageViewByXfermode>
 
        <com.zhy.view.RoundImageViewByXfermode
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_margin="10dp"
            android:src="@drawable/aa"
            zhy:type="circle" >
        </com.zhy.view.RoundImageViewByXfermode>
 
        <com.zhy.view.RoundImageViewByXfermode
            android:layout_width="40dp"
            android:layout_height="40dp"
            android:layout_margin="10dp"
            android:src="@drawable/aa"
            zhy:type="circle" >
        </com.zhy.view.RoundImageViewByXfermode>
 
    </LinearLayout>
 
</ScrollView>
```
效果图：
![效果图](https://upload-images.jianshu.io/upload_images/9028834-c45f2a2e52273d99.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
好了，比较简单~~
声明下：本例参考了：https://github.com/MostafaGazar/CustomShapeImageView ;不过对其中的部分代码进行了改变，比如CustomShapeImageView如果图片为长方形，但是设置为circle类型，效果依然是矩形；以及会对bitmap比例和view比例不一致的直接进行压缩，类似fitxy的效果~~~主要对这两点进行了修改~~当然了，该案例比本博客功能要强大，看名字也知道，支持很多形状，以及支持SVG~大家自行进行参考~
[源码点击下载](http://download.csdn.net/detail/lmj623565791/8288639)

# 基于BitmapShader实现
## 1、概述
记得初学那会写过一篇博客[Android 完美实现图片圆角和圆形（对实现进行分析）](http://blog.csdn.net/lmj623565791/article/details/24555655)，主要是个自定View加上使用Xfermode实现的。其实实现圆角图片的方法应该很多，常见的就是利用Xfermode，Shader。本篇博客会直接继承直接继承ImageView，使用BitmapShader实现圆角的绘制，大家如果耐着性子看完，我估计什么形状都能绘制出来。

## 2、效果图
![效果图1.png](https://upload-images.jianshu.io/upload_images/9028834-b1a2fd7799761ca8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这是圆角的一个演示图~~这个没什么说的，直接设置的圆角的大小就行；

![效果图2.png](https://upload-images.jianshu.io/upload_images/9028834-dc7d4a6dc6f59937.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这是圆形的显示图，这里需要注意下，因为设置的图片可能是长方形，例如上图：有两个长方形，一个宽比较大，一个高比较大；
那么我们希望显示成圆形，我们可能就要对其进行放大或者缩小（因为图片的宽可能不满足设置的边长，而高超出，此时我们就需要放大其宽度）。

![效果图3.png](https://upload-images.jianshu.io/upload_images/9028834-243619eea75cbecb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这个一张图，中间是正常尺寸；上下分别为特大特小，主要可以当尺寸大于或者小于设置尺寸，我们需要对其放大或者缩小；
圆角时如果图片与view的宽高不一致，也需要进行放大缩小，这里就不截图了，代码里面看吧。

## 3、浅谈BitmapShader
BitmapShader是Shader的子类，可以通过`Paint.setShader（Shader shader）`进行设置。

这里我们只关注BitmapShader，**构造方法**：

```java
mBitmapShader = new BitmapShader(bitmap, TileMode.CLAMP, TileMode.CLAMP);
```

- 参数1：bitmap
- 参数2，参数3：TileMode；
  **TileMode**的取值有三种：
  **CLAMP 拉伸**
  **REPEAT 重复**
  **MIRROR 镜像**
  如果大家给电脑屏幕设置屏保的时候，如果图片太小，可以选择重复、拉伸、镜像；
  **重复：**就是横向、纵向不断重复这个bitmap
  **镜像：**横向不断翻转重复，纵向不断翻转重复；
  **拉伸：**这个和电脑屏保的模式应该有些不同，这个拉伸的是图片最后的那一个像素；横向的最后一个横行像素，不断的重复，纵项的那一列像素，不断的重复；

现在大概明白了，**BitmapShader通过设置给mPaint**，然后用这个mPaint**绘图时**，就会**根据**你设置的**TileMode**，对绘制区域进行着色。
这里需要注意一点：就是BitmapShader是从你的画布的左上角开始绘制的，不在view的右下角绘制个正方形，它不会在你正方形的左上角开始。
好了，到此，我相信大家对BitmapShader有了一定的了解了；当然了，如果你希望对Shader充分的了解，请参考爱歌的神作： [自定义控件其实很简单1/3](http://blog.csdn.net/aigestudio/article/details/41799811) 。

对于我们的圆角，以及圆形，我们设置的模式都是CLAMP ，但是你会不会会有一个疑问：
**view的宽或者高大于我们的bitmap宽或者高岂不是会拉伸？**
嗯，我们会为BitmapShader设置一个matrix，去适当的放大或者缩小图片，不会让“ view的宽或者高大于我们的bitmap宽或者高 ”此条件成立的。
到此我们的原理基本介绍完毕了，拿到drawable转化为bitmap，然后直接初始化BitmapShader，画笔设置Shader，最后在onDraw里面进行画圆就行了。

## 4、BitmapShader实战
首先就来看看利用BitmapShader实现的圆形或者圆角。
我们这里直接继承ImageView，这样大家设置图片的代码会比较熟悉；但是我们需要支持两种模式，那么就需要自定义属性了：
### ①自定义属性
values/attr.xml
```
<?xml version="1.0" encoding="utf-8"?>
<resources>
 
    <attr name="borderRadius" format="dimension" />
    <attr name="type">
        <enum name="circle" value="0" />
        <enum name="round" value="1" />
    </attr>
  
 
    <declare-styleable name="RoundImageView">
        <attr name="borderRadius" />
        <attr name="type" />
    </declare-styleable>
 
</resources>
```
我们定义了一个枚举和一个圆角的大小borderRadius。
### ②获取自定义属性
```java
public class RoundImageView extends ImageView{
 
	/** 图片的类型，圆形or圆角 */
	private int type;
	private static final int TYPE_CIRCLE = 0;
	private static final int TYPE_ROUND = 1;
 
	/** 圆角大小的默认值 */
	private static final int BODER_RADIUS_DEFAULT = 10;
	/** 圆角的大小 */
	private int mBorderRadius;
 
	/** 绘图的Paint */
	private Paint mBitmapPaint;
	/** 圆角的半径 */
	private int mRadius;
	/** 3x3 矩阵，主要用于缩小放大 */
	private Matrix mMatrix;
	/** 渲染图像，使用图像为绘制图形着色 */
	private BitmapShader mBitmapShader;
	/** view的宽度 */
	private int mWidth;
	private RectF mRoundRect;
 
	public RoundImageView(Context context, AttributeSet attrs)	{
		super(context, attrs);
		mMatrix = new Matrix();
		mBitmapPaint = new Paint();
		mBitmapPaint.setAntiAlias(true);
 
		TypedArray a = context.obtainStyledAttributes(attrs,
				R.styleable.RoundImageView);
 
		mBorderRadius = a.getDimensionPixelSize(
				R.styleable.RoundImageView_borderRadius, (int) TypedValue
						.applyDimension(TypedValue.COMPLEX_UNIT_DIP,
								BODER_RADIUS_DEFAULT, getResources()
										.getDisplayMetrics()));// 默认为10dp
		type = a.getInt(R.styleable.RoundImageView_type, TYPE_CIRCLE);// 默认为Circle
 
		a.recycle();
	}
```
可以看到我们的一些成员变量，基本都加了注释；然后在构造方法中获取了我们的自定义属性，以及部分变量的初始化。

### ③onMeasure
```java
	@Override
	protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec)
	{
		Log.e("TAG", "onMeasure");
		super.onMeasure(widthMeasureSpec, heightMeasureSpec);
 
		/** 如果类型是圆形，则强制改变view的宽高一致，以小值为准 */
		if (type == TYPE_CIRCLE){
			mWidth = Math.min(getMeasuredWidth(), getMeasuredHeight());
			mRadius = mWidth / 2;
			setMeasuredDimension(mWidth, mWidth);
		} 
	}
```
我们复写了onMeasure方法，主要用于当设置类型为圆形时，我们强制让view的宽和高一致。
接下来只剩下设置BitmapShader和绘制了

### ④设置BitmapShader
```java
/** 初始化BitmapShader */
	private void setUpShader(){
		Drawable drawable = getDrawable();
		if (drawable == null){
			return;
		}
 
		Bitmap bmp = drawableToBitamp(drawable);
		// 将bmp作为着色器，就是在指定区域内绘制bmp
		mBitmapShader = new BitmapShader(bmp, TileMode.CLAMP, TileMode.CLAMP);
		float scale = 1.0f;
		if (type == TYPE_CIRCLE){
			// 拿到bitmap宽或高的小值
			int bSize = Math.min(bmp.getWidth(), bmp.getHeight());
			scale = mWidth * 1.0f / bSize;
 
		} else if (type == TYPE_ROUND){
			// 如果图片的宽或者高与view的宽高不匹配，计算出需要缩放的比例；缩放后的图片的宽高，一定要大于我们view的宽高；所以我们这里取大值；
			scale = Math.max(getWidth() * 1.0f / bmp.getWidth(), getHeight()
					* 1.0f / bmp.getHeight());
		}
		// shader的变换矩阵，我们这里主要用于放大或者缩小
		mMatrix.setScale(scale, scale);
		// 设置变换矩阵
		mBitmapShader.setLocalMatrix(mMatrix);
		// 设置shader
		mBitmapPaint.setShader(mBitmapShader);
	}
```
在setUpShader中，首先对drawable转化为我们的bitmap;
然后初始化mBitmapShader = new BitmapShader(bmp, TileMode.CLAMP, TileMode.CLAMP);
接下来，根据类型以及bitmap和view的宽高，计算scale；
- 关于scale的计算：
  圆形时：取bitmap的宽或者高的小值作为基准，如果采用大值，缩放后肯定不能填满我们的圆形区域。然后，view的mWidth/bSize ; 得到的就是scale。
  圆角时：因为设计到宽/高比例，我们分别getWidth() * 1.0f / bmp.getWidth() 和 getHeight() * 1.0f / bmp.getHeight() ；最终取大值，因为我们要让最终缩放完成的图片一定要大于我们的view的区域，有点类似centerCrop；
  比如：view的宽高为10*20；图片的宽高为5*100 ； 最终我们应该按照宽的比例放大，而不是按照高的比例缩小；因为我们需要让缩放后的图片，自定大于我们的view宽高，并保证原图比例。

有了scale，就可以设置给我们的matrix；
然后使用mBitmapShader.setLocalMatrix(mMatrix);
最后将bitmapShader设置给paint。

关于**drawable转bitmap**的代码：
```java
/** drawable转bitmap */
	private Bitmap drawableToBitamp(Drawable drawable)
	{
		if (drawable instanceof BitmapDrawable){
			BitmapDrawable bd = (BitmapDrawable) drawable;
			return bd.getBitmap();
		}
		int w = drawable.getIntrinsicWidth();
		int h = drawable.getIntrinsicHeight();
		Bitmap bitmap = Bitmap.createBitmap(w, h, Bitmap.Config.ARGB_8888);
		Canvas canvas = new Canvas(bitmap);
		drawable.setBounds(0, 0, w, h);
		drawable.draw(canvas);
		return bitmap;
	}
```
最后我们会在onDraw里面调用setUpShader（），然后进行绘制。

### ⑤绘制
到此，就剩下最后一步绘制了，因为我们的范围，以及缩放都完成了，所以真的只剩下绘制了。
```java
@Override
	protected void onDraw(Canvas canvas) {
		if (getDrawable() == null) {
			return;
		}
		setUpShader();
 
		if (type == TYPE_ROUND) {
			canvas.drawRoundRect(mRoundRect, mBorderRadius, mBorderRadius,
					mBitmapPaint);
		} else {
			canvas.drawCircle(mRadius, mRadius, mRadius, mBitmapPaint);
			// drawSomeThing(canvas);
		}
	}
	
	@Override
	protected void onSizeChanged(int w, int h, int oldw, int oldh) {
		super.onSizeChanged(w, h, oldw, oldh);
		// 圆角图片的范围
		if (type == TYPE_ROUND)
			mRoundRect = new RectF(0, 0, getWidth(), getHeight());
	}
```
绘制就很简单了，画个圆，圆角矩形什么的。圆角矩形的限定范围mRoundRect在onSizeChanged里面进行了初始化。

## 5、状态的存储与恢复
当然了，如果内存不足，而恰好我们的Activity置于后台，不幸被重启，或者用户旋转屏幕造成Activity重启，我们的View应该也能尽可能的去保存自己的属性。
状态保存什么用处呢？比如，现在一个的圆角大小是10dp，用户点击后变成50dp；当用户旋转以后，或者长时间置于后台以后，返回我们的Activity应该还是50dp；
我们简单的存储一下，当前的type以及mBorderRadius
```java
private static final String STATE_INSTANCE = "state_instance";
	private static final String STATE_TYPE = "state_type";
	private static final String STATE_BORDER_RADIUS = "state_border_radius";
 
	@Override
	protected Parcelable onSaveInstanceState(){
		Bundle bundle = new Bundle();
		bundle.putParcelable(STATE_INSTANCE, super.onSaveInstanceState());
		bundle.putInt(STATE_TYPE, type);
		bundle.putInt(STATE_BORDER_RADIUS, mBorderRadius);
		return bundle;
	}
 
	@Override
	protected void onRestoreInstanceState(Parcelable state){
		if (state instanceof Bundle){
			Bundle bundle = (Bundle) state;
			super.onRestoreInstanceState(((Bundle) state)
					.getParcelable(STATE_INSTANCE));
			this.type = bundle.getInt(STATE_TYPE);
			this.mBorderRadius = bundle.getInt(STATE_BORDER_RADIUS);
		} else{
			super.onRestoreInstanceState(state);
		} 
	}
```

同时我们也对外公布了两个方法，用于动态修改圆角大小和type
```
public void setBorderRadius(int borderRadius){
		int pxVal = dp2px(borderRadius);
		if (this.mBorderRadius != pxVal){
			this.mBorderRadius = pxVal;
			invalidate();
		}
	}
 
	public void setType(int type){
		if (this.type != type){
			this.type = type;
			if (this.type != TYPE_ROUND && this.type != TYPE_CIRCLE){
				this.type = TYPE_CIRCLE;
			}
			requestLayout();
		}
 
	}
 
	public int dp2px(int dpVal){
		return (int) TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP,
				dpVal, getResources().getDisplayMetrics());
	}
```
最后贴一下我们的布局文件和MainActivity。

## 6、调用
布局文件:
```xml
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:zhy="http://schemas.android.com/apk/res/com.zhy.variousshapeimageview"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" >
 
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical" >
 
        <com.zhy.view.RoundImageView
            android:id="@+id/id_qiqiu"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_margin="10dp"
            android:src="@drawable/qiqiu" >
        </com.zhy.view.RoundImageView>
 
        <com.zhy.view.RoundImageView
            android:layout_width="200dp"
            android:layout_height="200dp"
            android:layout_margin="10dp"
            android:src="@drawable/aa" >
        </com.zhy.view.RoundImageView>
 
        <com.zhy.view.RoundImageView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_margin="10dp"
            android:src="@drawable/icon" >
        </com.zhy.view.RoundImageView>
 
        <com.zhy.view.RoundImageView
            android:id="@+id/id_meinv"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_margin="10dp"
            android:src="@drawable/aa"
            zhy:borderRadius="20dp"
            zhy:type="round" >
        </com.zhy.view.RoundImageView>
 
        <com.zhy.view.RoundImageView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_margin="10dp"
            android:src="@drawable/icon"
            zhy:borderRadius="40dp"
            zhy:type="round" >
        </com.zhy.view.RoundImageView>
 
        <com.zhy.view.RoundImageView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_margin="10dp"
            android:src="@drawable/qiqiu"
            zhy:borderRadius="60dp"
            zhy:type="round" >
        </com.zhy.view.RoundImageView>
    </LinearLayout>
 
</ScrollView>
```

```java
package com.zhy.variousshapeimageview;
 
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
 
import com.zhy.view.RoundImageView;
 
public class MainActivity extends Activity{
	private RoundImageView mQiQiu;
	private RoundImageView mMeiNv ; 
 
	@Override
	protected void onCreate(Bundle savedInstanceState){
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		
		mQiQiu = (RoundImageView) findViewById(R.id.id_qiqiu);
		mMeiNv = (RoundImageView) findViewById(R.id.id_meinv);
		
		mQiQiu.setOnClickListener(new OnClickListener()	{
			@Override
			public void onClick(View v) {
				mQiQiu.setType(RoundImageView.TYPE_ROUND);
			}
		});
		
		mMeiNv.setOnClickListener(new OnClickListener() {
			
			@Override
			public void onClick(View v)
			{
				mMeiNv.setBorderRadius(90);
			}
		});
	}
 
}
```
好了，到此本篇博客就结束了。大家可以尝试绘制个五边形或者神马的形状；或者加个边框神马的，相信自己修改应该没问题~~代码可能会存在bug和不足之处，欢迎您的指出，共同进步。
最后的效果图：
[![最后的效果图.gif](https://upload-images.jianshu.io/upload_images/9028834-f309343e43bf6ef1.gif?imageMogr2/auto-orient/strip)](https://upload-images.jianshu.io/upload_images/9028834-f309343e43bf6ef1.gif?imageMogr2/auto-orient/strip)

[源码点击下载](http://download.csdn.net/detail/lmj623565791/8271979)



引用：
[Android Xfermode 实战 实现圆形、圆角图片](https://blog.csdn.net/lmj623565791/article/details/42094215)
[Android BitmapShader 实战 实现圆形、圆角图片](https://blog.csdn.net/lmj623565791/article/details/41967509)






