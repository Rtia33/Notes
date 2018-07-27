<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [【Android 虚拟机】](#android-%E8%99%9A%E6%8B%9F%E6%9C%BA)
  - [1.Dalvik虚拟机](#1dalvik%E8%99%9A%E6%8B%9F%E6%9C%BA)
    - [DVM vs JVM](#dvm-vs-jvm)
      - [共同点：](#%E5%85%B1%E5%90%8C%E7%82%B9)
      - [基于的架构不同](#%E5%9F%BA%E4%BA%8E%E7%9A%84%E6%9E%B6%E6%9E%84%E4%B8%8D%E5%90%8C)
      - [执行的字节码不同](#%E6%89%A7%E8%A1%8C%E7%9A%84%E5%AD%97%E8%8A%82%E7%A0%81%E4%B8%8D%E5%90%8C)
      - [DVM允许在有限的内存中同时运行多个进程](#dvm%E5%85%81%E8%AE%B8%E5%9C%A8%E6%9C%89%E9%99%90%E7%9A%84%E5%86%85%E5%AD%98%E4%B8%AD%E5%90%8C%E6%97%B6%E8%BF%90%E8%A1%8C%E5%A4%9A%E4%B8%AA%E8%BF%9B%E7%A8%8B)
      - [DVM由Zygote创建和初始化](#dvm%E7%94%B1zygote%E5%88%9B%E5%BB%BA%E5%92%8C%E5%88%9D%E5%A7%8B%E5%8C%96)
      - [DVM优势](#dvm%E4%BC%98%E5%8A%BF)
    - [DVM架构](#dvm%E6%9E%B6%E6%9E%84)
    - [DVM的运行时堆](#dvm%E7%9A%84%E8%BF%90%E8%A1%8C%E6%97%B6%E5%A0%86)
  - [2.ART虚拟机](#2art%E8%99%9A%E6%8B%9F%E6%9C%BA)
    - [ART vs DVM](#art-vs-dvm)
    - [ART的运行时堆](#art%E7%9A%84%E8%BF%90%E8%A1%8C%E6%97%B6%E5%A0%86)
- [面试题](#%E9%9D%A2%E8%AF%95%E9%A2%98)
- [引用：](#%E5%BC%95%E7%94%A8)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->



# 【Android 虚拟机】

## 1.Dalvik虚拟机

Dalvik虚拟机（ Dalvik Virtual Machine ），简称Dalvik VM或者DVM。它是由Dan Bornstein编写的，名字源于他的祖先居住过的名为Dalvik的小渔村。DVM是Google专门为Android平台开发的虚拟机，它运行在Android运行时库中。
需要注意的是**DVM并不是一个Java虚拟机**（以下简称JVM）。

### DVM vs JVM

#### 共同点：
  解释执行 byte code字节码文件
  在操作系统的进程运行一个VM，并执行一个单独的程序

#### 基于的架构不同 
**JVM基于栈**则意味着需要去栈中读写数据，所需的指令会更多，这样会导致速度慢，对于性能有限的移动设备，显然不是很适合。 
**DVM**是**基于寄存器**的，它没有基于栈的虚拟机在拷贝数据而使用的大量的出入栈指令，同时指令更紧凑更简洁。但是由于显示指定了操作数，所以基于寄存器的指令会比基于栈的指令要大，但是由于指令数量的减少，总的代码数不会增加多少。

#### 执行的字节码不同 
在Java SE程序中，**Java**类会被编译成一个或**多个.class文件**，打包成jar文件，而后JVM会通过相应的.class文件和jar文件获取相应的字节码。执行顺序为： **.java文件 -> .class文件 -> .jar文件** 
而**DVM**会用dx工具将所有的.class文件转换为**一个.dex文件**，然后DVM会从该.dex文件读取指令和数据。执行顺序为： 
**.java文件 –>.class文件-> .dex文件** 
![](http://upload-images.jianshu.io/upload_images/9028834-8b870dbfbfd2f887?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如上图所示，.jar文件里面包含多个.class文件，每个.class文件里面包含了该类的常量池、类信息、属性等等。当JVM加载该.jar文件的时候，会加载里面的所有的.class文件，JVM的这种加载方式很慢，对于内存有限的移动设备并不合适。 
而在.apk文件中只包含了一个.dex文件，这个.dex文件里面将所有的.class里面所包含的信息全部整合在一起了，这样再加载就提高了速度。.class文件存在很多的冗余信息，**dex**工具会**去除冗余信息**，并把**所有的.class文件整合到.dex文件中**，**减少**了**I/O**操作，**提高**了类的查找**速度**。

#### DVM允许在有限的内存中同时运行多个进程 
DVM经过优化，允许在有限的内存中同时运行多个进程。
在Android中的每**一个应用**都运行在**一个DVM实例**中，每一个DVM实例都运行在**一个独立的进程空间**。独立的进程可以防止在虚拟机崩溃的时候所有程序都被关闭。

#### DVM由Zygote创建和初始化 
在[Android系统启动流程（二）解析Zygote进程启动过程](http://liuwangshu.cn/framework/booting/2-zygote.html)这篇文章中我介绍过 Zygote，可以称它为孵化器，它是一个DVM进程，同时它也用来创建和初始化DVM实例。每当系统需要创建一个应用程序时，Zygote就会fock自身，快速的创建和初始化一个DVM实例，用于应用程序的运行。

#### DVM优势
编译时提前优化代码而不是等到运行时 。
虚拟机很小，占空间小。可以满足可高效运行多种虚拟机实例。 
常量池修改为只使用32位的索引，以简化解释器。 


### DVM架构

DVM的源码位于dalvik/目录下，其中dalvik/vm目录下的内容是DVM的具体实现部分，它会被编译成libdvm.so；dalvik/libdex会被编译成libdex.a静态库，作为dex工具使用；dalvik/dexdump是.dex文件的反编译工具；DVM的可执行程序位于dalvik/dalvikvm中，将会被编译成dalvikvm可执行程序。DVM架构如下图所示。

![DVM架构](http://upload-images.jianshu.io/upload_images/9028834-2de7e9a8101d67ab?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从上图可以看出，首先Java编译器编译的.class文件经过DX工具转换为.dex文件，.dex文件由类加载器处理，接着解释器根据指令集对Dalvik字节码进行解释、执行，最后交与Linux处理。

### DVM的运行时堆

DVM的运行时堆主要由两个Space以及多个辅助数据结构组成，两个Space分别是Zygote Space（Zygote Heap）和Allocation Space（Active Heap）。Zygote Space用来管理Zygote进程在启动过程中预加载和创建的各种对象，Zygote Space中不会触发GC，所有进程都共享该区域，比如系统资源。Allocation Space是在Zygote进程fork第一个子进程之前创建的，它是一种私有进程，Zygote进程和fock的子进程在Allocation Space上进行对象分配和释放。 
除了这两个Space，还包含以下数据结构：

*   Card Table：用于DVM Concurrent GC，当第一次进行垃圾标记后，记录垃圾信息。
*   Heap Bitmap：有两个Heap Bitmap，一个用来记录上次GC存活的对象，另一个用来记录这次GC存活的对象。
*   Mark Stack：DVM的运行时堆使用标记-清除（Mark-Sweep）算法进行GC，不了解标记-清除算法的同学查看[Java虚拟机（四）垃圾收集算法](http://liuwangshu.cn/java/jvm/4-garbage-collection.html)这篇文章。Mark Stack就是在GC的标记阶段使用的，它用来遍历存活的对象。

## 2.ART虚拟机

ART(Android Runtime)是**Android 4.4发布**的，用来**替换Dalvik虚拟**，Android 4.4默认采用的还是DVM，系统会提供一个选项来开启ART。在**Android 5.0**时，**默认**采用**ART**，DVM从此退出历史舞台。

### ART vs DVM

**DVM**中的应用每次**运行**时，字节码都需要通过**即时编译**器（JIT，just in time）转换为机器码，这会使得应用的运行效率降低。
而在**ART**中，系统在**安装**应用时会进行一次**预编译**（AOT，ahead of time）,将字节码预先编译成机器码并存储在本地，这样应用每次运行时就不需要执行编译了，运行效率也大大提升。

### ART的运行时堆

与DVM的GC不同的是，ART的GC类型有多种，主要分为Mark-Sweep GC和Compacting GC。
ART的运行时堆的空间根据不同的GC类型也有着不同的划分，如果采用的是Mark-Sweep GC，运行时堆主要是由四个Space和多个辅助数据结构组成，四个Space分别是Zygote Space、Allocation Space、Image Space和Large Object Space。Zygote Space、Allocation Space和DVM中的作用是一样的。Image Space用来存放一些预加载类，Large Object Space用来分配一些大对象（默认大小为12k）。其中Zygote Space和Image Space是进程间共享的。 
采用Mark-Sweep GC的运行时堆空间划分如下图所示。

![DVM和ADT(1).png](http://upload-images.jianshu.io/upload_images/9028834-070c67442c4b818f?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

除了这四个Space，ART的Java堆中还包括两个Mod Union Table，一个Card Table，两个Heap Bitmap，两个Object Map，以及三个Object Stack。如果想要跟多的了解它们，请参考[ART运行时Java堆创建过程分析 – 罗升阳](http://blog.csdn.net/luoshengyang/article/details/42379729)这篇文章。

参考资料 
《深入解析Android虚拟机》 
《Android技术内幕-系统卷》 
《Android性能优化最佳实践》 
[stackoverflow：关于dvm存储的问题](https://stackoverflow.com/questions/22299403/whats-the-data-in-dalvik-linearalloc-dalvik-aux-structure-dalvik-bitmap-1-da) 
[Dalvik Virtual Machine – COSC 530](http://www.icl.utk.edu/~luszczek/teaching/courses/fall2013/cosc530/project-report.pdf) 
[ART运行时Java堆创建过程分析 – 罗升阳](http://blog.csdn.net/luoshengyang/article/details/42379729) 
[Dalvik虚拟机Java堆创建过程分析 – 罗升阳](http://blog.csdn.net/luoshengyang/article/details/41581063) 
[ART运行时垃圾收集机制简要介绍和学习计划 – 罗升阳](http://blog.csdn.net/luoshengyang/article/details/42072975) 
[Android 性能优化—Android memory 参数tuning(二)](http://www.10tiao.com/html/431/201601/401579694/1.html)

# 面试题
Android dvm的进程和Linux的进程，以下说法是错误的是
A. 它们都是进程的一种
B. dvm是android的虚拟机
C. linux的进程是程序的具体实现，是执行程序的过程
D. dvm中可以创建多个进程来处理应用间的同步问题


正确答案: D
Android 运行时由两部分组成： Android 核心库集和 Dalvik 虚拟机。其中核心库集提供了 Java 语言核心库所能使用的绝大部分功能，而虚拟机则负责运行 Android 应用程序。
每个 Android 应用程序都运行在单独的 Dalvik 虚拟机内（即每个 Android 应用程序对应一条 Dalvik 进程）， Dalvik 专门针对同时高效地运行多个虚拟机进行优化，因此 Android 系统以方便的实现对应用程序进行隔离。


# 引用：
[Android内存优化（一）DVM和ART原理初探](https://blog.csdn.net/itachi85/article/details/72861179)



