<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [【Android 性能优化 资料】](#android-%E6%80%A7%E8%83%BD%E4%BC%98%E5%8C%96-%E8%B5%84%E6%96%99)
- [App启动速度优化](#app%E5%90%AF%E5%8A%A8%E9%80%9F%E5%BA%A6%E4%BC%98%E5%8C%96)
  - [优化启动流程、白屏、SDK懒加载](#%E4%BC%98%E5%8C%96%E5%90%AF%E5%8A%A8%E6%B5%81%E7%A8%8B%E7%99%BD%E5%B1%8Fsdk%E6%87%92%E5%8A%A0%E8%BD%BD)
  - [multidex](#multidex)
  - [检测启动优化工具](#%E6%A3%80%E6%B5%8B%E5%90%AF%E5%8A%A8%E4%BC%98%E5%8C%96%E5%B7%A5%E5%85%B7)
    - [TraceView](#traceview)
    - [StrictMode](#strictmode)
    - [hugo](#hugo)
- [UI流畅度优化](#ui%E6%B5%81%E7%95%85%E5%BA%A6%E4%BC%98%E5%8C%96)
  - [优化方案：](#%E4%BC%98%E5%8C%96%E6%96%B9%E6%A1%88)
  - [检测方案：](#%E6%A3%80%E6%B5%8B%E6%96%B9%E6%A1%88)
- [内存优化](#%E5%86%85%E5%AD%98%E4%BC%98%E5%8C%96)
  - [内存相关基础知识](#%E5%86%85%E5%AD%98%E7%9B%B8%E5%85%B3%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86)
- [apk瘦身](#apk%E7%98%A6%E8%BA%AB)
- [电量优化](#%E7%94%B5%E9%87%8F%E4%BC%98%E5%8C%96)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->



# 【Android 性能优化 资料】
一般情况下，我们谈性能优化基本上会从以下几个方面：

*   App启动速度优化

*   UI流畅度优化

*   内存优化

*   apk瘦身

*   电量优化

还有些网络速度优化、以及特定类型app的一些针对性的优化，比如播放器起播速度等...

我们重点谈论上面5个方面。

性能优化正常的流程是，发现自己某方面与竞品差距比较大，比如UI渲染速度，需要通过某种方式去发现为什么比别人慢，往往需要利用一些**工具**，然后可以做一些针对性的调优（代码优化、实现方式改变等），当然了在调优的过程中会有一些通用性的**调优方式**可以帮助你。

所以本文从共性的角度来看，主要介绍的就是针对以上5个方面的检测工具以及调优方式了。

# App启动速度优化
* 对于App启动速度优化，一般情况下我们会利用主题去防止出现白屏；
* 针对启动速度慢，需要尽可能减少Application的onCreate中所要做的事情，比如一些不重要的SDK延迟或者异步加载；
* 多进程情况下一定要可以在onCreate中去区分进程做一些初始化工作；
* 部分将要使用到的类异步加载；
* 还有针对multidex专门做优化的（当然这个随着5.0以上的设备越来越多，影响倒是没有那么大了）。

下面针对各个方面推荐一些文章：
## 优化启动流程、白屏、SDK懒加载
*   [Android性能优化（一）之启动加速35%](http://www.jianshu.com/p/f5514b1a826c)
*   [Android冷启动白屏解析，带你一步步分析和解决问题](http://blog.csdn.net/guolin_blog/article/details/51019856)
*   [Android APP启动优化](http://wuxiaolong.me/2017/03/13/appStart/)
*   [App启动速度优化之耗时检测处理](http://www.jianshu.com/p/a0e242d57360)
  上面几篇文章基本上描述了应用的启动流程，如何优化白屏，以及一些SDK的懒加载等等...

## multidex
*   [美团Android DEX自动拆包及动态加载简介](https://tech.meituan.com/mt-android-auto-split-dex.html)
*   [Android MultiDex初次启动APP优化](http://blog.csdn.net/synaric/article/details/53540760)
*   [其实你不知道MultiDex到底有多坑](http://t.cn/RjjhD95)
  这3篇可以帮助你对multidex做一定的了解，以及提供了优化方案供参考。

## 检测启动优化工具
当然了，在检测启动优化上，除了利用adb命令去检测总时间，还有就是去发现耗时的方法，可以利用TraceView，或者打开StrictMode，如果你喜欢使用Log，还可以去使用hugo，或者自己写一个类似的AOP日志框架。

### TraceView
*   [TraceView 简介及其案例实战](https://www.cnblogs.com/sunzn/p/3192231.html)
*   [使用 TraceView 找到卡顿的元凶](http://blog.csdn.net/u011240877/article/details/54347396)
*   [Android App优化之提升你的App启动速度之实例挑战](http://www.jianshu.com/p/4f10c9a10ac9)


### StrictMode

### hugo
hugo是Jake大神的一个开源库，主要是利用aspectJ，源码很少，也比较简单，不过使用起来还不错，直接看readme就够了，感兴趣可以看下~
*   https://github.com/JakeWharton/hugo

# UI流畅度优化
谈到UI流畅度，一般就是不要在主进程去做耗时的操作，提升UI的绘制速度（减少View的布局层级，避免过渡绘制等）...TraceView、Lint、Hugo、StrictMode等...

## 优化方案：
*   [Android性能优化（二）之布局优化面面观](http://www.jianshu.com/p/4f44a178c547)
*   [Android UI性能优化实战 识别绘制中的性能问题](http://blog.csdn.net/lmj623565791/article/details/45556391/)
*   [性能优化之布局优化](http://www.trinea.cn/android/layout-performance/
  )
*   [Android性能调优](http://www.trinea.cn/android/android-performance-demo/)

## 检测方案：
1.  一般有监听Looper的日志
2.  利用Choreographer
  可参考我之前编写的：
*   [Android UI性能优化 检测应用中的UI卡顿](http://mp.weixin.qq.com/s?__biz=MzAxMTI4MTkwNQ==&mid=2650822205&idx=1&sn=6b8e78bc1d71eb79a199667cf132acf7&chksm=80b782a3b7c00bb5c12437556fca68136c75409855e9252e395b545621319edf23959942b67c&scene=21#wechat_redirect) 

当然也相应的有一些开源工具：

*   https://github.com/markzhai/AndroidPerformanceMonitor
*   https://github.com/wasabeef/Takt 
*   https://github.com/friendlyrobotnyc/TinyDancer 


# 内存优化
内存优化那么主要就是去消除应用中的内存泄露、避免内存抖动；常用工具就是AS自带的内存检测，可以很好的发现内存抖动；leakcanary可以非常方便的帮助我们发现内存泄露；MAT可以做更多的内存分析。

## 内存相关基础知识

*   [Android性能优化（三）之内存管理](http://www.jianshu.com/p/c4b283848970)
*   [Android性能优化第（二）篇---Memory Monitor检测内存泄露](http://www.jianshu.com/p/ef9081050f5c)
*   [内存泄露实例分析 -- Android内存优化第四弹](http://www.jianshu.com/p/cbe2ee08ca02)
*   [Android最佳性能实践(一)——合理管理内存](http://blog.csdn.net/guolin_blog/article/details/42238627)
*   [Android最佳性能实践(二)——分析内存的使用情况](http://blog.csdn.net/guolin_blog/article/details/42238633)
*   [Android性能优化-内存泄漏的8个Case](http://mp.weixin.qq.com/s?__biz=MzAxMTI4MTkwNQ==&mid=2650822597&idx=1&sn=462b116f97623f239ecf667d3bdef446&chksm=80b7835bb7c00a4d4cbc9f7e19829d9a99f3cf58c1bc43dace16ffec58c98668927c9fa8dcda&scene=21#wechat_redirect) 
*   [Android 内存优化总结&实践](https://mp.weixin.qq.com/s/2MsEAR9pQfMr1Sfs7cPdWQ)
*   [Android内存优化之OOM](http://hukai.me/android-performance-oom/)
*   [Android应用内存泄露分析、改善经验总结](https://zhuanlan.zhihu.com/p/20831913)
*   [内存泄露从入门到精通三部曲之基础知识篇](http://dev.qq.com/topic/59152c9029d8be2a14b64dae)
*   [内存泄露从入门到精通三部曲之排查方法篇](http://dev.qq.com/topic/591522d9142eee2b6b9735a2)
*   [手把手教你在Android Studio 3.0上分析内存泄漏](http://mp.weixin.qq.com/s?__biz=MzAxMTI4MTkwNQ==&mid=2650824544&idx=1&sn=2fc3cc16806bd1ddd9902ccef2cd12f5&chksm=80b78bfeb7c002e822314661aa0df8e8d7981fb2ff3bc362c129bbf2952a87ff59a6213b821c&scene=21#wechat_redirect)

# apk瘦身
关于Apk瘦身，主要由以下几个方式：

1.  利用ProGuard压缩代码去除无用资源

2.  andresguard进一步压缩与混淆资源

3.  第三方开源库的瘦身，仅保留自己需要的部分

4.  极致的图片压缩与webp的使用

5.  合理配置去除不必要的配置，仅保留中文配置等...

6.  so的优化与配置，只保留一类so

7.  动态下发一些资源:字库、so、换肤包等；

以上仅有7比较麻烦，需要服务端的配合，此外对于动态下发So，可以参考tinker对So热修复部分代码。

其余都是常规方式，且1 ，5，6都比较简单，build.gradle最下配置即可，当然了也有一些参考文章：

*   [App瘦身最佳实践](http://www.jianshu.com/p/8f14679809b3#)
*   [Android APP终极瘦身指南](http://t.cn/RGjNpam)
*   [Android性能优化（十）之App瘦身攻略](http://www.jianshu.com/p/99f3c09982d4
  )
*   [APK瘦身看这一篇文章就够了](http://www.jianshu.com/p/6be4f98162d7)
*   [安装包立减1M--微信Android资源混淆打包工具](http://t.cn/RjjVe4f)
*   [爱奇艺Android移动客户端app瘦身经验](http://t.cn/RjjfzrY)
*   [Android Webp 完全解析 快来缩小apk的大小吧](http://mp.weixin.qq.com/s?__biz=MzAxMTI4MTkwNQ==&mid=2650821531&idx=1&sn=9a51c95bf149d3c5a487f9bb4cd3dcf9&chksm=80b78705b7c00e13aac7d021b1cda0d774dbca584bd106c0f89e8521718cc4ea32cc255dccd2&scene=21#wechat_redirect) 
*   [App优化攻略-用TextView显示图片](http://mp.weixin.qq.com/s?__biz=MzAxMTI4MTkwNQ==&mid=2650821059&idx=1&sn=6067ef412e0e16de2e436c8986283f88&scene=21#wechat_redirect) 
*   [Android IconFont全攻略](http://mp.weixin.qq.com/s?__biz=MzAxMTI4MTkwNQ==&mid=2650821086&idx=1&sn=080bc2cfaa04947b974bf6273bbd7259&chksm=80b78540b7c00c56fd15fb8027b4d2730523c32c2099970cc49f9ddc03d235c907dc59e1695c&scene=21#wechat_redirect) 

# 电量优化
电量优化说实在的关注度较低，一般情况就是合理的使用一些传感器、谨慎的使用Wake Lock、减少后台的不要的操作等...检测可以利用battery-historian 
*   [Android性能优化（九）之不可忽视的电量](http://www.jianshu.com/p/5d83d8649c98)
*   [Android性能优化之电量篇](http://hukai.me/android-performance-battery/)
*   [Android性能优化-电量优化](http://mp.weixin.qq.com/s?__biz=MzAxMTI4MTkwNQ==&mid=2650823025&idx=1&sn=561e345e918900af610b1b10377934e8&chksm=80b78defb7c004f90bc516ad9b5183d2d4f2f3e4972570e0040b0b8a72d7501f438b9d6a8fa6&scene=21#wechat_redirect) 
*   [Android性能优化系列之电量优化](http://blog.csdn.net/u012124438/article/details/74617649)
*   [Android App优化之电池省着用](http://www.jianshu.com/p/c55ef05c0047)
*   https://github.com/google/battery-historian

好了，大家可以将每个方案的所采用的实际的**优化策略**记录下来，对每种方式提到的**工具**一定要自己去体验，用起来肯定不会有多么的困难，实操起来印象会更加深刻。

性能优化并不困难，重要的还是要去实际的去操作一把，基本上也是面试常见的问题了。

最后，好文非常多，本文希望仅起到抛砖引入的效果，感谢所有作者~

想起来了16年MDCC上明云凶做过一个性能优化的分享，所以每次谈到性能优化我都能想到当年演讲的ppt。（[ppt的下载以及Google的性能优化典范视频](https://pan.baidu.com/s/1kVHyCUb)，懒得复制的可以公众号内回复**1118**即可。）

对了，腾讯有个非常强大的手机上的“集成调测环境”，就是手机上的软件，可以用于性能检测，叫[GT](http://gt.tencent.com/download.html)

引用：
[必知必会 | Android 性能优化的方面方面都在这儿](https://mp.weixin.qq.com/s?__biz=MzAxMTI4MTkwNQ==&mid=2650824552&idx=1&sn=a634748d786072ecb083e46f27362d87&chksm=80b78bf6b7c002e09b949b7fbc14b9ae0eb97d8794aca6fa6d42f80afcd27d07947641bab083&scene=21#wechat_redirect)

