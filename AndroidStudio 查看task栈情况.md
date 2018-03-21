<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [【AndroidStudio 查看task栈情况】](#androidstudio-%E6%9F%A5%E7%9C%8Btask%E6%A0%88%E6%83%85%E5%86%B5)
- [**查看task栈情况**](#%E6%9F%A5%E7%9C%8Btask%E6%A0%88%E6%83%85%E5%86%B5)
- [引用：](#%E5%BC%95%E7%94%A8)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->



# 【AndroidStudio 查看task栈情况】
> 我们都知道，每个Activity都有taskAffinity属性，这个属性指出了它希望进入的Task。

如果一个Activity没有显式的指明该 Activity的taskAffinity，那么它的这个属性就等于Application指明的taskAffinity，如果 Application也没有指明，那么该taskAffinity的值就等于包名。而Task也有自己的affinity属性，它的值等于它的根 Activity的taskAffinity的值。

# **查看task栈情况**

在cmd命令行里或者Android Studio中的**Terminal**里敲入如下命令：

```
adb shell dumpsys activity
```

然后会出现很长一段详细信息，滚到中间的地方，会看到Task栈的状态如下：

![](http://upload-images.jianshu.io/upload_images/9028834-9aafe25a81a50ebf?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此外，这些信息的最底部还可以看到当前显示在前台的Activity是哪一个，还有使用设备的分辨率等信息

![](http://upload-images.jianshu.io/upload_images/9028834-6aec4de2957e7977?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 引用：
[【Android】如何查看Activity Task栈的情况](http://blog.csdn.net/u010983881/article/details/50541498)



