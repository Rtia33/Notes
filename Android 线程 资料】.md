

# 【Android 线程 资料】

[Android线程,线程池使用及原理博文参考](https://mp.weixin.qq.com/s/YQQ5zwe73wltg8uun8gf_Q)
今天这篇来自 **miaoyongjun** 的投稿是篇分享文章，给大家介绍了他学习过程中搜集的经典好文以及重点复述，希望大家喜欢（另外，文中的蓝字是无法点击的，需要复制对应文字下面的网址至浏览器访问）。

**miaoyongjun **的博客地址：http://www.jianshu.com/users/793dea1c5532

说一说

这块的知识可以说是一大块,想撸清楚还是要花点时间,线程池中关联到的队列不仅在线程池中使用,在各种第三方网络框架和图片框架等等中也是通过自己调度队列来实现异步。有关理论的东西"前人"写的好文章太多了,所以也没必要再去复制粘贴来写一篇博文（**文章结尾链接一个有关线程和线程池的面试题**）。

还是先回顾下Handler消息机制的原理图：

![image](http://upload-images.jianshu.io/upload_images/9028834-7124da62d2b00ee6?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

同样的还是先看看一篇对《Android开发艺术探索》的总结，对线程和线程池有个大致的了解（建议和源码一起看）：

> Android开发艺术探索 第11章 线程与线程池 读书笔记
>
> http://www.jianshu.com/p/8265dba04f34

上面的文章讲的只是基本概念，还是要看看稍微详细的文章：

> 线程、多线程与线程池总结
>
> http://cdn0.jianshu.io/p/b8197dd2934c

在 **AsyncTask **源码中有使用到 **Callable、Future和FutureTask**，对这个概念不清楚的不妨看看这篇博客：

> Java并发编程：Callable、Future和FutureTask
>
> http://www.cnblogs.com/dolphin0520/p/3949310.html

针对书中讲到的AsyncTask附上一张AsyncTask工作原理图(结合源代码理解)：

![image](http://upload-images.jianshu.io/upload_images/9028834-135544c4e6696710?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### **不过不建议使用AsyncTask，因为4.x系统中AsyncTask是串行的，高并发的Task让AsyncTask一个一个串行执行，程序就会很慢。可以看看这篇译文讲解具体原因：**

> 译文：Android中糟糕的AsyncTask
>
> http://droidyue.com/blog/2014/11/08/bad-smell-of-asynctask-in-android/index.html

但是这不是我们不看源码的理由，虽然很多缺点但是掌握AsyncTask原理可以对消息机制以及线程池理解更加深刻。

在《Android开发艺术探索》书中有提到** HandlerThread和IntentService**，具体的分析，可以看看洋神写的两篇文章（如果对消息机制不熟悉的建议再看看消息机制的原理，这样会更好理解这两篇文章）：

> Android HandlerThread 完全解析
>
> http://blog.csdn.net/lmj623565791/article/details/47079737

> Android IntentService完全解析 当Service遇到Handler
>
> http://blog.csdn.net/lmj623565791/article/details/47143563

撸一撸

上面的文章都没有对线程池详细的讲解,所以推荐两篇篇好文，主要是讲android中自带的5个线程池的使用以及自定义线程池的使用,例子也是根据官方的例子来讲解的,**非常详细：**

> Android性能优化之使用线程池处理异步任务
>
> http://blog.csdn.net/u010687392/article/details/49850803

> 【Java并发编程】之十九并发新特性—Executor框架与线程池
>
> http://blog.csdn.net/ns_code/article/details/17465497

文章提及到了默认的一个拒绝策略(AbortPolicy),这里就撸一下4种策略,使用的话只要简单传参：

```java
new ThreadPoolExecutor.DiscardPolicy();
```

##### **RejectedExecutionHandler 的四种拒绝策略**

*   **ThreadPoolExecutor.AbortPolicy(默认策略)：**
    当线程池中的数量等于最大线程数时抛出 **java.util.concurrent.RejectedExecutionException **异常，涉及到该异常的任务也不会被执行。

*   **ThreadPoolExecutor.CallerRunsPolicy()：**
    当线程池中的数量等于最大线程数时,重试添加当前的任务;它会自动重复调用 **execute() **方法。

*   **ThreadPoolExecutor.DiscardOldestPolicy()：**
    当线程池中的数量等于最大线程数时,抛弃线程池中工作队列头部的任务(即等待时间最久Oldest的任务)，并执行新传入的任务。

*   **ThreadPoolExecutor.DiscardPolicy()：**
    当线程池中的数量等于最大线程数时,丢弃不能执行的新加任务。

**顺便再撸一下线程池中的排队策略：**

参考自：

> http://blog.csdn.net/ns_code/article/details/17465497

排队有三种通用策略：

**1\. 直接提交：**

工作队列的默认选项是 **SynchronousQueue**，它将任务直接提交给线程而不保持它们。在此，如果不存在可用于立即运行任务的线程，则试图把任务加入队列将失败，因此会构造一个新的线程。此策略可以避免在处理可能具有内部依赖性的请求集合时出现锁定。直接提交通常要求无界 **maximumPoolSizes **以避免拒绝新提交的任务。当命令以超过队列所能处理的平均数连续到达时，此策略允许无界线程具有增长的可能性。

**2\. 无界队列：**

使用无界队列（例如，不具有预定义容量的 **LinkedBlockingQueue**）将导致在所有 **corePoolSize **线程都忙的情况下将新任务加入队列。这样，创建的线程就不会超过 **corePoolSize**。（因此，**maximumPoolSize **的值也就无效了。）当每个任务完全独立于其他任务，即任务执行互不影响时，适合于使用无界队列；例如，在 Web页服务器中。这种排队可用于处理瞬态突发请求，当命令以超过队列所能处理的平均数连续到达时，此策略允许无界线程具有增长的可能性。

**3\. 有界队列：**

当使用有限的 **maximumPoolSizes **时，有界队列（如 **ArrayBlockingQueue**）有助于防止资源耗尽，但是可能较难调整和控制。队列大小和最大池大小可能需要相互折衷：使用大型队列和小型池可以最大限度地降低 CPU 使用率、操作系统资源和上下文切换开销，但是可能导致人工降低吞吐量。如果任务频繁阻塞（例如，如果它们是 I/O 边界），则系统可能为超过您许可的更多线程安排时间。使用小型队列通常要求较大的池大小，CPU 使用率较高，但是可能遇到不可接受的调度开销，这样也会降低吞吐量。

**针对几篇文章中提到的队列,这里简单总结下：**

**Queue**接口与**List****、Set**同一级别，都是继承了**Collection**接口。Android中大量使用的**BlockingQueue**继承了**Queue**接口,线程池中的队列都是实现**BlockingQueue**接口,看看**BlockingQueue**中定义的一些接口方法：

![image](http://upload-images.jianshu.io/upload_images/9028834-70ae05c90f9703be?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**LinkedBlockingQueue**默认情况下，LinkedBlockingQueue的容量是没有上限的（说的不准确，在不指定时容量为Integer.MAX_VALUE，不要然的话在put时怎么会受阻呢），但是也可以选择指定其最大容量，它是基于链表的队列，此队列按 FIFO（先进先出）排序元素。

**ArrayBlockingQueue**在构造时需要指定容量， 并可以选择是否需要公平性，如果公平参数被设置true，等待时间最长的线程会优先得到处理（其实就是通过将ReentrantLock设置为true来 达到这种公平性的：即等待时间最长的线程会先操作）。通常，公平性会使你在性能上付出代价，只有在的确非常需要的时候再使用它。它是基于数组的阻塞循环队 列，此队列按 FIFO（先进先出）原则对元素进行排序。

**PriorityBlockingQueue**是一个带优先级的 队列，而不是先进先出队列。元素按优先级顺序被移除，该队列也没有上限（看了一下源码，PriorityBlockingQueue是对 PriorityQueue的再次包装，是基于堆数据结构的，而PriorityQueue是没有容量限制的，与ArrayList一样，所以在优先阻塞 队列上put时是不会受阻的。虽然此队列逻辑上是无界的，但是由于资源被耗尽，所以试图执行添加操作可能会导致 OutOfMemoryError），但是如果队列为空，那么取元素的操作take就会阻塞，所以它的检索操作take是受阻的。另外，往入该队列中的元 素要具有比较能力。

**DelayQueue**（基于PriorityQueue来实现的）是一个存放Delayed 元素的无界阻塞队列，只有在延迟期满时才能从中提取元素。该队列的头部是延迟期满后保存时间最长的 Delayed 元素。如果延迟都还没有期满，则队列没有头部，并且poll将返回null。当一个元素的 getDelay(TimeUnit.NANOSECONDS) 方法返回一个小于或等于零的值时，则出现期满，poll就以移除这个元素了。此队列不允许使用 null 元素。

参考自：

> http://blog.sina.com.cn/s/blog_9815359e0102vbet.html

关于队列的具体解释和具体使用,这里分享一个国外的网站(英文比较基础,大部分能看懂)：

> http://tutorials.jenkov.com/java-util-concurrent/blockingqueue.html

OK,至此你已经看完了有关线程和线程池的概念以及使用,你会发现上面的文章有很多重复的地方,没错,那就是重要的地方。当然这是不够的,这里推荐两篇博文,去看看Executor线程池这个东西到底是怎么运作的：

> 戏（细）说Executor框架线程池任务执行全过程（上）
>
> http://www.infoq.com/cn/articles/executor-framework-thread-pool-task-execution-part-01#anch130839

> 戏（细）说Executor框架线程池任务执行全过程（下）
>
> http://www.infoq.com/cn/articles/executor-framework-thread-pool-task-execution-part-02

最后

**面试题链接：**

> JAVA多线程和并发基础面试问答
>
> http://www.cnblogs.com/dolphin0520/p/3932934.html

**Note：**

当时看《Androd开发艺术探索》里分析AsyncTask原理的时候,写了一下关于子线程不能更新UI的测试代码：

![image](http://upload-images.jianshu.io/upload_images/9028834-aaa0220d3b6a927a?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

发现没报错，不正常！

之后听了QQ群里朋友的解答去查阅了有关**ViewRootImpl**的原理，得出下面结论：

首先View更新的流程为：

*   View.invalidate

*   View.invalidateInternal

*   ViewGroup.invalidateChild

*   ViewParent.invalidateChildInParent

*   ViewRootImpl.invalidateChildInParent

*   ViewRootImpl.checkThread

```java
public final void invalidateChild(View child, final Rect dirty) {
    ViewParent parent = this;

    final AttachInfo attachInfo = mAttachInfo;
    if (attachInfo != null) {
        ......

        parent = parent.invalidateChildInParent(location, dirty);

        ......
    }
}
```

**ViewGroup.invalidateChild**方法中 **mAttachInfo **为不空时，才会继续调用 **ViewParent.invalidateChildInParent** 。如果为空，下面的流程就不再执行， **checkThread **方法也就不会执行，也就不会报错。

**view树 **的初始化会在 **ViewRootImpl的performTraversals **开始执行，从 **DecorView**开始遍历绘制view，同时也在将 **mAttachInfo **赋值，但是 **onCreate **完成时，Activity并没有完成初始化view树，所以上面那个情况下 **mAttachInfo **为空。换句话说就是 **checkThread **的代码执行的比你更新UI的代码要晚（上面测试代码加个click或者sleep就会报错了）。



