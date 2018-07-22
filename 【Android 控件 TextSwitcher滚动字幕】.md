

# 【Android 控件 TextSwitcher滚动字幕】
Activity中：

```java
    @BindView(R.id.tv_home_remind_msg)
    TextSwitcher tsRemind;
    
    private void initRemind() {
        tsRemind.setInAnimation(AnimationUtils.loadAnimation(mActivity, R.anim.push_up_in));
        tsRemind.setOutAnimation(AnimationUtils.loadAnimation(mActivity, R.anim.push_up_out));
        tsRemind.setFactory(new ViewSwitcher.ViewFactory() {
            @Override
            public View makeView() {
                TextView textView = new TextView(mActivity);
                textView.setLines(2);
                textView.setEllipsize(TextUtils.TruncateAt.END);
                textView.setTextSize(TypedValue.COMPLEX_UNIT_SP, 15);
                textView.setTextColor(getResources().getColor(R.color.txt_gray));
                FrameLayout.LayoutParams params = new FrameLayout.LayoutParams(
                        ViewGroup.LayoutParams.WRAP_CONTENT, ViewGroup.LayoutParams.WRAP_CONTENT);
//                params.gravity = Gravity.CENTER;
                textView.setLayoutParams(params);
//                textView.setGravity(Gravity.CENTER);
                textView.setText("暂无提醒");
                return textView;
            }
        });
    }
    public void showTodayRemind(final List<TodayRemindListJson.TodayRemind> todayReminds) {
        if (todayReminds == null || todayReminds.size() <= 0) {
            tsRemind.setText("暂无提醒");
        } else {
            final int size = todayReminds.size();
            if (size > 1) {
                remindSubscrib = Flowable.interval(3, TimeUnit.SECONDS)
                        .doOnNext(new Consumer<Long>() {
                            @Override
                            public void accept(Long aLong) throws Exception {

                            }
                        })
                        .observeOn(AndroidSchedulers.mainThread())
                        .subscribe(new Consumer<Long>() {
                            @Override
                            public void accept(Long aLong) throws Exception {
                                int index = (int) (aLong % size);
                                if (index < size) {
                                    TodayRemindListJson.TodayRemind todayRemind = todayReminds.get(index);
                                    if (todayRemind != null) {
                                        tsRemind.setText(todayRemind.getContent());
                                    }
                                }
                            }
                        });
            } else {
                TodayRemindListJson.TodayRemind todayRemind = todayReminds.get(0);
                if (todayRemind != null) {
                    tsRemind.setText(todayRemind.getContent());
                }
            }
        }
    }
    @Override
    public void onDestroy() {
        super.onDestroy();
        if (remindSubscrib != null) {
            remindSubscrib.dispose();
        }
    }
```
push_up_in.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="@integer/animation_default_duration">
    <translate
        android:fromYDelta="100%"
        android:toYDelta="0" />
    <alpha
        android:fromAlpha="0"
        android:toAlpha="1" />

</set>
```
push_up_out.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="@integer/animation_default_duration">
    <translate
        android:fromYDelta="0"
        android:toYDelta="-100%" />
    <alpha
        android:fromAlpha="1"
        android:toAlpha="0" />

</set>
```

