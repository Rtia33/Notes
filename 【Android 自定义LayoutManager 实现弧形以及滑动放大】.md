

# 【Android 自定义LayoutManager 实现弧形以及滑动放大】
我们都知道 RecyclerView 可以通过将 LayoutManager 设置为 StaggeredGridLayoutManager 来实现瀑布流的效果。默认的还有 LinearLayoutManager 用于实现线性布局，GridLayoutManager 用于实现网格布局。

然而 RecyclerView 可以做的不仅限于此, 通过重写 LayoutManager 我们可以按自己的意愿实现更为复杂的效果。而且将控件与其显示效果解耦之后我们就可以动态的改变其显示效果。

设想有这么一个界面，以列表形式展示了一系列的数据，点击一个按钮后以网格形势显示另一组数据。传统的做法可能是在同一布局下设置了一个 listview 和一个 gridview 然后通过按钮点击事件切换他们的 visiblity 属性。而如果使用 recyclerview 的话你只需通过 setAdapter 方法改变数据, setLayoutManager 方法改变样式即可，这样不仅简化了布局也实现了逻辑上的简洁。

下面我们就来介绍怎么通过重写一个 LayoutManager 来实现一个弧形的 recycylerview 以及另一个会随着滚动在指定位置缩放的 recyclerview。并实现类似 viewpager 的回弹效果。

