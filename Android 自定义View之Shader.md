<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [【Android 自定义View之Shader】](#android-%E8%87%AA%E5%AE%9A%E4%B9%89view%E4%B9%8Bshader)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->



# 【Android 自定义View之Shader】

[自定义控件其实很简单1/3](https://blog.csdn.net/aigestudio/article/details/41799811)
前几天身子骨出了点小毛病不爽，再加上CSDN抽风也木有更新，现在补上之前漏掉的1/3
上一节结尾的时候我们说到，Paint类中我们还有一个方法没讲
```
setShader(Shader shader)
```
这个方法呢其实也没有什么特别的，那么为什么我们要把它单独分离出来讲那么异类呢？难道它贿赂了我吗？显然不是的，哥视金钱如粪土（我的要求很低，只需要一克反物质即可）！怎么可能做出如此下三滥的事情！之所以要把这货单独拿出来是为了引出Android在图形变换中非常重要的一个类！这个类是什么呢？我也先不说，咱还是先来看看Shader：
![img](https://img-blog.csdn.net/20141208093306483?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
Shader类呢也是个灰常灰常简单的类，它有五个子类，像PathEffect一样每个子类都实现了一种Shader，Shader在三维软件中我们称之为着色器，其作用嘛就像它的名字一样是来给图像着色的或者更通俗的说法是上色！这么说该懂了吧！再不懂去厕所哭去！这五个Shader里最异类的是BitmapShader，因为只有它是允许我们载入一张图片来给图像着色，那我们还是先来看看这个怪胎吧
BitmapShader
只有一个含参的构造方法BitmapShader (Bitmap bitmap, Shader.TileMode tileX, Shader.TileMode tileY)而其他的四个兄弟姐妹呢都有两个！它只有一个蛋，又一魂谈！那好吧，我们来看看它是什么个效果，顺便呢也学习一下Shader的用法先，来看我们熟悉的代码：
```
public class ShaderView extends View {
	private static final int RECT_SIZE = 400;// 矩形尺寸的一半
 
	private Paint mPaint;// 画笔
 
	private int left, top, right, bottom;// 矩形坐上右下坐标
 
	public ShaderView(Context context, AttributeSet attrs) {
		super(context, attrs);
		// 获取屏幕尺寸数据
		int[] screenSize = MeasureUtil.getScreenSize((Activity) context);
 
		// 获取屏幕中点坐标
		int screenX = screenSize[0] / 2;
		int screenY = screenSize[1] / 2;
 
		// 计算矩形左上右下坐标值
		left = screenX - RECT_SIZE;
		top = screenY - RECT_SIZE;
		right = screenX + RECT_SIZE;
		bottom = screenY + RECT_SIZE;
 
		// 实例化画笔
		mPaint = new Paint(Paint.ANTI_ALIAS_FLAG | Paint.DITHER_FLAG);
 
		// 获取位图
		Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.a);
 
		// 设置着色器
		mPaint.setShader(new BitmapShader(bitmap, Shader.TileMode.CLAMP, Shader.TileMode.CLAMP));
	}
 
	@Override
	protected void onDraw(Canvas canvas) {
		// 绘制矩形
		canvas.drawRect(left, top, right, bottom, mPaint);
	}
}
```
如果上面我们没有设置Shader：
```
mPaint.setShader(new BitmapShader(bitmap, Shader.TileMode.CLAMP, Shader.TileMode.CLAMP));
```
那么我们Draw出来的图像一定是一个位于屏幕正中黑色的正方形，但是我们设置了Shader后还是一样的吗？看看效果：
我靠！这什么玩意！罪过罪过！真是看不懂！别急，Shader.TileMode里有三种模式：CLAMP、MIRROR和REPETA，我们看看其他两种模式是什么效果呢：
```
mPaint.setShader(new BitmapShader(bitmap, Shader.TileMode.MIRROR, Shader.TileMode.MIRROR));
```
诶？这效果还能接受，我们还能看得出一点效果，说白了就是上下左右的镜像而已，那再看看REPETA：
```
mPaint.setShader(new BitmapShader(bitmap, Shader.TileMode.REPEAT, Shader.TileMode.REPEAT));
```
这个就更简单了，明显的一个重复效果，而REPEAT也就是重复的意思，同理MIRROR也就是镜像的意思，这个很好理解吧。那第一个CLAMP模式究竟特么的是什么东西呢？看效果根本看不出来，我们不妨换个思维，BitmapShader (Bitmap bitmap, Shader.TileMode tileX, Shader.TileMode tileY)的第一个参数是位图这个很显然，而后两个参数则分别表示XY方向上的着色模式，既然可以分开设置，那么我们是不是可以这样设置一个Shader呢？
```
mPaint.setShader(new BitmapShader(bitmap, Shader.TileMode.CLAMP, Shader.TileMode.MIRROR));
```
也就是说我们在X轴方向上采取CLAMP模式而Y轴方向上采取MIRROR模式，那么这样肯定是可行的撒：
大家可以看到图像分为两部分左边呢Y轴镜像了，而右边像是被拉伸了一样怪怪的！其实CLAMP的意思就是边缘拉伸的意思，比如上图中左边Y轴镜像了，而右边会紧挨着左边将图像边缘上的第一个像素沿X轴复制！产生一种被拉伸的效果！就像扯蛋，不过这里扯的不是蛋而是图像边缘的第一个像素，就是这么简单。但是！作为一个严谨的男人必须要又一个严谨的态度！这时我就会想，如果两种模式互换会怎样呢？比如：
```
mPaint.setShader(new BitmapShader(bitmap, Shader.TileMode.MIRROR, Shader.TileMode.CLAMP));
```
这样来看，应该是X轴会镜像而Y轴会拉伸对吧，看看效果：
这…………好像跟我们想象中的不大一样唉……是我们做错了吗？不是的，结合上一个例子大家有没有注意BitmapShader是先应用了Y轴的模式而X轴是后应用的！所以着色是先在Y轴拉伸了然后再沿着X轴重复对吧（阴笑ing……）？！
要善于发现生活规律！我曾经说过：存在必定合理，那么这么一个玩意有什么用处呢？给大家看一个非常有趣的东西：
![img](https://img-blog.csdn.net/20150113163411109)
这玩意是不是感觉跟以前那种小霸王某个游戏的开场动画很类似？我们就是利用BitmapShader来实现的，而且实现方法也异常简单：
```
public class BrickView extends View {
	private Paint mFillPaint, mStrokePaint;// 填充和描边的画笔
	private BitmapShader mBitmapShader;// Bitmap着色器
 
	private float posX, posY;// 触摸点的XY坐标
 
	public BrickView(Context context, AttributeSet attrs) {
		super(context, attrs);
 
		// 初始化画笔
		initPaint();
	}
 
	/**
	 * 初始化画笔
	 */
	private void initPaint() {
		/*
		 * 实例化描边画笔并设置参数
		 */
		mStrokePaint = new Paint(Paint.ANTI_ALIAS_FLAG | Paint.DITHER_FLAG);
		mStrokePaint.setColor(0xFF000000);
		mStrokePaint.setStyle(Paint.Style.STROKE);
		mStrokePaint.setStrokeWidth(5);
 
		// 实例化填充画笔
		mFillPaint = new Paint();
 
		/*
		 * 生成BitmapShader
		 */
		Bitmap mBitmap = BitmapFactory.decodeResource(getResources(), R.drawable.brick);
		mBitmapShader = new BitmapShader(mBitmap, Shader.TileMode.REPEAT, Shader.TileMode.REPEAT);
		mFillPaint.setShader(mBitmapShader);
	}
 
	@Override
	public boolean onTouchEvent(MotionEvent event) {
		/*
		 * 手指移动时获取触摸点坐标并刷新视图
		 */
		if (event.getAction() == MotionEvent.ACTION_MOVE) {
			posX = event.getX();
			posY = event.getY();
 
			invalidate();
		}
		return true;
	}
 
	@Override
	protected void onDraw(Canvas canvas) {
		// 设置画笔背景色
		canvas.drawColor(Color.DKGRAY);
 
		/*
		 * 绘制圆和描边
		 */
		canvas.drawCircle(posX, posY, 300, mFillPaint);
		canvas.drawCircle(posX, posY, 300, mStrokePaint);
	}
}
```
我只是单纯地加载了一张板砖的贴图然后呢在BitmapShader中XY轴重复就这么简单 = = ，是不是感脚被耍了一样好无聊~~~~是你不动脑而已……囧！来看看另一个Shader叫做
LinearGradient
线性渐变，顾名思义这锤子玩意就是来画渐变的，实际上Shader的五个子类中除了上面我们说的那个怪胎，还有个变形金刚ComposeShader外其余三个都是渐变只是效果不同而已，而这个LinearGradient线性渐变一说大家估计都懂，先来看张效果图：
![img](https://img-blog.csdn.net/20141208094055763?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
是不是秒懂了！恩，说明你头脑简单，这个实现也很简单，具体代码跟上面的BitmapShader一样只是把BitmapShader换成了LinearGradient而已：
```
mPaint.setShader(new LinearGradient(left, top, right, bottom, Color.RED, Color.YELLOW, Shader.TileMode.REPEAT));
```
上面我们提到过除了BitmapShader外其他子类都有两个构造方法，上面我们用到了
```
LinearGradient(float x0, float y0, float x1, float y1, int color0, int color1, Shader.TileMode tile)
```
这是LinearGradient最简单的一个构造方法，参数虽多其实很好理解x0和y0表示渐变的起点坐标而x1和y1则表示渐变的终点坐标，这两点都是相对于屏幕坐标系而言的，而color0和color1则表示起点的颜色和终点的颜色，这些即便是213也能懂 - - ……Shader.TileMode上面我们给的是REPEAT重复但是并没有任何效果，这时因为我们渐变的起点和终点都落在了图形的两端，整个渐变shader已经填充了图形所以不起作用，如果我们改改，把终点坐标变一下：
```
mPaint.setShader(new LinearGradient(left, top, right - RECT_SIZE, bottom - RECT_SIZE, Color.RED, Color.YELLOW, Shader.TileMode.REPEAT));
```
此时我们渐变终点坐标落在了图形的终点上，根据我们的REPEAT模式，会呈现一个渐变重复的效果：
仅仅两种颜色的渐变根本无法满足我们身体的欲望，太单调乏味！我们是不是可以定义多种颜色渐变呢？答案是必须的，LinearGradient的另一个构造方法
```
LinearGradient(float x0, float y0, float x1, float y1, int[] colors, float[] positions, Shader.TileMode tile)
```
就为我们实现了这么一个功能：
```
mPaint.setShader(new LinearGradient(left, top, right, bottom, new int[] { Color.RED, Color.YELLOW, Color.GREEN, Color.CYAN, Color.BLUE }, new float[] { 0, 0.1F, 0.5F, 0.7F, 0.8F }, Shader.TileMode.MIRROR));
```
前面四个参数也是定义坐标的不扯了colors是一个int型数组，我们用来定义所有渐变的颜色，positions表示的是渐变的相对区域，其取值只有0到1，上面的代码中我们定义了一个[0, 0.1F, 0.5F, 0.7F, 0.8F]，意思就是红色到黄色的渐变起点坐标在整个渐变区域（left, top, right, bottom定义了渐变的区域）的起点，而终点则在渐变区域长度 * 10%的地方，而黄色到绿色呢则从渐变区域10%开始到50%的地方以此类推，positions可以为空：
```
mPaint.setShader(new LinearGradient(left, top, right, bottom, new int[] { Color.RED, Color.YELLOW, Color.GREEN, Color.CYAN, Color.BLUE }, null, Shader.TileMode.MIRROR));
```
为空时各种颜色的渐变将会均分整个渐变区域：
实际应用中线性渐变也是一个非常好玩的东西，比如哦我们呢可以通过它和混合模式一起制作我们常见的图片倒影效果：
![img](https://img-blog.csdn.net/20141208094928218?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
效果并不复杂实现也非常简单：
```
public class ReflectView extends View {
	private Bitmap mSrcBitmap, mRefBitmap;// 位图
	private Paint mPaint;// 画笔
	private PorterDuffXfermode mXfermode;// 混合模式
 
	private int x, y;// 位图起点坐标
 
	public ReflectView(Context context, AttributeSet attrs) {
		super(context, attrs);
 
		// 初始化资源
		initRes(context);
	}
 
	/*
	 * 初始化资源
	 */
	private void initRes(Context context) {
		// 获取源图
		mSrcBitmap = BitmapFactory.decodeResource(getResources(), R.drawable.gril);
 
		// 实例化一个矩阵对象
		Matrix matrix = new Matrix();
		matrix.setScale(1F, -1F);
 
		// 生成倒影图
		mRefBitmap = Bitmap.createBitmap(mSrcBitmap, 0, 0, mSrcBitmap.getWidth(), mSrcBitmap.getHeight(), matrix, true);
 
		int screenW = MeasureUtil.getScreenSize((Activity) context)[0];
		int screenH = MeasureUtil.getScreenSize((Activity) context)[1];
 
		x = screenW / 2 - mSrcBitmap.getWidth() / 2;
		y = screenH / 2 - mSrcBitmap.getHeight() / 2;
 
		// ………………………………
		mPaint = new Paint();
		mPaint.setShader(new LinearGradient(x, y + mSrcBitmap.getHeight(), x, y + mSrcBitmap.getHeight() + mSrcBitmap.getHeight() / 4, 0xAA000000, Color.TRANSPARENT, Shader.TileMode.CLAMP));
 
		// ………………………………
		mXfermode = new PorterDuffXfermode(PorterDuff.Mode.DST_IN);
	}
 
	@Override
	protected void onDraw(Canvas canvas) {
		canvas.drawColor(Color.BLACK);
		canvas.drawBitmap(mSrcBitmap, x, y, null);
 
		int sc = canvas.saveLayer(x, y + mSrcBitmap.getHeight(), x + mRefBitmap.getWidth(), y + mSrcBitmap.getHeight() * 2, null, Canvas.ALL_SAVE_FLAG);
 
		canvas.drawBitmap(mRefBitmap, x, y + mSrcBitmap.getHeight(), null);
 
		mPaint.setXfermode(mXfermode);
 
		canvas.drawRect(x, y + mSrcBitmap.getHeight(), x + mRefBitmap.getWidth(), y + mSrcBitmap.getHeight() * 2, mPaint);
 
		mPaint.setXfermode(null);
 
		canvas.restoreToCount(sc);
	}
}
```
SweepGradient
的意思是梯度渐变，也称之为扫描式渐变，因为其效果有点类似雷达的扫描效果，他也有两个构造方法：
```
SweepGradient(float cx, float cy, int color0, int color1)
```
其实都跟LinearGradient差不多的，简直没什么可说的，直接上效果跳过无聊的讲解：
```
mPaint.setShader(new SweepGradient(screenX, screenY, Color.RED, Color.YELLOW));
```
```
SweepGradient(float cx, float cy, int[] colors, float[] positions)
```
类似，不重复浪费口水：
```
mPaint.setShader(new SweepGradient(screenX, screenY, new int[] { Color.GREEN, Color.WHITE, Color.GREEN }, null));
```
RadialGradient
径向渐变，径向渐变说的简单点就是个圆形中心向四周渐变的效果，他也一样有两个构造方法……艾玛！我都要吐了……
```
RadialGradient (float centerX, float centerY, float radius, int centerColor, int edgeColor, Shader.TileMode tileMode)
```
简单，一看就懂，例子劳资都懒得上了，屮！
```
RadialGradient (float centerX, float centerY, float radius, int centerColor, int edgeColor, Shader.TileMode tileMode)
```
同上！还是说点有意思的，来个妹子养养眼：
![img](https://img-blog.csdn.net/20141208095400796?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
是不是很漂亮？不过哥压根不放在眼里，哥女朋友比她更漂亮~~好，我们应用1/6里学到的知识给她校校色，因为这张图片的颜色实在太过鲜艳了不符合小清新的LOMO风格~~~~
```
public class DreamEffectView extends View {
	private Paint mBitmapPaint;// 位图画笔
	private Bitmap mBitmap;// 位图
	private PorterDuffXfermode mXfermode;// 图形混合模式
	private int x, y;// 位图起点坐标
	private int screenW, screenH;// 屏幕宽高
 
	public DreamEffectView(Context context, AttributeSet attrs) {
		super(context, attrs);
 
		// 初始化资源
		initRes(context);
 
		// 初始化画笔
		initPaint();
	}
 
	/**
	 * 初始化资源
	 * 
	 * @param context
	 *            丢你螺母
	 */
	private void initRes(Context context) {
		// 获取位图
		mBitmap = BitmapFactory.decodeResource(context.getResources(), R.drawable.gril);
 
		// 实例化混合模式
		mXfermode = new PorterDuffXfermode(PorterDuff.Mode.SCREEN);
 
		screenW = MeasureUtil.getScreenSize((Activity) context)[0];
		screenH = MeasureUtil.getScreenSize((Activity) context)[1];
 
		x = screenW / 2 - mBitmap.getWidth() / 2;
		y = screenH / 2 - mBitmap.getHeight() / 2;
	}
 
	/**
	 * 初始化画笔
	 */
	private void initPaint() {
		// 实例化画笔
		mBitmapPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
 
		// 去饱和、提亮、色相矫正
		mBitmapPaint.setColorFilter(new ColorMatrixColorFilter(new float[] { 0.8587F, 0.2940F, -0.0927F, 0, 6.79F, 0.0821F, 0.9145F, 0.0634F, 0, 6.79F, 0.2019F, 0.1097F, 0.7483F, 0, 6.79F, 0, 0, 0, 1, 0 }));
	}
 
	@Override
	protected void onDraw(Canvas canvas) {
		canvas.drawColor(Color.BLACK);
 
		// 新建图层
		int sc = canvas.saveLayer(x, y, x + mBitmap.getWidth(), y + mBitmap.getHeight(), null, Canvas.ALL_SAVE_FLAG);
 
		// 绘制混合颜色
		canvas.drawColor(0xcc1c093e);
 
		// 设置混合模式
		mBitmapPaint.setXfermode(mXfermode);
 
		// 绘制位图
		canvas.drawBitmap(mBitmap, x, y, mBitmapPaint);
 
		// 还原混合模式
		mBitmapPaint.setXfermode(null);
 
		// 还原画布
		canvas.restoreToCount(sc);
	}
}
```
这样感觉是不是好很多了呢？
类似的效果在一些相机特效中称之为梦幻，我最近在做的一个开源相机项目也有类似的效果。但是这样的效果好像还凑合，但是整张图片没重点，我们可以模拟单反相机的暗角效果，压暗图片周围的颜色亮度提亮中心，让整张图片的中心突出来！实现方式有很多种，比如1/4我们提到过的BlurMsakFilter向内模糊就可以得到一个类似的效果，但是BlurMsakFilter计算出来的像素太生硬毫无生气，这里我们就使用RadialGradient来模拟一下下赑屃：
```
public class DreamEffectView extends View {
	private Paint mBitmapPaint, mShaderPaint;// 位图画笔和Shader图形的画笔
	private Bitmap mBitmap;// 位图
	private PorterDuffXfermode mXfermode;// 图形混合模式
	private int x, y;// 位图起点坐标
	private int screenW, screenH;// 屏幕宽高
 
	public DreamEffectView(Context context, AttributeSet attrs) {
		super(context, attrs);
 
		// 初始化资源
		initRes(context);
 
		// 初始化画笔
		initPaint();
	}
 
	/**
	 * 初始化资源
	 * 
	 * @param context
	 *            丢你螺母
	 */
	private void initRes(Context context) {
		// 获取位图
		mBitmap = BitmapFactory.decodeResource(context.getResources(), R.drawable.gril);
 
		// 实例化混合模式
		mXfermode = new PorterDuffXfermode(PorterDuff.Mode.SCREEN);
 
		screenW = MeasureUtil.getScreenSize((Activity) context)[0];
		screenH = MeasureUtil.getScreenSize((Activity) context)[1];
 
		x = screenW / 2 - mBitmap.getWidth() / 2;
		y = screenH / 2 - mBitmap.getHeight() / 2;
	}
 
	/**
	 * 初始化画笔
	 */
	private void initPaint() {
		// 实例化画笔
		mBitmapPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
 
		// 去饱和、提亮、色相矫正
		mBitmapPaint.setColorFilter(new ColorMatrixColorFilter(new float[] { 0.8587F, 0.2940F, -0.0927F, 0, 6.79F, 0.0821F, 0.9145F, 0.0634F, 0, 6.79F, 0.2019F, 0.1097F, 0.7483F, 0, 6.79F, 0, 0, 0, 1, 0 }));
 
		// 实例化Shader图形的画笔
		mShaderPaint = new Paint();
 
		// 设置径向渐变，渐变中心当然是图片的中心也是屏幕中心，渐变半径我们直接拿图片的高度但是要稍微小一点
		// 中心颜色为透明而边缘颜色为黑色
		mShaderPaint.setShader(new RadialGradient(screenW / 2, screenH / 2, mBitmap.getHeight() * 7 / 8, Color.TRANSPARENT, Color.BLACK, Shader.TileMode.CLAMP));
	}
 
	@Override
	protected void onDraw(Canvas canvas) {
		canvas.drawColor(Color.BLACK);
 
		// 新建图层
		int sc = canvas.saveLayer(x, y, x + mBitmap.getWidth(), y + mBitmap.getHeight(), null, Canvas.ALL_SAVE_FLAG);
 
		// 绘制混合颜色
		canvas.drawColor(0xcc1c093e);
 
		// 设置混合模式
		mBitmapPaint.setXfermode(mXfermode);
 
		// 绘制位图
		canvas.drawBitmap(mBitmap, x, y, mBitmapPaint);
 
		// 还原混合模式
		mBitmapPaint.setXfermode(null);
 
		// 还原画布
		canvas.restoreToCount(sc);
 
		// 绘制一个跟图片大小一样的矩形
		canvas.drawRect(x, y, x + mBitmap.getWidth(), y + mBitmap.getHeight(), mShaderPaint);
	}
}
```
Look Look~~是不是效果更好了呢？四周的亮度被无情地压了下去，重点直接呈现在中心
所以……大家一定要活用工具~~~~善于组合思考！说起组合，接下来Shader的最后一个子类简直就是组合他妈生的
ComposeShader
就是组合Shader的意思，顾名思义就是两个Shader组合在一起作为一个新Shader……老掉牙的剧情是吧！同样，这锤子玩意也有两个构造方法
```
ComposeShader (Shader shaderA, Shader shaderB, Xfermode mode)
ComposeShader (Shader shaderA, Shader shaderB, PorterDuff.Mode mode)
```
两个都差不多的，只不过一个指定了只能用PorterDuff的混合模式而另一个只要是Xfermode下的混合模式都没问题！上面我们为米女图片加暗角的时候只是单纯地使用了一下径向渐变，但是其实实际获得的效果并不好，因为我们应用的径向渐变是个圆形的，但是我们的图片实际上是个竖向矩形的，直接往上面“盖”一个径向渐变实际效果简而言之应该是这样的：
可见渐变的坡度太平缓了，不符合我们的Style，能不能改一下让它拉伸下变成一个竖向的椭圆形呢？比如下图这样的：
![img](https://img-blog.csdn.net/20141208095709579?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
我们来试试看：
```
public class DreamEffectView extends View {
	private Paint mBitmapPaint, mShaderPaint;// 位图画笔和Shader图形的画笔
	private Bitmap mBitmap, darkCornerBitmap;// 源图的Bitmap和我们自己画的暗角Bitmap
	private PorterDuffXfermode mXfermode;// 图形混合模式
	private int x, y;// 位图起点坐标
	private int screenW, screenH;// 屏幕宽高
 
	public DreamEffectView(Context context, AttributeSet attrs) {
		super(context, attrs);
 
		// 初始化资源
		initRes(context);
 
		// 初始化画笔
		initPaint();
	}
 
	/**
	 * 初始化资源
	 * 
	 * @param context
	 *            丢你螺母
	 */
	private void initRes(Context context) {
		// 获取位图
		mBitmap = BitmapFactory.decodeResource(context.getResources(), R.drawable.gril);
 
		// 实例化混合模式
		mXfermode = new PorterDuffXfermode(PorterDuff.Mode.SCREEN);
 
		screenW = MeasureUtil.getScreenSize((Activity) context)[0];
		screenH = MeasureUtil.getScreenSize((Activity) context)[1];
 
		x = screenW / 2 - mBitmap.getWidth() / 2;
		y = screenH / 2 - mBitmap.getHeight() / 2;
	}
 
	/**
	 * 初始化画笔
	 */
	private void initPaint() {
		// 实例化画笔
		mBitmapPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
 
		// 去饱和、提亮、色相矫正
		mBitmapPaint.setColorFilter(new ColorMatrixColorFilter(new float[] { 0.8587F, 0.2940F, -0.0927F, 0, 6.79F, 0.0821F, 0.9145F, 0.0634F, 0, 6.79F, 0.2019F, 0.1097F, 0.7483F, 0, 6.79F, 0, 0, 0, 1, 0 }));
 
		// 实例化Shader图形的画笔
		mShaderPaint = new Paint();
 
		// 根据我们源图的大小生成暗角Bitmap
		darkCornerBitmap = Bitmap.createBitmap(mBitmap.getWidth(), mBitmap.getHeight(), Bitmap.Config.ARGB_8888);
 
		// 将该暗角Bitmap注入Canvas
		Canvas canvas = new Canvas(darkCornerBitmap);
 
		// 计算径向渐变半径
		float radiu = canvas.getHeight() * (2F / 3F);
 
		// 实例化径向渐变
		RadialGradient radialGradient = new RadialGradient(canvas.getWidth() / 2F, canvas.getHeight() / 2F, radiu, new int[] { 0, 0, 0xAA000000 }, new float[] { 0F, 0.7F, 1.0F }, Shader.TileMode.CLAMP);
 
		// 实例化一个矩阵
		Matrix matrix = new Matrix();
 
		// 设置矩阵的缩放
		matrix.setScale(canvas.getWidth() / (radiu * 2F), 1.0F);
 
		// 设置矩阵的预平移
		matrix.preTranslate(((radiu * 2F) - canvas.getWidth()) / 2F, 0);
 
		// 将该矩阵注入径向渐变
		radialGradient.setLocalMatrix(matrix);
 
		// 设置画笔Shader
		mShaderPaint.setShader(radialGradient);
 
		// 绘制矩形
		canvas.drawRect(0, 0, canvas.getWidth(), canvas.getHeight(), mShaderPaint);
	}
 
	@Override
	protected void onDraw(Canvas canvas) {
		canvas.drawColor(Color.BLACK);
 
		// 新建图层
		int sc = canvas.saveLayer(x, y, x + mBitmap.getWidth(), y + mBitmap.getHeight(), null, Canvas.ALL_SAVE_FLAG);
 
		// 绘制混合颜色
		canvas.drawColor(0xcc1c093e);
 
		// 设置混合模式
		mBitmapPaint.setXfermode(mXfermode);
 
		// 绘制位图
		canvas.drawBitmap(mBitmap, x, y, mBitmapPaint);
 
		// 还原混合模式
		mBitmapPaint.setXfermode(null);
 
		// 还原画布
		canvas.restoreToCount(sc);
 
		// 绘制我们画好的径向渐变图
		canvas.drawBitmap(darkCornerBitmap, x, y, null);
	}
}
```
运行一下可以看到如下结果：
是不是感脚暗角比上面我们做的那个更Perfect？图片中心大部分区域不受任何干扰只把四角边缘处的亮度压了下去，当然这个效果对哥来说也不是很满意，不过先将就凑合着 = = 。上例中我们使用到了两个东西，一个是在独立的Canvas中绘制自己的Bitmap，在哥下一节我们就会详细讲到这里就先不扯了，而另一个则是我们说的重点：Matrix，其实在上面我们做倒影的时候已经简单地使用过了Matrix，那么Matrix究竟是什么呢？其中文直译为矩阵，而我更愿意称之为矩阵变换或者图形变换，它和我们1/6中学到的图形的混合可谓是双簧，同样地重要！
在本文的开头哥给大家挖了一个坑，在讲BitmapShader的时候我们在
```
mPaint.setShader(new BitmapShader(bitmap, Shader.TileMode.CLAMP, Shader.TileMode.CLAMP));
```
的模式下得到了如下这样狗吃屎的效果：
不知道大家有没有质疑过为什么？如果没有，情有可原！但是在我们使用另外两种模式REPEAT和MIRROR的时候难道大家就没想过为什么我们的位图会像那样重复或者镜像吗？是必须那样吗？那个例子中我们使用的是一个边长为800置于屏幕中心的矩形，我们何不尝试将其铺满屏幕看看？
```
public class ShaderView extends View {
	private static final int RECT_SIZE = 400;// 矩形尺寸的一半
 
	private Paint mPaint;// 画笔
 
	private int left, top, right, bottom;// 矩形坐上右下坐标
	private int screenX, screenY;
 
	public ShaderView(Context context, AttributeSet attrs) {
		super(context, attrs);
 
		// 获取屏幕尺寸数据
		int[] screenSize = MeasureUtil.getScreenSize((Activity) context);
 
		// 获取屏幕中点坐标
		screenX = screenSize[0] / 2;
		screenY = screenSize[1] / 2;
 
		// 计算矩形左上右下坐标值
		left = screenX - RECT_SIZE;
		top = screenY - RECT_SIZE;
		right = screenX + RECT_SIZE;
		bottom = screenY + RECT_SIZE;
 
		// 实例化画笔
		mPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
 
		// 获取位图
		Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.a);
 
		// 设置着色器
		mPaint.setShader(new BitmapShader(bitmap, Shader.TileMode.CLAMP, Shader.TileMode.CLAMP));
		// mPaint.setShader(new LinearGradient(left, top, right - RECT_SIZE, bottom - RECT_SIZE, Color.RED, Color.YELLOW, Shader.TileMode.MIRROR));
		// mPaint.setShader(new LinearGradient(left, top, right, bottom, new int[] { Color.RED, Color.YELLOW, Color.GREEN, Color.CYAN, Color.BLUE }, null, Shader.TileMode.MIRROR));
		// mPaint.setShader(new SweepGradient(screenX, screenY, Color.RED, Color.YELLOW));
		// mPaint.setShader(new SweepGradient(screenX, screenY, new int[] { Color.GREEN, Color.WHITE, Color.GREEN }, null));
	}
 
	@Override
	protected void onDraw(Canvas canvas) {
		// 绘制矩形
		// canvas.drawRect(left, top, right, bottom, mPaint);
		canvas.drawRect(0, 0, screenX * 2, screenY * 2, mPaint);
	}
}
```
看看效果：
是不是忽然明白了什么？好了，你可以从坑里爬出来了…………这样看是不是懂了？BitmapShader是从画布的左上方开始着色，回到我们刚才的问题，这个着色方式必须是这样的么？显然不是！在Shader类中有一对setter和getter方法：setLocalMatrix(Matrix localM)和getLocalMatrix(Matrix localM)我们可以利用它们来设置或获取Shader的变换矩阵，比如上面的例子我还是绘制成一个边长为800的矩形：
```
public class ShaderView extends View {
	private static final int RECT_SIZE = 400;// 矩形尺寸的一半
 
	private Paint mPaint;// 画笔
 
	private int left, top, right, bottom;// 矩形坐上右下坐标
	private int screenX, screenY;
 
	public ShaderView(Context context, AttributeSet attrs) {
		super(context, attrs);
 
		// 获取屏幕尺寸数据
		int[] screenSize = MeasureUtil.getScreenSize((Activity) context);
 
		// 获取屏幕中点坐标
		screenX = screenSize[0] / 2;
		screenY = screenSize[1] / 2;
 
		// 计算矩形左上右下坐标值
		left = screenX - RECT_SIZE;
		top = screenY - RECT_SIZE;
		right = screenX + RECT_SIZE;
		bottom = screenY + RECT_SIZE;
 
		// 实例化画笔
		mPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
 
		// 获取位图
		Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.a);
 
		// 实例化一个Shader
		BitmapShader bitmapShader = new BitmapShader(bitmap, Shader.TileMode.CLAMP, Shader.TileMode.CLAMP);
 
		// 实例一个矩阵对象
		Matrix matrix = new Matrix();
 
		// 设置矩阵变换
		matrix.setTranslate(left, top);
 
		// 设置Shader的变换矩阵
		bitmapShader.setLocalMatrix(matrix);
 
		// 设置着色器
		mPaint.setShader(bitmapShader);
		// mPaint.setShader(new LinearGradient(left, top, right - RECT_SIZE, bottom - RECT_SIZE, Color.RED, Color.YELLOW, Shader.TileMode.MIRROR));
		// mPaint.setShader(new LinearGradient(left, top, right, bottom, new int[] { Color.RED, Color.YELLOW, Color.GREEN, Color.CYAN, Color.BLUE }, null, Shader.TileMode.MIRROR));
		// mPaint.setShader(new SweepGradient(screenX, screenY, Color.RED, Color.YELLOW));
		// mPaint.setShader(new SweepGradient(screenX, screenY, new int[] { Color.GREEN, Color.WHITE, Color.GREEN }, null));
	}
 
	@Override
	protected void onDraw(Canvas canvas) {
		// 绘制矩形
		canvas.drawRect(left, top, right, bottom, mPaint);
		// canvas.drawRect(0, 0, screenX * 2, screenY * 2, mPaint);
	}
}
```
不一样的是我在给画笔设置着色器前为我们的着色器设置了一个变换矩阵，让我们的Shader依据自身的坐标→平移left个单位↓平移top个单位，也就是说原本shader的原点应该是画布（注意不是屏幕！这里只是刚好画布更屏幕重合了而已！切记！）的左上方[0,0]的位置，通过变换移至了[left,top]的位置，如果没问题，Shader此时应该是刚好是从我们矩形的左上方开始着色：
Is anyone has question yet？是不是感觉有点儿懂Matrix了？Really？
其实说了半天我们依然没进入到Matrix的本质，在1/6中我们学习了一个和它比较类似的玩意叫做ColorMatrix大家还记得否？我在讲ColorMatrix的时候说过其是一个4x5的颜色矩阵，而同样，我们的Matrix也是一个矩阵，只不过不是4*5而是3*3的位置坐标矩阵：
![img](https://img-blog.csdn.net/20141208100700851?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
变换变换，既然说到变换那么必定涉及最基本的旋转啊、缩放啊、平移之类，而在Matrix中除了该三者还多了一种:错切，什么叫错切呢？所谓错切数学中也称之为剪切变换，原理呢就是将图形上所有点的X/Y坐标保持不变而按比例平移Y/X坐标，并且平移的大小和某点到X/Y轴的垂直距离成正比，变换前后图形的面积不变。其实对于Matrix可以这样说：图形的变换实质上就是图形上点的变换，而我们的Matrix的计算也正是基于此，比如点P(x0,y0)经过上面的矩阵变换后会去到P(x,y)的位置：
![img](https://img-blog.csdn.net/20141208100726234?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
注：除了平移外，缩放、旋转、错切、透视都是需要一个中心点作为参照的，如果没有平移，默认为图形的[0,0]点，平移只需要指定移动的距离即可，平移操作会改变中心点的位置！非常重要！记牢了！
PS：唉……说实话矩阵的计算原理真心不想写……不过算是个小总结吧，爱看看不爱看直接跳过即可……
有一点需要注意的是，矩阵的乘法运算是不符合交换律的，因此矩阵B*A和A*B是两个截然不同的结果，前者表示A右乘B，是列变换；后者表示A左乘B，是行变换。如果有心的童鞋会发现Matrix类中会有三大类方法：setXXX、preXXX和postXXX，而preXXX和postXXX就是分别表示矩阵的左右乘，也有前后乘的说法，对于不懂矩阵的人来说都一样 = = ……但是要注意一点！！！大家在理解Matrix的时候要把它想象成一个容器，什么容器呢？存放我们变换信息的容器，Matrix的所有方法都是针对其自身的！！！！当我们把所有的变换操作做完后再“一次性地”把它注入我们想要的地方，比如上面我们为shader注入了一个Matrix。还有一点要注意，一定要注意：ColorMatrix和Matrix在应用给其他对象时都是左乘的，而其自身内部是可以左右乘的！千万别搞混了！UnderStand？一定要理解这一点，不然我只能哭晕在厕所了压根没法讲下去你也不会听的懂……
上图的公式中，GHI都表示的是透视参数，一般情况下我们不会去处理，三维的透视我更乐意使用Camare，所以很多时候G和H的值都为0而I的值恒为1，至于为什么如果有时间待会会说。
所有的Matrix变换中最好理解的其实是缩放变换，因为缩放的本质其实就是图形上所有的点X/Y轴沿着中心点放大一定的倍数，比如：
![img](https://img-blog.csdn.net/20141208100841160?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
这么一个矩阵变换实质就是x = x0 * a、y = y0 * b，难度系数：0
![img](https://img-blog.csdn.net/20141208100904575?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
X/Y轴分别放大a\b倍
相对来说平移稍难但是也好理解：
![img](https://img-blog.csdn.net/20141208100931033?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
同理x = x0 + a、y = y0 + b，难度系数：0
![img](https://img-blog.csdn.net/20141208100958578?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
X/Y轴分别平移！￥￥%#%……%……#￥%￥%
旋转就很复杂了……分为两种：一种是直接绕默认中点[0,0]旋转，另一种是指定中点，也就是将中点[0,0]平移后在旋转：
直接绕[0,0]顺时针转：
![img](https://img-blog.csdn.net/20141208101031171?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
唉、这个先看图吧：
![img](https://img-blog.csdn.net/20141208101048343?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
根据三角函数的关系我们可以得出p(x,y)的坐标：
![img](https://img-blog.csdn.net/20141208101108546?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
同样根据三角函数的关系我们也可以得出p(x0,y0)的坐标：
![img](https://img-blog.csdn.net/20141208101132984?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
上述两公式结合我们则可以得出简化后的p(x,y)的坐标：
![img](https://img-blog.csdn.net/20141208101155941?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
这是什么公式呢？是不是就是上面矩阵的乘积呢？囧……
绕点p(a,b)顺时针转：
其实绕某个点旋转没有想象中的那么复杂，相对于绕中心点来说就多了两步：先将坐标原点移到我们的p(a,b)处然后执行旋转最后再把坐标圆点移回去：
![img](https://img-blog.csdn.net/20141208101231291?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
对了……开头忘说了……矩阵的乘法是从右边开始的，额，其实也只有上面这算式才有多个矩阵相乘 =  = 冏，也就是说最右边的两个会先乘，大家看看最右边的两个乘积是什么……是不是就是我们把原点移动到P(a,b)后[x0,y0]的新坐标啊？然后继续往左乘，旋转一定得角度这跟上面[0,0]旋转是一样的，最后往左乘把坐标还原
![img](https://img-blog.csdn.net/20141208101253537?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
不扯了，你听得懂甚好！不懂没关系！真的没关系！哥不骗你！哥从不骗妹子……
哎……错切我也先不说了，大家听了这么多槽点是不是头都大了？麻痹的做个变换还这么麻烦劳资TM还不如不做！是的，这样去做变换真心太麻烦！要是开发Android这么麻烦的话特么谁还玩？所以这些复杂繁琐的扑街玩意Android早就为我们封装好了……压根就不需要我们去管那么多！上面我们曾提到的setXXX、preXXX和postXXX方法就是Android为我们封装好的针对不同运算的“档位”，那么怎么用呢？非常非常简单，你压根可以现在忘记上面我们说到的各种计算原理，比如，我这里还是拿BitmapShader来说吧，我们想要移动Shader的位置：
```
public class MatrixView extends View {
	private static final int RECT_SIZE = 400;// 矩形尺寸的一半
 
	private Paint mPaint;// 画笔
 
	private int left, top, right, bottom;// 矩形坐上右下坐标
	private int screenX, screenY;
 
	public MatrixView(Context context, AttributeSet attrs) {
		super(context, attrs);
 
		// 获取屏幕尺寸数据
		int[] screenSize = MeasureUtil.getScreenSize((Activity) context);
 
		// 获取屏幕中点坐标
		screenX = screenSize[0] / 2;
		screenY = screenSize[1] / 2;
 
		// 计算矩形左上右下坐标值
		left = screenX - RECT_SIZE;
		top = screenY - RECT_SIZE;
		right = screenX + RECT_SIZE;
		bottom = screenY + RECT_SIZE;
 
		// 实例化画笔
		mPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
 
		// 获取位图
		Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.a);
 
		// 实例化一个Shader
		BitmapShader bitmapShader = new BitmapShader(bitmap, Shader.TileMode.CLAMP, Shader.TileMode.CLAMP);
 
		// 实例一个矩阵对象
		Matrix matrix = new Matrix();
 
		// 设置矩阵变换
		matrix.setTranslate(500, 500);
 
		// 设置Shader的变换矩阵
		bitmapShader.setLocalMatrix(matrix);
 
		// 设置着色器
		mPaint.setShader(bitmapShader);
	}
 
	@Override
	protected void onDraw(Canvas canvas) {
		// 绘制矩形
		// canvas.drawRect(left, top, right, bottom, mPaint);
		canvas.drawRect(0, 0, screenX * 2, screenY * 2, mPaint);
	}
}
```
这段代码其实跟前面的有点类似，不纠结，我们只是简单地在Matrix中做了个平移：
效果也很简单，那我们再来个旋转5度？
```
// 设置矩阵变换
matrix.setTranslate(500, 500);
matrix.setRotate(5);
```
完事后看看效果尼玛怎么是介个样子？说好的平移呢？被狗吃了？
![img](https://img-blog.csdn.net/20141208101437152?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
Why？其实是这样的，在我们new了一个Matrix对象后，这个Matrix对象中已经就为我们封装了一组原始数据：
```
float[]{
		1, 0, 0
		0, 1, 0
		0, 0, 1
}
```
而我们的setXXX方法执行的操作是把原本Matrix对象中的数据重置，重新设置新的数据，比如：
```
matrix.setTranslate(500, 500);
```
后数据即变为：
```
float[]{
		1, 0, 500
		0, 1, 500
		0, 0, 1
}
```
而如果再旋转了呢？比如我们上面的：
```
matrix.setTranslate(500, 500);
matrix.setRotate(5);
```
那旋转的数据就会直接覆盖掉我们平移的数据：
```
float[]{
		cos, sin, 0
		sin, cos, 0
		0, 0, 1
}
```
具体参数值我也就不计算了，从这里大家也可以看出Android给我们封装的方法是多么的体贴到位~~~你只需要setRotate个角度即可压根就不需要你关心如何去算的对吧？我们来看另外的两个方法preXXX和postXXX，这里我把setRotate换成preRotate：
```
matrix.setTranslate(500, 500);
matrix.preRotate(5);
```
和
```
matrix.setTranslate(500, 500);
matrix.postRotate(5);
```
好像没啥区别啊？看不出有什么特别的对吧？其实呢这是一个谁先谁后的问题，preXXX和postXXX我们之前说过一个是前乘一个是后乘，那么具体表现是什么样的呢？，非常简单，比如我有如下代码
```
matrix.preScale(0.5f, 1); 
matrix.setScale(1, 0.6f); 
matrix.postScale(0.7f, 1); 
matrix.preTranslate(15, 0);
```
那么Matrix的计算过程即为：translate (15, 0) -> scale (1, 0.6f) -> scale (0.7f, 1)，我们说过set会重置数据，所以最开始的
```
matrix.preScale(0.5f, 1);
```
也就GG了
同样地，对于类似的变换：
```
matrix.preScale(0.5f, 1); 
matrix.preTranslate(10, 0);
matrix.postScale(0.7f, 1);  
matrix.postTranslate(15, 0);
```
其计算过程为：translate (10, 0) -> scale (0.5f, 1) -> scale (0.7f, 1) -> translate (15, 0)，是不是很简单呢？你一定不傻逼对吧！
那么对于上图的结果真的是一样的吗？这里我教给大家一个方法自己去验证，Matrix有一个getValues方法可以获取当前Matrix的变换浮点数组，也就是我们之前说的矩阵：
```
/*
 * 新建一个9个单位长度的浮点数组
 * 因为我们的Matrix矩阵是9个单位长的对吧
 */
float[] fs = new float[9];
 
// 将从matrix中获取到的浮点数组装载进我们的fs里
matrix.getValues(fs);
Log.d("Aige", Arrays.toString(fs));// 输出看看呗！
```
大家觉得好奇的都可以去验证，这三类方法我就不多说了，Matrix中还有其他很多实用的方法，以后我们用到的时候在讲，因为Matrix太常用了~~~~
现在，大家回过头去再看看我给妹子图加暗角的那段代码，里面关于Matrix的操作能大致看懂了么：
```
// 计算径向渐变半径
float radiu = canvas.getHeight() * (2F / 3F);
 
// 实例化径向渐变
RadialGradient radialGradient = new RadialGradient(canvas.getWidth() / 2F, canvas.getHeight() / 2F, radiu, new int[] { 0, 0, 0xAA000000 }, new float[] { 0F, 0.7F, 1.0F }, Shader.TileMode.CLAMP);
 
// 实例化一个矩阵
Matrix matrix = new Matrix();
 
// 设置矩阵的缩放
matrix.setScale(canvas.getWidth() / (radiu * 2F), 1.0F);
 
// 设置矩阵的预平移
matrix.preTranslate(((radiu * 2F) - canvas.getWidth()) / 2F, 0);
 
// 将该矩阵注入径向渐变
radialGradient.setLocalMatrix(matrix);
 
// 设置画笔Shader
mShaderPaint.setShader(radialGradient);
```
是不是灰常滴简单呢？这里其实你只要注意我们除了平移所有变换操作都是基于一个原点的即可！找对原点你就成功一大半了！
好了，对Matrix的一个简单介绍就到这里，正如我所说，Matrix的应用是相当广泛的，不仅仅是在我们的Shader，我们的canvas也有setMatrix(matrix)方法来设置矩阵变换，更常见的是在ImageView中对ImageView进行变换，当我们手指在屏幕上划过一定的距离后根据这段距离来平移我们的控件，根据两根手指之间拉伸的距离和相对于上一次旋转的角度来缩放旋转我们的图片：
![img](https://img-blog.csdn.net/20150113163500765)
```
public class MatrixImageView extends ImageView {
	private static final int MODE_NONE = 0x00123;// 默认的触摸模式
	private static final int MODE_DRAG = 0x00321;// 拖拽模式
	private static final int MODE_ZOOM = 0x00132;// 缩放or旋转模式
 
	private int mode;// 当前的触摸模式
 
	private float preMove = 1F;// 上一次手指移动的距离
	private float saveRotate = 0F;// 保存了的角度值
	private float rotate = 0F;// 旋转的角度
 
	private float[] preEventCoor;// 上一次各触摸点的坐标集合
 
	private PointF start, mid;// 起点、中点对象
	private Matrix currentMatrix, savedMatrix;// 当前和保存了的Matrix对象
	private Context mContext;// Fuck……
 
	public MatrixImageView(Context context, AttributeSet attrs) {
		super(context, attrs);
		this.mContext = context;
 
		// 初始化
		init();
	}
 
	/**
	 * 初始化
	 */
	private void init() {
		/*
		 * 实例化对象
		 */
		currentMatrix = new Matrix();
		savedMatrix = new Matrix();
		start = new PointF();
		mid = new PointF();
 
		// 模式初始化
		mode = MODE_NONE;
 
		/*
		 * 设置图片资源
		 */
		Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.mylove);
		bitmap = Bitmap.createScaledBitmap(bitmap, MeasureUtil.getScreenSize((Activity) mContext)[0], MeasureUtil.getScreenSize((Activity) mContext)[1], true);
		setImageBitmap(bitmap);
	}
 
	@Override
	public boolean onTouchEvent(MotionEvent event) {
		switch (event.getAction() & MotionEvent.ACTION_MASK) {
		case MotionEvent.ACTION_DOWN:// 单点接触屏幕时
			savedMatrix.set(currentMatrix);
			start.set(event.getX(), event.getY());
			mode = MODE_DRAG;
			preEventCoor = null;
			break;
		case MotionEvent.ACTION_POINTER_DOWN:// 第二个点接触屏幕时
			preMove = calSpacing(event);
			if (preMove > 10F) {
				savedMatrix.set(currentMatrix);
				calMidPoint(mid, event);
				mode = MODE_ZOOM;
			}
			preEventCoor = new float[4];
			preEventCoor[0] = event.getX(0);
			preEventCoor[1] = event.getX(1);
			preEventCoor[2] = event.getY(0);
			preEventCoor[3] = event.getY(1);
			saveRotate = calRotation(event);
			break;
		case MotionEvent.ACTION_UP:// 单点离开屏幕时
		case MotionEvent.ACTION_POINTER_UP:// 第二个点离开屏幕时
			mode = MODE_NONE;
			preEventCoor = null;
			break;
		case MotionEvent.ACTION_MOVE:// 触摸点移动时
			/*
			 * 单点触控拖拽平移
			 */
			if (mode == MODE_DRAG) {
				currentMatrix.set(savedMatrix);
				float dx = event.getX() - start.x;
				float dy = event.getY() - start.y;
				currentMatrix.postTranslate(dx, dy);
			}
			/*
			 * 两点触控拖放旋转
			 */
			else if (mode == MODE_ZOOM && event.getPointerCount() == 2) {
				float currentMove = calSpacing(event);
				currentMatrix.set(savedMatrix);
				/*
				 * 指尖移动距离大于10F缩放
				 */
				if (currentMove > 10F) {
					float scale = currentMove / preMove;
					currentMatrix.postScale(scale, scale, mid.x, mid.y);
				}
				/*
				 * 保持两点时旋转
				 */
				if (preEventCoor != null) {
					rotate = calRotation(event);
					float r = rotate - saveRotate;
					currentMatrix.postRotate(r, getMeasuredWidth() / 2, getMeasuredHeight() / 2);
				}
			}
			break;
		}
 
		setImageMatrix(currentMatrix);
		return true;
	}
 
	/**
	 * 计算两个触摸点间的距离
	 */
	private float calSpacing(MotionEvent event) {
		float x = event.getX(0) - event.getX(1);
		float y = event.getY(0) - event.getY(1);
		return (float) Math.sqrt(x * x + y * y);
	}
 
	/**
	 * 计算两个触摸点的中点坐标
	 */
	private void calMidPoint(PointF point, MotionEvent event) {
		float x = event.getX(0) + event.getX(1);
		float y = event.getY(0) + event.getY(1);
		point.set(x / 2, y / 2);
	}
 
	/**
	 * 计算旋转角度
	 * 
	 * @param 事件对象
	 * @return 角度值
	 */
	private float calRotation(MotionEvent event) {
		double deltaX = (event.getX(0) - event.getX(1));
		double deltaY = (event.getY(0) - event.getY(1));
		double radius = Math.atan2(deltaY, deltaX);
		return (float) Math.toDegrees(radius);
	}
}
```
记得在xml中设置我们MatrixImageView的scaleType="matrix"：
```
<com.aigestudio.customviewdemo.views.MatrixImageView
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:scaleType="matrix" />
```
虽然我们通过Matrix简单地实现了对ImageView的变换操作，但是有一些小BUG，比如我们两指缩放/旋转图片后抬起一只手指，此时应该立即切换回平移模式对吧？但是我们上述的代码中却是终止了各种操作，事件机制虽然我们还没讲，但是我们也在这几节中用到了不少，这个简单的问题你能解决么？
我在之前讲到大家可以使用Matrix的getValues(float[])方法去验证自己不确定的东西，同时呢，我们也可以使用Matrix的setValues(float[])方法来直接给Matrix设置一个矩阵数组，like：
```
setValues(new float[]{
		1, 0, 57
		0, 1, 78
		0, 0, 1
});
```
效果跟
```
matrix.setTranslate(57, 78);
```
是一样的。上面我们说到Matrix矩阵最后的3个数是用来设置透视变换的，为什么最后一个值恒为1？因为其表示的是在Z轴向的透视缩放，这三个值都可以被设置，前两个值跟右手坐标系的XY轴有关，大家可以尝试去发现它们之间的规律，我就不多说了。这里多扯一点，大家一定要学会如何透过现象看本质，即便看到的本质不一定就是实质，但是离实质已经不远了，不要一来就去追求什么底层源码啊、逻辑什么的，就像上面的矩阵变换一样，矩阵的9个数作用其实很多人都说不清，与其听别人胡扯还不如自己动手试试你说是吧，不然苦逼的只是你自己。
在实际应用中我们极少会使用到Matrix的尾三数做透视变换，更多的是使用Camare摄像机，比如我们使用Camare让ListView看起来像倒下去一样：（这里只做了解，已超出我们本系列的范畴）
```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFFFFF"
    android:gravity="center"
    android:orientation="vertical" >
 
    <com.aigestudio.customviewdemo.views.AnimListView
        android:id="@+id/main_alv"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scrollbars="none" />
 
</LinearLayout>
```
自定义ListView重写onDraw：
```
public class AnimListView extends ListView {
	private Camera mCamera;
	private Matrix mMatrix;
 
	public AnimListView(Context context, AttributeSet attrs) {
		super(context, attrs);
		mCamera = new Camera();
		mMatrix = new Matrix();
	}
 
	@Override
	protected void onDraw(Canvas canvas) {
		mCamera.save();
		mCamera.rotate(30, 0, 0);
		mCamera.getMatrix(mMatrix);
		mMatrix.preTranslate(-getWidth() / 2, -getHeight() / 2);
		mMatrix.postTranslate(getWidth() / 2, getHeight() / 2);
		canvas.concat(mMatrix);
		super.onDraw(canvas);
		mCamera.restore();
	}
}
```
在MainActivity中设置数据：
```
public class MainActivity extends Activity {
 
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
 
		AnimListView animListView = (AnimListView) findViewById(R.id.main_alv);
		animListView.setAdapter(new BaseAdapter() {
 
			@Override
			public View getView(int position, View convertView, ViewGroup parent) {
				convertView = LayoutInflater.from(getApplicationContext()).inflate(R.layout.item, null);
				return convertView;
			}
 
			@Override
			public long getItemId(int position) {
				return 0;
			}
 
			@Override
			public Object getItem(int position) {
				return null;
			}
 
			@Override
			public int getCount() {
				return 100;
			}
		});
	}
}
```
效果like below：
好了，Paint所有的方法已经Over了~~~大家是不是觉得终于解放的赶脚呢？别急……还有个Canvas……光学会了如何用笔而不知道画什么有何用呢？不过别急，Canvas我们下一节再细讲，这一节我们算是对Paint来个总结，注意不是了断哦！了断可不行，上一节末尾我留了几张图说要在这一节给大家说怎么在View中画，这一节呢我们来实现它，注意哦，不要指望在这里你会得到一个完美的控件拿去用……我们还没学怎么去测绘View也还没有讲到ViewGroup，So~~~~在这我们只是单纯地先画。
几个图中最难的大概就是那个各种圈圈的了：
![img](https://img-blog.csdn.net/20141208102454710?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
其他的三个图表千篇一律……会画上面那玩意几个图标简直就是小case，这个圈圈图也是我在群里搜刮的，看不出来是吧，没事，我临摹了一个差不多的：
![img](https://img-blog.csdn.net/20141208102522828?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
这样看总清晰了吧……大家在自定义一个View的时候不要老想着自己是个Coder，你老是这样想越做不出棒的View，你要把自己看成一个Designer，这个View将会是由你创造的！而不是你敲出了它……本来在这一系列之后我有单独的番外篇叫如何去设计一个控件，今天在做这个圈圈图的时候突然有这么一个想法还是干脆穿插进来说算了，直接粗暴！
既然我们是一个设计者，那么我们必然要有这么一张图纸去概述我们的View，因为一般情况下大家都知道美工跟开发者之间是有代沟的，假如，我是说假如美工花了一个很屌的界面，但是你如果直接按着他给你的设计图照着做你会发现做不出一模一样的来……这时你就该调整自己多去与美工沟通在不大改设计图的前提下把难度降到你能力范围内。同样的，我们这里也一样。假如这个圈圈图是JB美工给你的效果图，叫你照着做，我们不可能真的这样照着做，因为他给的东西对我们来说无规律可循，而对于开发者来说，有规律的东西往往是最简单，这时我们就要“设计和代码相结合”：
![img](https://img-blog.csdn.net/20141208102556515?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
大家看到我在刚才那张临摹图上做了一些改动，从这张草图上来看改动其实并不大，只是把一些位置啊、大小啊什么的做了一些调整，要记住一点，我们的控件要做到在任何屏幕设备上都能完美使用！而不是像布局、资源图之类的还要做好几套，那就是扯蛋！所以我们这的所有尺寸都是以控件的边长S作为参考依据的：
![img](https://img-blog.csdn.net/20141208102623927?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
注：因为我们还没讲如何测绘控件，所以我们在自定义View的时候强制控件的长宽一致以简化不必要的口水
还是老套路，先分析一下：控件中心往下是最中心的圆，而其他的六个圆都直接或间接地与其相连，上面的三个是大圆而下面的三个是相对较小的圆，大圆之间的线段是紧挨着的而中心大圆和下面三个小圆之间的线段是有一定距离的，右上方的大圆上方还有一段实体描边弧，弧上有文字，而每个圆内都可以设置文本，大概就是酱紫，那么我们从哪作为插入点呢？当然是中心的那个圆，但是我们知道它的圆心是要往控件中心向下偏移一个半径的：
![img](https://img-blog.csdn.net/20141208102651773?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
代码如何实现呢？不用我说你也应该知道：
```
public class MultiCricleView extends View {
	private static final float STROKE_WIDTH = 1F / 256F, // 描边宽度占比
			LINE_LENGTH = 3F / 32F, // 线段长度占比
			CRICLE_LARGER_RADIU = 3F / 32F,// 大圆半径
			CRICLE_SMALL_RADIU = 5F / 64F,// 小圆半径
			ARC_RADIU = 1F / 8F,// 弧半径
			ARC_TEXT_RADIU = 5F / 32F;// 弧围绕文字半径
 
	private Paint strokePaint;// 描边画笔
 
	private int size;// 控件边长
 
	private float strokeWidth;// 描边宽度
	private float ccX, ccY;// 中心圆圆心坐标
	private float largeCricleRadiu;// 大圆半径
 
	public MultiCricleView(Context context, AttributeSet attrs) {
		super(context, attrs);
 
		// 初始化画笔
		initPaint(context);
	}
 
	/**
	 * 初始化画笔
	 * 
	 * @param context
	 *            Fuck
	 */
	private void initPaint(Context context) {
		/*
		 * 初始化描边画笔
		 */
		strokePaint = new Paint(Paint.ANTI_ALIAS_FLAG | Paint.DITHER_FLAG);
		strokePaint.setStyle(Paint.Style.STROKE);
		strokePaint.setColor(Color.WHITE);
		strokePaint.setStrokeCap(Paint.Cap.ROUND);
	}
 
	@Override
	protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
		// 强制长宽一致
		super.onMeasure(widthMeasureSpec, widthMeasureSpec);
	}
 
	@Override
	protected void onSizeChanged(int w, int h, int oldw, int oldh) {
		// 获取控件边长
		size = w;
 
		// 参数计算
		calculation();
	}
 
	/*
	 * 参数计算
	 */
	private void calculation() {
		// 计算描边宽度
		strokeWidth = STROKE_WIDTH * size;
 
		// 计算大圆半径
		largeCricleRadiu = size * CRICLE_LARGER_RADIU;
 
		// 计算中心圆圆心坐标
		ccX = size / 2;
		ccY = size / 2 + size * CRICLE_LARGER_RADIU;
 
		// 设置参数
		setPara();
	}
 
	/**
	 * 设置参数
	 */
	private void setPara() {
		// 设置描边宽度
		strokePaint.setStrokeWidth(strokeWidth);
	}
 
	@Override
	protected void onDraw(Canvas canvas) {
		// 绘制背景
		canvas.drawColor(0xFFF29B76);
 
		// 绘制中心圆
		canvas.drawCircle(ccX, ccY, largeCricleRadiu, strokePaint);
	}
}
```
大家可以看到我在View重写了这了一个方法：
```
@Override
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
	// 强制长宽一致
	super.onMeasure(widthMeasureSpec, widthMeasureSpec);
}
```
这个方法就是用来测量控件宽高的，而其从爹那获取的两个参数widthMeasureSpec和heightMeasureSpec分别封装了View的Size和Mode，我们会在1/2讲View的绘制流程，这里只需跟着我的脚步在这光滑的地板上摩擦摩擦即可 = = ！
onSizeChanged这个方法我们前面也提过就不多扯了，效果如下：
![img](https://img-blog.csdn.net/20141208102806903?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
然后下一步该如何做呢？画哪个呢？再从左上开始吧……，好我们计算坐标：
![img](https://img-blog.csdn.net/20141208102827245?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
没错对吧，绿色的点就是我们要计算的坐标。但是这样去算太TM复杂了！毫无违和感！我们细心观察，左上边那一节不就是等同于：
![img](https://img-blog.csdn.net/20141208102846730?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
这样的一个变换吗？之前我们曾多次使用到画布的图层，如果我们能这样画图形再以中心圆的圆心坐标为旋转点向右旋转画布岂不是可以很简单：
```
public class MultiCricleView extends View {
	private static final float STROKE_WIDTH = 1F / 256F, // 描边宽度占比
			LINE_LENGTH = 3F / 32F, // 线段长度占比
			CRICLE_LARGER_RADIU = 3F / 32F,// 大圆半径
			CRICLE_SMALL_RADIU = 5F / 64F,// 小圆半径
			ARC_RADIU = 1F / 8F,// 弧半径
			ARC_TEXT_RADIU = 5F / 32F;// 弧围绕文字半径
 
	private Paint strokePaint;// 描边画笔
 
	private int size;// 控件边长
 
	private float strokeWidth;// 描边宽度
	private float ccX, ccY;// 中心圆圆心坐标
	private float largeCricleRadiu;// 大圆半径
	private float lineLength;// 线段长度
 
	public MultiCricleView(Context context, AttributeSet attrs) {
		super(context, attrs);
 
		// 初始化画笔
		initPaint(context);
	}
 
	/**
	 * 初始化画笔
	 * 
	 * @param context
	 *            Fuck
	 */
	private void initPaint(Context context) {
		/*
		 * 初始化描边画笔
		 */
		strokePaint = new Paint(Paint.ANTI_ALIAS_FLAG | Paint.DITHER_FLAG);
		strokePaint.setStyle(Paint.Style.STROKE);
		strokePaint.setColor(Color.WHITE);
		strokePaint.setStrokeCap(Paint.Cap.ROUND);
	}
 
	@Override
	protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
		// 强制长宽一致
		super.onMeasure(widthMeasureSpec, widthMeasureSpec);
	}
 
	@Override
	protected void onSizeChanged(int w, int h, int oldw, int oldh) {
		// 获取控件边长
		size = w;
 
		// 参数计算
		calculation();
	}
 
	/*
	 * 参数计算
	 */
	private void calculation() {
		// 计算描边宽度
		strokeWidth = STROKE_WIDTH * size;
 
		// 计算大圆半径
		largeCricleRadiu = size * CRICLE_LARGER_RADIU;
 
		// 计算线段长度
		lineLength = size * LINE_LENGTH;
 
		// 计算中心圆圆心坐标
		ccX = size / 2;
		ccY = size / 2 + size * CRICLE_LARGER_RADIU;
 
		// 设置参数
		setPara();
	}
 
	/**
	 * 设置参数
	 */
	private void setPara() {
		// 设置描边宽度
		strokePaint.setStrokeWidth(strokeWidth);
	}
 
	@Override
	protected void onDraw(Canvas canvas) {
		// 绘制背景
		canvas.drawColor(0xFFF29B76);
 
		// 绘制中心圆
		canvas.drawCircle(ccX, ccY, largeCricleRadiu, strokePaint);
 
		// 绘制左上方图形
		drawTopLeft(canvas);
	}
 
	/**
	 * 绘制左上方图形
	 * 
	 * @param canvas
	 */
	private void drawTopLeft(Canvas canvas) {
		// 锁定画布
		canvas.save();
 
		// 平移和旋转画布
		canvas.translate(ccX, ccY);
		canvas.rotate(-30);
 
		// 依次画：线-圈-线-圈
		canvas.drawLine(0, -largeCricleRadiu, 0, -lineLength * 2, strokePaint);
		canvas.drawCircle(0, -lineLength * 3, largeCricleRadiu, strokePaint);
		canvas.drawLine(0, -largeCricleRadiu * 4, 0, -lineLength * 5, strokePaint);
		canvas.drawCircle(0, -lineLength * 6, largeCricleRadiu, strokePaint);
 
		// 释放画布
		canvas.restore();
	}
}
```
like below：
保存和释放画布就不说了，用过N次了。
```
canvas.translate(ccX, ccY);
```
我们将画布平移了ccx和xxy个单位其实就是让画布的左上端原点远我们的中心圆圆心重合：
```
canvas.rotate(-30);
```
向左旋转画布30度：
这里有一点非常非常地重要！画布的平移旋转同样也会影响画布的自身坐标！如上图，我们看到画布的坐标也跟着旋转了30度！我们正是利用了这一点巧妙地在画图而避免繁杂的坐标计算！！
同理我们可以绘制出其他三个小圆：
```
public class MultiCricleView extends View {
	private static final float STROKE_WIDTH = 1F / 256F, // 描边宽度占比
			SPACE = 1F / 64F,// 大圆小圆线段两端间隔占比
			LINE_LENGTH = 3F / 32F, // 线段长度占比
			CRICLE_LARGER_RADIU = 3F / 32F,// 大圆半径
			CRICLE_SMALL_RADIU = 5F / 64F,// 小圆半径
			ARC_RADIU = 1F / 8F,// 弧半径
			ARC_TEXT_RADIU = 5F / 32F;// 弧围绕文字半径
 
	private Paint strokePaint;// 描边画笔
 
	private int size;// 控件边长
 
	private float strokeWidth;// 描边宽度
	private float ccX, ccY;// 中心圆圆心坐标
	private float largeCricleRadiu, smallCricleRadiu;// 大圆半径和小圆半径
	private float lineLength;// 线段长度
	private float space;// 大圆小圆线段两端间隔
 
	private enum Type {
		LARGER, SMALL
	}
 
	public MultiCricleView(Context context, AttributeSet attrs) {
		super(context, attrs);
 
		// 初始化画笔
		initPaint(context);
	}
 
	/**
	 * 初始化画笔
	 * 
	 * @param context
	 *            Fuck
	 */
	private void initPaint(Context context) {
		/*
		 * 初始化描边画笔
		 */
		strokePaint = new Paint(Paint.ANTI_ALIAS_FLAG | Paint.DITHER_FLAG);
		strokePaint.setStyle(Paint.Style.STROKE);
		strokePaint.setColor(Color.WHITE);
		strokePaint.setStrokeCap(Paint.Cap.ROUND);
	}
 
	@Override
	protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
		// 强制长宽一致
		super.onMeasure(widthMeasureSpec, widthMeasureSpec);
	}
 
	@Override
	protected void onSizeChanged(int w, int h, int oldw, int oldh) {
		// 获取控件边长
		size = w;
 
		// 参数计算
		calculation();
	}
 
	/*
	 * 参数计算
	 */
	private void calculation() {
		// 计算描边宽度
		strokeWidth = STROKE_WIDTH * size;
 
		// 计算大圆半径
		largeCricleRadiu = size * CRICLE_LARGER_RADIU;
 
		// 计算小圆半径
		smallCricleRadiu = size * CRICLE_SMALL_RADIU;
 
		// 计算线段长度
		lineLength = size * LINE_LENGTH;
 
		// 计算大圆小圆线段两端间隔
		space = size * SPACE;
 
		// 计算中心圆圆心坐标
		ccX = size / 2;
		ccY = size / 2 + size * CRICLE_LARGER_RADIU;
 
		// 设置参数
		setPara();
	}
 
	/**
	 * 设置参数
	 */
	private void setPara() {
		// 设置描边宽度
		strokePaint.setStrokeWidth(strokeWidth);
	}
 
	@Override
	protected void onDraw(Canvas canvas) {
		// 绘制背景
		canvas.drawColor(0xFFF29B76);
 
		// 绘制中心圆
		canvas.drawCircle(ccX, ccY, largeCricleRadiu, strokePaint);
 
		// 绘制左上方图形
		drawTopLeft(canvas);
 
		// 绘制右上方图形
		drawTopRight(canvas);
 
		// 绘制左下方图形
		drawBottomLeft(canvas);
 
		// 绘制下方图形
		drawBottom(canvas);
 
		// 绘制右下方图形
		drawBottomRight(canvas);
	}
 
	/**
	 * 绘制左上方图形
	 * 
	 * @param canvas
	 */
	private void drawTopLeft(Canvas canvas) {
		// 锁定画布
		canvas.save();
 
		// 平移和旋转画布
		canvas.translate(ccX, ccY);
		canvas.rotate(-30);
 
		// 依次画：线-圈-线-圈
		canvas.drawLine(0, -largeCricleRadiu, 0, -lineLength * 2, strokePaint);
		canvas.drawCircle(0, -lineLength * 3, largeCricleRadiu, strokePaint);
		canvas.drawLine(0, -largeCricleRadiu * 4, 0, -lineLength * 5, strokePaint);
		canvas.drawCircle(0, -lineLength * 6, largeCricleRadiu, strokePaint);
 
		// 释放画布
		canvas.restore();
	}
 
	/**
	 * 绘制右上方图形
	 * 
	 * @param canvas
	 */
	private void drawTopRight(Canvas canvas) {
		// 锁定画布
		canvas.save();
 
		// 平移和旋转画布
		canvas.translate(ccX, ccY);
		canvas.rotate(30);
 
		// 依次画：线-圈
		canvas.drawLine(0, -largeCricleRadiu, 0, -lineLength * 2, strokePaint);
		canvas.drawCircle(0, -lineLength * 3, largeCricleRadiu, strokePaint);
 
		// 释放画布
		canvas.restore();
	}
 
	private void drawBottomLeft(Canvas canvas) {
		// 锁定画布
		canvas.save();
 
		// 平移和旋转画布
		canvas.translate(ccX, ccY);
		canvas.rotate(-100);
 
		// 依次画：(间隔)线(间隔)-圈
		canvas.drawLine(0, -largeCricleRadiu - space, 0, -lineLength * 2 - space, strokePaint);
		canvas.drawCircle(0, -lineLength * 2 - smallCricleRadiu - space * 2, smallCricleRadiu, strokePaint);
 
		// 释放画布
		canvas.restore();
	}
 
	private void drawBottom(Canvas canvas) {
		// 锁定画布
		canvas.save();
 
		// 平移和旋转画布
		canvas.translate(ccX, ccY);
		canvas.rotate(180);
 
		// 依次画：(间隔)线(间隔)-圈
		canvas.drawLine(0, -largeCricleRadiu - space, 0, -lineLength * 2 - space, strokePaint);
		canvas.drawCircle(0, -lineLength * 2 - smallCricleRadiu - space * 2, smallCricleRadiu, strokePaint);
 
		// 释放画布
		canvas.restore();
	}
 
	private void drawBottomRight(Canvas canvas) {
		// 锁定画布
		canvas.save();
 
		// 平移和旋转画布
		canvas.translate(ccX, ccY);
		canvas.rotate(100);
 
		// 依次画：(间隔)线(间隔)-圈
		canvas.drawLine(0, -largeCricleRadiu - space, 0, -lineLength * 2 - space, strokePaint);
		canvas.drawCircle(0, -lineLength * 2 - smallCricleRadiu - space * 2, smallCricleRadiu, strokePaint);
 
		// 释放画布
		canvas.restore();
	}
}
```
大家可以看到，每一次绘制我都锁定了画布并平移旋转以调整画布的原点坐标极大程度地方便我们计算。效果如下：
![img](https://img-blog.csdn.net/20141208103154538?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
上面的代码会有巨量的重复代码，正如上面我说，这个图形我们是画死的，在没讲完View的测绘和ViewGroup之前我们不会做任何一个完整的控件，So~~~~同时也是为了方便大家容易理解这玩意是怎么画的，我也就不对重复的方法做进一步封装了，不过在做项目的时候切忌大量的重复代码。
我们再给这些圈圈里加些文字，文字的中点很明显就是这些圈圈的圆心对吧，1/4中我说过如何把文字画到中心的？
```
public class MultiCricleView extends View {
	private static final float STROKE_WIDTH = 1F / 256F, // 描边宽度占比
			SPACE = 1F / 64F,// 大圆小圆线段两端间隔占比
			LINE_LENGTH = 3F / 32F, // 线段长度占比
			CRICLE_LARGER_RADIU = 3F / 32F,// 大圆半径
			CRICLE_SMALL_RADIU = 5F / 64F,// 小圆半径
			ARC_RADIU = 1F / 8F,// 弧半径
			ARC_TEXT_RADIU = 5F / 32F;// 弧围绕文字半径
 
	private Paint strokePaint, textPaint;// 描边画笔和文字画笔
 
	private int size;// 控件边长
 
	private float strokeWidth;// 描边宽度
	private float ccX, ccY;// 中心圆圆心坐标
	private float largeCricleRadiu, smallCricleRadiu;// 大圆半径和小圆半径
	private float lineLength;// 线段长度
	private float space;// 大圆小圆线段两端间隔
	private float textOffsetY;// 文本的Y轴偏移值
 
	public MultiCricleView(Context context, AttributeSet attrs) {
		super(context, attrs);
 
		// 初始化画笔
		initPaint(context);
	}
 
	/**
	 * 初始化画笔
	 * 
	 * @param context
	 *            Fuck
	 */
	private void initPaint(Context context) {
		/*
		 * 初始化描边画笔
		 */
		strokePaint = new Paint(Paint.ANTI_ALIAS_FLAG | Paint.DITHER_FLAG);
		strokePaint.setStyle(Paint.Style.STROKE);
		strokePaint.setColor(Color.WHITE);
		strokePaint.setStrokeCap(Paint.Cap.ROUND);
 
		/*
		 * 初始化文字画笔
		 */
		textPaint = new TextPaint(Paint.ANTI_ALIAS_FLAG | Paint.DITHER_FLAG | Paint.SUBPIXEL_TEXT_FLAG);
		textPaint.setColor(Color.WHITE);
		textPaint.setTextSize(30);
		textPaint.setTextAlign(Paint.Align.CENTER);
 
		textOffsetY = (textPaint.descent() + textPaint.ascent()) / 2;
	}
 
	@Override
	protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
		// 强制长宽一致
		super.onMeasure(widthMeasureSpec, widthMeasureSpec);
	}
 
	@Override
	protected void onSizeChanged(int w, int h, int oldw, int oldh) {
		// 获取控件边长
		size = w;
 
		// 参数计算
		calculation();
	}
 
	/*
	 * 参数计算
	 */
	private void calculation() {
		// 计算描边宽度
		strokeWidth = STROKE_WIDTH * size;
 
		// 计算大圆半径
		largeCricleRadiu = size * CRICLE_LARGER_RADIU;
 
		// 计算小圆半径
		smallCricleRadiu = size * CRICLE_SMALL_RADIU;
 
		// 计算线段长度
		lineLength = size * LINE_LENGTH;
 
		// 计算大圆小圆线段两端间隔
		space = size * SPACE;
 
		// 计算中心圆圆心坐标
		ccX = size / 2;
		ccY = size / 2 + size * CRICLE_LARGER_RADIU;
 
		// 设置参数
		setPara();
	}
 
	/**
	 * 设置参数
	 */
	private void setPara() {
		// 设置描边宽度
		strokePaint.setStrokeWidth(strokeWidth);
	}
 
	@Override
	protected void onDraw(Canvas canvas) {
		// 绘制背景
		canvas.drawColor(0xFFF29B76);
 
		// 绘制中心圆
		canvas.drawCircle(ccX, ccY, largeCricleRadiu, strokePaint);
		canvas.drawText("AigeStudio", ccX, ccY - textOffsetY, textPaint);
 
		// 绘制左上方图形
		drawTopLeft(canvas);
 
		// 绘制右上方图形
		drawTopRight(canvas);
 
		// 绘制左下方图形
		drawBottomLeft(canvas);
 
		// 绘制下方图形
		drawBottom(canvas);
 
		// 绘制右下方图形
		drawBottomRight(canvas);
	}
 
	/**
	 * 绘制左上方图形
	 * 
	 * @param canvas
	 */
	private void drawTopLeft(Canvas canvas) {
		// 锁定画布
		canvas.save();
 
		// 平移和旋转画布
		canvas.translate(ccX, ccY);
		canvas.rotate(-30);
 
		// 依次画：线-圈-线-圈
		canvas.drawLine(0, -largeCricleRadiu, 0, -lineLength * 2, strokePaint);
		canvas.drawCircle(0, -lineLength * 3, largeCricleRadiu, strokePaint);
		canvas.drawText("Apple", 0, -lineLength * 3 - textOffsetY, textPaint);
 
		canvas.drawLine(0, -largeCricleRadiu * 4, 0, -lineLength * 5, strokePaint);
		canvas.drawCircle(0, -lineLength * 6, largeCricleRadiu, strokePaint);
		canvas.drawText("Orange", 0, -lineLength * 6 - textOffsetY, textPaint);
 
		// 释放画布
		canvas.restore();
	}
 
	/**
	 * 绘制右上方图形
	 * 
	 * @param canvas
	 */
	private void drawTopRight(Canvas canvas) {
		float cricleY = -lineLength * 3;
 
		// 锁定画布
		canvas.save();
 
		// 平移和旋转画布
		canvas.translate(ccX, ccY);
		canvas.rotate(30);
 
		// 依次画：线-圈
		canvas.drawLine(0, -largeCricleRadiu, 0, -lineLength * 2, strokePaint);
		canvas.drawCircle(0, cricleY, largeCricleRadiu, strokePaint);
		canvas.drawText("Tropical", 0, cricleY - textOffsetY, textPaint);
 
		// 释放画布
		canvas.restore();
	}
 
	private void drawBottomLeft(Canvas canvas) {
		float lineYS = -largeCricleRadiu - space, lineYE = -lineLength * 2 - space, cricleY = -lineLength * 2 - smallCricleRadiu - space * 2;
 
		// 锁定画布
		canvas.save();
 
		// 平移和旋转画布
		canvas.translate(ccX, ccY);
		canvas.rotate(-100);
 
		// 依次画：(间隔)线(间隔)-圈
		canvas.drawLine(0, lineYS, 0, lineYE, strokePaint);
		canvas.drawCircle(0, cricleY, smallCricleRadiu, strokePaint);
		canvas.drawText("Banana", 0, cricleY - textOffsetY, textPaint);
 
		// 释放画布
		canvas.restore();
	}
 
	private void drawBottom(Canvas canvas) {
		float lineYS = -largeCricleRadiu - space, lineYE = -lineLength * 2 - space, cricleY = -lineLength * 2 - smallCricleRadiu - space * 2;
 
		// 锁定画布
		canvas.save();
 
		// 平移和旋转画布
		canvas.translate(ccX, ccY);
		canvas.rotate(180);
 
		// 依次画：(间隔)线(间隔)-圈
		canvas.drawLine(0, lineYS, 0, lineYE, strokePaint);
		canvas.drawCircle(0, cricleY, smallCricleRadiu, strokePaint);
		canvas.drawText("Cucumber", 0, cricleY - textOffsetY, textPaint);
 
		// 释放画布
		canvas.restore();
	}
 
	private void drawBottomRight(Canvas canvas) {
		float lineYS = -largeCricleRadiu - space, lineYE = -lineLength * 2 - space, cricleY = -lineLength * 2 - smallCricleRadiu - space * 2;
 
		// 锁定画布
		canvas.save();
 
		// 平移和旋转画布
		canvas.translate(ccX, ccY);
		canvas.rotate(100);
 
		// 依次画：(间隔)线(间隔)-圈
		canvas.drawLine(0, lineYS, 0, lineYE, strokePaint);
		canvas.drawCircle(0, cricleY, smallCricleRadiu, strokePaint);
		canvas.drawText("Vibrators", 0, cricleY - textOffsetY, textPaint);
 
		// 释放画布
		canvas.restore();
	}
}
```
That's so easy right?
稍微有点难的是右上方的那个半回扇形，扇形的中心应该是与右上方的圆心重合的对吧，那我们在画右上方图形的时候一起画不就是了？
```
/**
 * 绘制右上方图形
 * 
 * @param canvas
 */
private void drawTopRight(Canvas canvas) {
	float cricleY = -lineLength * 3;
 
	// 锁定画布
	canvas.save();
 
	// 平移和旋转画布
	canvas.translate(ccX, ccY);
	canvas.rotate(30);
 
	// 依次画：线-圈
	canvas.drawLine(0, -largeCricleRadiu, 0, -lineLength * 2, strokePaint);
	canvas.drawCircle(0, cricleY, largeCricleRadiu, strokePaint);
	canvas.drawText("Tropical", 0, cricleY - textOffsetY, textPaint);
 
	// 画弧形
	drawTopRightArc(canvas, cricleY);
 
	// 释放画布
	canvas.restore();
}
 
/**
 * 绘制右上角画弧形
 * 
 * @param canvas
 * @param cricleY
 */
private void drawTopRightArc(Canvas canvas, float cricleY) {
	canvas.save();
	
	canvas.translate(0, cricleY);
	canvas.rotate(-30);
 
	float arcRadiu = size * ARC_RADIU;
 
	RectF oval = new RectF(-arcRadiu, -arcRadiu, arcRadiu, arcRadiu);
 
	arcPaint.setStyle(Paint.Style.FILL);
	arcPaint.setColor(0x55EC6941);
	canvas.drawArc(oval, -22.5F, -135, true, arcPaint);
 
	arcPaint.setStyle(Paint.Style.STROKE);
	arcPaint.setColor(Color.WHITE);
	canvas.drawArc(oval, -22.5F, -135, false, arcPaint);
 
	canvas.restore();
}
```
代码逻辑不复杂，大家很容易能看懂……注释什么的我就不写了……扇形上的文本如果大家理解了画布的变换很容易实现，首先在上面我们绘制扇形的时候已经将画布原点与右上圆心重合了对吧，这时我们再把画布向左旋转 扇形弧度/2 个度数是不是就可以让画布的坐标与扇形的右边重合了呢？那第一个文字的坐标就是[0,负的文字弧形半径]，第二个文字坐标只需转画布过 扇形弧度/4 个弧度以此类推可以画出五个文字：
```
/**
 * 绘制右上角画弧形
 * 
 * @param canvas
 * @param cricleY
 */
private void drawTopRightArc(Canvas canvas, float cricleY) {
	canvas.save();
 
	canvas.translate(0, cricleY);
	canvas.rotate(-30);
 
	float arcRadiu = size * ARC_RADIU;
	RectF oval = new RectF(-arcRadiu, -arcRadiu, arcRadiu, arcRadiu);
	arcPaint.setStyle(Paint.Style.FILL);
	arcPaint.setColor(0x55EC6941);
	canvas.drawArc(oval, -22.5F, -135, true, arcPaint);
	arcPaint.setStyle(Paint.Style.STROKE);
	arcPaint.setColor(Color.WHITE);
	canvas.drawArc(oval, -22.5F, -135, false, arcPaint);
 
	float arcTextRadiu = size * ARC_TEXT_RADIU;
 
	canvas.save();
	// 把画布旋转到扇形左端的方向
	canvas.rotate(-135F / 2F);
 
	/*
	 * 每隔33.75度角画一次文本
	 */
	for (float i = 0; i < 5 * 33.75F; i += 33.75F) {
		canvas.save();
		canvas.rotate(i);
 
		canvas.drawText("Aige", 0, -arcTextRadiu, textPaint);
 
		canvas.restore();
	}
 
	canvas.restore();
 
	canvas.restore();
}
```
对吧？看看效果：
好了，正如我所说，这只是一个单纯地画，而且此类奇葩的玩意难以真正做成一个控件去复用除非真的是公事公办，但是，我们依然可以尝试把它做成一个独立的控件，这在我们学写了如何测绘View和ViewGroup之后对你来说一定是小case。
上面我们的最终效果有一点是不对的，大家发现文字TMD居然都旋转了 - - ，那有木有方法让文字保持水平呢？其实答案我已经告诉你。自己去发掘吧！
源码下载：[传送门](http://download.csdn.net/detail/aigestudio/8234751)
这几天龙体欠安啊、哎……更新进度会有打乱~~~敬请大家follow~



