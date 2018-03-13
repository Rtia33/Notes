<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [【Android 自定义View 滚动刻度尺】](#android-%E8%87%AA%E5%AE%9A%E4%B9%89view-%E6%BB%9A%E5%8A%A8%E5%88%BB%E5%BA%A6%E5%B0%BA)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->



# 【Android 自定义View 滚动刻度尺】
http://blog.csdn.net/fengzhongdeyang/article/details/52639880

自定义控件是件很有乐趣的事，慢慢的会爱上它，哈哈哈。废话不多说先上设计图

![image](http://upload-images.jianshu.io/upload_images/9028834-e903becae9da2bf2?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

接下来我们我们要先分析一下页面中需要到哪些东西

1.一条无限长的底线；

2.一根在屏幕中间的红色标记线，代表当前位置；

3.一个半透明的蓝色背景，改背景超过部分才会被展现；

4.刻度 整数高一点刻度  非整第一点的刻度

5.一个大刻度上都会标的数值

6.滚动

接下来进入正题，让我们开始绘制：

首先我们先给控件定义它的属性，根据刚才的分析我给他定义了一下的属性

```xml
<?xml version="1.0" encoding="utf-8"?>  
<resources>  
    <declare-styleable name="RulerView">  
        <!--最大刻度-->  
        <attr name="max_value" format="dimension" />  
        <!--最小刻度-->  
        <attr name="min_value" format="dimension" />  
        <!--刻度字体大小-->  
        <attr name="scale_text_size" format="dimension" />  
        <!--刻度字体颜色-->  
        <attr name="scale_text_color" format="color" />  
        <!--刻度超过颜色-->  
        <attr name="scale_select_color" format="color" />  
        <!--刻度超过颜色背景-->  
        <attr name="scale_select_background_color" format="color" />  
        <!--刻度未超颜色-->  
        <attr name="scale_unselect_color" format="color" />  
        <!--标记颜色-->  
        <attr name="tag_color" format="color" />  
        <!--标尺开始显示位置-->  
        <attr name="start_location" format="dimension" />  
        <!--一屏显示Item-->  
        <attr name="show_item_size" format="integer" />  
        <!--一个刻度的大小-->  
        <attr name="one_item_value" format="integer" />  
    </declare-styleable>  
</resources>  
```

接下来在控件的构造方法中获取这些值，里面还涉及到一些需要初始化的值都有备注

```java
public RulerView(Context context, AttributeSet attrs) {  
       super(context, attrs);  
       WindowManager windowManager = (WindowManager) context.getSystemService(Context.WINDOW_SERVICE);  
       DisplayMetrics dm = new DisplayMetrics();  
       windowManager.getDefaultDisplay().getMetrics(dm);  
       screenWidth = dm.widthPixels;  
       TypedArray typedArray = context.obtainStyledAttributes(attrs, R.styleable.RulerView);  
       maxValue = typedArray.getDimensionPixelOffset(R.styleable.RulerView_max_value, 200000);  
       minValue = typedArray.getDimensionPixelOffset(R.styleable.RulerView_min_value, 0);  
       scaleTextSize = typedArray.getDimensionPixelOffset(R.styleable.RulerView_scale_text_size, 24);  
       scaleTextColor = typedArray.getColor(R.styleable.RulerView_scale_text_color, Color.parseColor("#d8d8d8"));  
       scaleSelectColor = typedArray.getColor(R.styleable.RulerView_scale_select_color, Color.parseColor("#76e4ff"));  
       scaleSelectBackgroundColor = typedArray.getColor(R.styleable.RulerView_scale_select_background_color, Color.parseColor("#6676e4ff"));  
       scaleUnSelectColor = typedArray.getColor(R.styleable.RulerView_scale_unselect_color, Color.parseColor("#d8d8d8"));  
       cursorColor = typedArray.getColor(R.styleable.RulerView_tag_color, Color.parseColor("#ff5555"));  
       if (currLocation == 0) {  
           currLocation = typedArray.getDimensionPixelOffset(R.styleable.RulerView_start_location, 0);  
       }  
       showItemSize = typedArray.getInteger(R.styleable.RulerView_show_item_size, 5);  
       oneItemValue = typedArray.getInteger(R.styleable.RulerView_show_item_size, 1000);  
       typedArray.recycle();  
       //一个刻度的宽度  
       scaleWidth = (screenWidth / (showItemSize * 10));  
       //尺子长度总的个数*一个的宽度  
       viewWidth = maxValue / oneItemValue * scaleWidth;  
       //滚动计算器  
       scroller = new Scroller(context);  
       //手势解析器  
       gestureDetector = new GestureDetector(context, gestureListener);  
       gestureDetector.setIsLongpressEnabled(false);  
   }  
```

我们先从简单的开始画一条在底部的线，那问题来了这个View底部坐标在哪里呢？不用担心View有个onMeasure方法，在里面我们可以直接获取到View的高度

```java
@Override  
 protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {  
     super.onMeasure(widthMeasureSpec, heightMeasureSpec);  
     viewHeight = MeasureSpec.getSize(heightMeasureSpec);  
  
 }  
```

```java
private void drawBottomLine(Canvas canvas) {  
    paint = new Paint(Paint.ANTI_ALIAS_FLAG);  
    paint.setStrokeWidth(3);  
    paint.setColor(scaleUnSelectColor);  
    canvas.drawLine(0, viewHeight, viewWidth, viewHeight, paint);  
}  
```

绘制刻度以及刻度值，以及超过时的颜色

```java
private void drawScale(Canvas canvas) {  
       paint = new Paint(Paint.ANTI_ALIAS_FLAG);  
       paint.setStrokeWidth(2);  
       //计算游标开始绘制的位置  
       float startLocation = (screenWidth / 2) - ((scaleWidth * (currLocation / oneItemValue)));  
       for (int i = 0; i < maxValue / oneItemValue; i++) {  
           //判断当前刻度是否小于当前刻度  
           if (i * oneItemValue <= currLocation) {  
               paint.setColor(scaleSelectColor);  
           } else {  
               paint.setColor(scaleUnSelectColor);  
           }  
           float location = startLocation + i * scaleWidth;  
           if (i % 10 == 0) {  
               canvas.drawLine(location, viewHeight - scaleHeight, location, viewHeight, paint);  
               paintText = new Paint(Paint.ANTI_ALIAS_FLAG);  
               paintText.setTextSize(scaleTextSize);  
               if (i * oneItemValue <= currLocation) {  
                   paintText.setColor(scaleSelectColor);  
               } else {  
                   paintText.setColor(scaleTextColor);  
               }  
               String drawStr = oneItemValue * i + "";  
               Rect bounds = new Rect();  
               paintText.getTextBounds(drawStr, 0, drawStr.length(), bounds);  
               canvas.drawText(drawStr, location - bounds.width() / 2, viewHeight - (scaleHeight + 5), paintText);  
           } else {  
               canvas.drawLine(location, viewHeight - scaleHeight / 2, location, viewHeight, paint);  
           }  
           //绘制选中的背景  
           paint = new Paint(Paint.ANTI_ALIAS_FLAG);  
           paint.setStyle(Paint.Style.FILL);  
           paint.setColor(scaleSelectBackgroundColor);  
           canvas.drawRect(startLocation, viewHeight - scaleHeight / 3, startLocation + currLocation / oneItemValue * scaleWidth, viewHeight, paint);  
       }  
   }  
```

最后绘制一条中线

```java
private void drawCursor(Canvas canvas) {  
    paint = new Paint(Paint.ANTI_ALIAS_FLAG);  
    paint.setStrokeWidth(3);  
    paint.setColor(cursorColor);  
    canvas.drawLine(screenWidth / 2, viewHeight / 5, screenWidth / 2, viewHeight, paint);  
}  
```

上面的方法都要在View的onDraw方法中调用一遍，这样刻度尺就绘制完成了。最后我们的工作就是让刻度尺跟着我们的手指动起来，直接重写onTouchEvent方法将所有事件交给手势处理器来处理然后重写onScroll（滚动），onFling（抛事件）方法

```java
@Override  
  public boolean onTouchEvent(MotionEvent event) {  
  
      gestureDetector.onTouchEvent(event);  
  
      return true;  
  }  
```

```java
private GestureDetector.SimpleOnGestureListener gestureListener = new GestureDetector.SimpleOnGestureListener() {  
  
    public boolean onScroll(MotionEvent e1, MotionEvent e2,  
                            float distanceX, float distanceY) {  
        if (!isScrollingPerformed) {  
            isScrollingPerformed = true;  
        }  
        doScroll((int) -distanceX);  
        invalidate();  
        return true;  
    }  
  
    public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX,  
                           float velocityY) {  
        lastScrollX = getCurrentItem() * getItemWidth() + scrollingOffset;  
        int maxX = getItemsCount()  
                * getItemWidth();  
        int minX = 0;  
        scroller.fling(lastScrollX, 0, (int) (-velocityX / 1.5), 0, minX, maxX, 0, 0);  
        setNextMessage(MESSAGE_SCROLL);  
        return true;  
    }  
};  
```

```java
private void doScroll(int delta) {  
       //偏移量叠加  
       scrollingOffset += delta;  
       //总共滚动了多少个Item  
       int count = scrollingOffset / getItemWidth();  
       //当前位置  
       int pos = getCurrentItem() - count;  
       //限制滚到范围  
       if (isScrollingPerformed) {  
           if (pos < 0) {  
               count = getCurrentItem();  
               pos = 0;  
           } else if (pos >= getItemsCount()) {  
               count = getCurrentItem() - getItemsCount() + 1;  
               pos = getItemsCount() - 1;  
           }  
       }  
       int offset = scrollingOffset;  
       //移动了一个Item的距离，就更新页面  
       if (pos != getCurrentItem()) {  
           setCurrentItem(pos);  
       }  
  
       // 重新更新一下偏移量  
       scrollingOffset = offset - count * getItemWidth();  
  
   }  
  
   public int getItemWidth() {  
       return scaleWidth;  
   }  
  
   public int getCurrentItem() {  
       return currLocation / oneItemValue;  
   }  
  
   public int getItemsCount() {  
       return maxValue / oneItemValue;  
   }  
  
   public void setCurrentItem(int index) {  
       scrollingOffset = 0;  
       currLocation = index * oneItemValue;  
       invalidate();  
       if (onRulerChangeListener != null) {  
           onRulerChangeListener.onChanged(currLocation);  
       }  
   }  
```

```java
private void setNextMessage(int message) {  
      animationHandler.removeMessages(MESSAGE_SCROLL);  
      animationHandler.sendEmptyMessage(message);  
  }  
  
  // 动画处理  
  private Handler animationHandler = new Handler() {  
      public void handleMessage(Message msg) {  
          scroller.computeScrollOffset();  
          int currX = scroller.getCurrX();  
          int delta = lastScrollX - currX;  
          lastScrollX = currX;  
          if (delta != 0) {  
              doScroll(delta);  
          }  
          // 滚动还没有完成，到最后，完成手动  
          if (Math.abs(currX - scroller.getFinalX()) < MIN_DELTA_FOR_SCROLLING) {  
              scroller.forceFinished(true);  
          }  
          if (!scroller.isFinished()) {  
              animationHandler.sendEmptyMessage(msg.what);  
          } else {  
              finishScrolling();  
          }  
      }  
  };  
  
  
  private void finishScrolling() {  
      if (isScrollingPerformed) {  
          isScrollingPerformed = false;  
      }  
      invalidate();  
  }  
```

最后上一张效果图

![image](http://upload-images.jianshu.io/upload_images/9028834-820503324df9a55a?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

源码地址：http://download.csdn.net/detail/fengzhongdeyang/9638371