[![](http://upload-images.jianshu.io/upload_images/9028834-17c48ff7c7315bd4?imageMogr2/auto-orient/strip)](http://upload-images.jianshu.io/upload_images/9028834-17c48ff7c7315bd4?imageMogr2/auto-orient/strip)

 ![技术分享](http://upload-images.jianshu.io/upload_images/9028834-7adee7890c2b2e0b?imageMogr2/auto-orient/strip)

项目地址 [Github](https://github.com/leochuan/CircleLayoutManager)

### 通常重写一个 LayoutManager 可以分为以下几个步骤

*   指定默认的 LayoutParams
*   测量并记录每个 item 的信息
*   回收以及放置各个 item
*   处理滚动

#### 指定默认的 LayoutParams

当你继承 LayoutManager 之后，有一个必须重写的方法

**generateDefaultLayoutParams()**

这个方法指定了每一个子 view 默认的 LayoutParams, 并且这个 LayoutParams 会在你调用 getViewForPosition() 返回子 view 前应用到这个子 view。

```
@Override
public RecyclerView.LayoutParams generateDefaultLayoutParams() {
    return new RecyclerView.LayoutParams(ViewGroup.LayoutParams.WRAP_CONTENT, 
            ViewGroup.LayoutParams.WRAP_CONTENT);
}
```

#### 测量并记录每个 item 的信息

接下来我们需要重写 onLayoutChildren() 这个方法。这是 LayoutManager 的主要入口，他会在初始化布局以及 adapter 数据发生改变（或更换 adapter）的时候调用。所以我们在这个方法中对我们的 item 进行测量以及初始化。

在贴代码前有必要先提一下，recycler 有两种缓存的机制，scrap heap 以及 recycle pool。相比之下 scrap heap 更轻量一点，他会直接将当前的 view 缓存而不通过 adapter，当一个 view 被 detach 之后就会暂存进 scrap heap。而 recycle pool 所存储的 view，我们一般认为里面存的是错误的数据（这个 view 之后需要拿出来重用显示别的位置的数据），所以这里面的 view 会被传给 adapter 进行数据的重新绑定，一般，我们将子 view 从其 parent view 中 remove 之后会将其存入 recycler pool 中。

当界面上我们需要显示一个新的 view 时，recycler 会先检查 scrap heap 中 position 相匹配的 view，如果有，则直接返回，如果没有 recycler 会从 recycler pool 中取一个合适的 view，将其传递给 adapter, 然后调用 adapter 的 bindViewHolder() 方法，绑定数据之后将其返回。

```
@Override
    public void onLayoutChildren(RecyclerView.Recycler recycler, RecyclerView.State state) {
        if (getItemCount() == 0) {
            detachAndScrapAttachedViews(recycler);
            offsetRotate = 0;
            return;
        }

        //calculate the size of child
        if (getChildCount() == 0) {
            View scrap = recycler.getViewForPosition(0);
            addView(scrap);
            measureChildWithMargins(scrap, 0, 0);
            mDecoratedChildWidth = getDecoratedMeasuredWidth(scrap);
            mDecoratedChildHeight = getDecoratedMeasuredHeight(scrap);
            startLeft = contentOffsetX == -1?(getHorizontalSpace() - mDecoratedChildWidth)/2: contentOffsetX;
            startTop = contentOffsetY ==-1?0: contentOffsetY;
            mRadius = mDecoratedChildHeight;
            detachAndScrapView(scrap, recycler);
        }

        //record the state of each items
        float rotate = firstChildRotate;
        for (int i = 0; i < getItemCount(); i++) {
            itemsRotate.put(i,rotate);
            itemAttached.put(i,false);
            rotate+= intervalAngle;
        }

        detachAndScrapAttachedViews(recycler);
        fixRotateOffset();
        layoutItems(recycler,state);
    }
```

getItemCount() 方法会调用 adapter 的 getItemCount() 方法，所以他获取到的是数据的总数，而 getChildCount() 方法则是获取当前已添加了的子 View 的数量。

因为在这个项目中所有 view 的大小都是一样的，所以就只测量了 position 为 0 的 view 的大小。itemsRotate 用于记录初始状态下，每一个 item 的旋转角度，offsetRotate 是旋转的偏移角度，每个 item 的旋转角加上这个偏移角度便是最后显示在界面上的角度，滑动过程中我们只需对应改变 offsetRotate 即可，itemAttached 则用于记录这个 item 是否已经添加到当前界面。

#### 回收以及放置各个 item

```
private void layoutItems(RecyclerView.Recycler recycler,
                             RecyclerView.State state){
        if(state.isPreLayout()) return;

        //remove the views which out of range
        for(int i = 0;i<getChildCount();i++){
            View view =  getChildAt(i);
            int position = getPosition(view);
            if(itemsRotate.get(position) - offsetRotate>maxRemoveDegree
                    || itemsRotate.get(position) - offsetRotate< minRemoveDegree){
                itemAttached.put(position,false);
                removeAndRecycleView(view,recycler);
            }
        }

        //add the views which do not attached and in the range
        for(int i=0;i<getItemCount();i++){
            if(itemsRotate.get(i) - offsetRotate<= maxRemoveDegree
                    && itemsRotate.get(i) - offsetRotate>= minRemoveDegree){
                if(!itemAttached.get(i)){
                    View scrap = recycler.getViewForPosition(i);
                    measureChildWithMargins(scrap, 0, 0);
                    addView(scrap);
                    float rotate = itemsRotate.get(i) - offsetRotate;
                    int left = calLeftPosition(rotate);
                    int top = calTopPosition(rotate);
                    scrap.setRotation(rotate);
                    layoutDecorated(scrap, startLeft + left, startTop + top,
                            startLeft + left + mDecoratedChildWidth, startTop + top + mDecoratedChildHeight);
                    itemAttached.put(i,true);
                }
            }
        }
    }
```

prelayout 是 recyclerview 绘制动画的阶段，因为这个项目不需要处理动画所以直接 return。这里先是将当前已添加的子 view 中超出范围的那些 remove 掉并添加进 recycle pool，（是的，只要调用 removeAndRecycleView 就行了），然后将所有 item 中还没有 attach 的 view 进行测量后，根据当前角度运用一下初中数学知识算出 x,y 坐标后添加到当前布局就行了。

```
private int calLeftPosition(float rotate){
        return (int) (mRadius * Math.cos(Math.toRadians(90 - rotate)));
    }
private int calTopPosition(float rotate){
        return (int) (mRadius - mRadius * Math.sin(Math.toRadians(90 - rotate)));
    }
```

#### 处理滚动

现在我们的 LayoutManager 已经能按我们的意愿显示一个弧形的列表了，只是少了点生气。接下来我们就让他滚起来！

```
@Override
    public boolean canScrollHorizontally() {
        return true;
    }
```

看名字就知道这个方法是用于设定能否横向滚动的，对应的还有 canScrollVertically() 这个方法。

```
@Override
    public int scrollHorizontallyBy(int dx, RecyclerView.Recycler recycler, RecyclerView.State state) {
        int willScroll = dx;

        float theta = dx/DISTANCE_RATIO; // the angle every item will rotate for each dx
        float targetRotate = offsetRotate + theta;

        //handle the boundary
        if (targetRotate < 0) {
            willScroll = (int) (-offsetRotate*DISTANCE_RATIO);
        }
        else if (targetRotate > getMaxOffsetDegree()) {
            willScroll = (int) ((getMaxOffsetDegree() - offsetRotate)*DISTANCE_RATIO);
        }
        theta = willScroll/DISTANCE_RATIO;

        offsetRotate+=theta; //increase the offset rotate so when re-layout it can recycle the right views

        //re-calculate the rotate x,y of each items
        for(int i=0;i<getChildCount();i++){
            View view = getChildAt(i);
            float newRotate = view.getRotation() - theta;
            int offsetX = calLeftPosition(newRotate);
            int offsetY = calTopPosition(newRotate);
            layoutDecorated(view, startLeft + offsetX, startTop + offsetY,
                    startLeft + offsetX + mDecoratedChildWidth, startTop + offsetY + mDecoratedChildHeight);
            view.setRotation(newRotate);
        }

        //different direction child will overlap different way
        layoutItems(recycler, state);
        return willScroll;
    }
```

如果是处理纵向滚动请重写 scrollVerticallyBy 这个方法。

在这里将滑动的距离按一定比例转换成滑动对应的角度，按滑动的角度重新绘制当前的子 view，最后再调用一下 layoutItems 处理一下各个 item 的回收。

到这里一个弧形 (圆形) 的 LayoutManager 就写好了。滑动放大的 layoutManager 的实现与之类似，在中心点 scale 时最大，距离中心 x 坐标做差后取绝对值再转换为对应 scale 即可。

```
private float calculateScale(int x){
        int deltaX = Math.abs(x-(getHorizontalSpace() - mDecoratedChildWidth) / 2);
        float diff = 0f;
        if((mDecoratedChildWidth-deltaX)>0) diff = mDecoratedChildWidth-deltaX;
        return (maxScale-1f)/mDecoratedChildWidth * diff + 1;
    }
```

### Bonuses

#### 添加回弹

如果想实现类似于 viewpager 可以锁定到某一页的效果要怎么做？一开始想到对 scrollHorizontallyBy() 中的 dx 做手脚，但最后实现的效果很不理想。又想到重写并实现 smoothScrollToPosition 方法，然后给 recyclerview 设置滚动监听器在 IDLE 状态下调用 smoothScrollToPosition。但最后滚动到的位置总会有偏移。 
最后查阅 API 后发现 recyclerView 有一个 smoothScrollBy 方法，他会根据你给定的偏移量调用 scrollHorizontallyBy 以及 scrollVerticallyBy。 
所以我们可以重写一个 OnScrollListener，然后给我们的 recyclerView 添加滚动监听器就可以了。

```
public class CenterScrollListener extends RecyclerView.OnScrollListener{
    private boolean mAutoSet = true;

    @Override
    public void onScrollStateChanged(RecyclerView recyclerView, int newState) {
        super.onScrollStateChanged(recyclerView, newState);
        final RecyclerView.LayoutManager layoutManager = recyclerView.getLayoutManager();
        if(!(layoutManager instanceof CircleLayoutManager) && !(layoutManager instanceof ScrollZoomLayoutManager)){
            mAutoSet = true;
            return;
        }

        if(!mAutoSet){
            if(newState == RecyclerView.SCROLL_STATE_IDLE){
                if(layoutManager instanceof ScrollZoomLayoutManager){
                    final int scrollNeeded = ((ScrollZoomLayoutManager) layoutManager).getOffsetCenterView();
                    recyclerView.smoothScrollBy(scrollNeeded,0);
                }else{
                    final int scrollNeeded = ((CircleLayoutManager)layoutManager).getOffsetCenterView();
                    recyclerView.smoothScrollBy(scrollNeeded,0);
                }

            }
            mAutoSet = true;
        }
        if(newState == RecyclerView.SCROLL_STATE_DRAGGING || newState == RecyclerView.SCROLL_STATE_SETTLING){
            mAutoSet = false;
        }
    }
}
```

```
recyclerView.addOnScrollListener(new CenterScrollListener());
```

还需要在自定义的LayoutManager添加一个获取滚动偏移量的方法

```
public int getCurrentPosition(){
        return Math.round(offsetRotate / intervalAngle);
    }

public int getOffsetCenterView(){
        return (int) ((getCurrentPosition()*intervalAngle-offsetRotate)*DISTANCE_RATIO);
    }
```

完整代码已上传 [Github](https://github.com/leochuan/CircleLayoutManager)

### 参考资料

[Building a RecyclerView LayoutManager](http://wiresareobsolete.com/2014/09/building-a-recyclerview-layoutmanager-part-1/)



**引用：**
[自定义LayoutManager 实现弧形以及滑动放大效果RecyclerView](https://www.jianshu.com/p/7bb7556bbe10)



