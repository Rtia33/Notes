<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [【JAVA 开发环境搭建】](#java-%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA)
- [一、安装JDK](#%E4%B8%80%E5%AE%89%E8%A3%85jdk)
- [二、配置环境变量](#%E4%BA%8C%E9%85%8D%E7%BD%AE%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->



# 【JAVA 开发环境搭建】
# 一、安装JDK

##1、JVM、JRE、JDK名词解释

| 名词   | 解释                                       |
| ---- | ---------------------------------------- |
| JVM  | (Java Virtual Machine—Java虚拟机) 负责Java程序在该系统中的运行。<br>因为有了JVM，所以同一个Java程序在三个不同的操作系统中都可以执行。这样就实现了Java程序的**跨平台性**。也称为Java具有良好的**可移植性**。 |
| JRE  | (Java Runtime Environment—Java运行时环境) JVM+Java library |
| JDK  | (Java Development kit—Java开发工具包) JRE+Tool<br>JDK是Java的核心，包含了Java的运行环境（Java Runtime Environment），一堆Java工具和给开发者开发应用程序时调用的Java类库。 |

##2、安装JDK
官方下载地址：[http://www.oracle.com/technetwork/java/javase/downloads/index.html](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

**JDK JRE安装到同一个JAVA目录下**(SE表示标准版)

![](http://upload-images.jianshu.io/upload_images/9028834-a174f2cd4a0d0f62.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##3、JDK工具介绍
jdk的安装目录下的Bin目录，里面有许多后缀名为exe的可执行程序，这些都是JDK包含的工具。通过第二步讲到的配置JDK的变量环境，我们可以方便地调用这些工具及它们的命令。
 ![](http://upload-images.jianshu.io/upload_images/9028834-25c1f57c2f0d65ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
<br>
| 基本工具    | 作用                   |
| ------- | -------------------- |
| javac   | Java编译器，将源代码转成字节码。   |
| jar     | 打包工具，将相关的类文件打包成一个文件。 |
| javadoc | 文档生成器，从源码注释中提取文档。    |
| jdb     | debugger，调试查错工具。     |
| java    | 运行编译后的java程序。        |

# 二、配置环境变量
##1、为何要配置环境变量

我们平时打开一个应用程序，一般是通过桌面的应用程序图标双击或单击系统开始菜单中应用程序的菜单链接，无论是桌面的快捷图标还是菜单链接都包含了应用程序的安装位置信息，打开它们的时候系统会按照这些位置信息找到安装目录然后启动程序。

 ![](http://upload-images.jianshu.io/upload_images/9028834-5ec3cd3b49c46967.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

知道了一个应用程序的安装目录位置，我们也可以通过命令行工具打开，如QQ的位置为：C:\Program Files (x86)\Tencent\QQ\QQProtect\Bin，QQ的应用程序名为为QQProtect.exe，那么我们打开命令行工具，然后进入到“C:\Program Files (x86)\Tencent\QQ\QQProtect\Bin”目录，再输入“QQProtect”，即可运行qq。

![](http://upload-images.jianshu.io/upload_images/9028834-fe375fb2c9103fa7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果我们希望打开命令行工具后，直接输入“QQProtect”就能启动qq程序，而不是每次都进入qq的安装目录再启动，这个时候可以通过配置系统环境变量Path来实现。配置完环境变量后，不管在任何目录下，你只要输入qqprotect的命令，qq就会启动。

![](http://upload-images.jianshu.io/upload_images/9028834-fa13f0f148206bb0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
<br>
>总结：当要求系统启动一个应用程序时，系统会先在当前目录下查找，如果没有则在系统变量path指定的路径去查找。前面我们说了JDK包含了一堆开发工具，这些开发工具都在JDK的安装目录下，为了方便使用这些开发工具，我们有必要把JDK的安装目录设置了系统变量。这就是为什么在Windows安装了JDK后需要设置JDK的bin目录为系统环境变量的原因。

##2、临时配置环境变量
临时配置方式：通过dos命令中set命令完成（这种配置方式只在当前dos窗口有效。窗口关闭，配置消失。）
| 命令                 | 作用                |
| ------------------ | ----------------- |
| set                | 用于查看本机的所有环境变量的信息。 |
| set	变量名            | 查看具体一个环境变量的值。     |
| set	变量名=           | 清空一个环境变量的值。       |
| set	变量名=具体值        | 给指定变量定义具体值。       |
| %变量名%              | 获取到原有环境变量的值       |
| set	path=新值;%path% | 在原有环境变量值基础上添加新值   |
<br>
配置前
![](https://upload-images.jianshu.io/upload_images/9028834-e6187120dd9774ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
<br>
配置后
![](https://upload-images.jianshu.io/upload_images/9028834-26d7863f9c0961a4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##3、配置JDK系统环境变量
右击“我的电脑”，选择“属性”，在打开窗口中点击左边的“高级系统设置”，出现“系统属性”窗口，在“高级”选项卡下面点击“环境变量”，对以下变量进行相应增加修改，确定保存，即可。
| 变量名            | 变量值                                      |
| -------------- | ---------------------------------------- |
| **JAVA_HOME**  | 新增该系统变量名称，变量值为JDK在你电脑上的安装路径：C:\Program Files\Java\jdk1.8.0_20。创建好后则可以利用%JAVA_HOME%作为JDK安装目录的统一引用路径 |
| **Path**       | PATH属性已存在，在原来变量后追加：<br>;%JAVA_HOME%\bin  |
| **CLASSPATH ** | 新增该系统变量名称，变量值为：<br>.;%JAVA_HOME%\lib （变量值字符串前面有一个"."表示当前目录）<br><br>设置CLASSPATH 的目的，在于告诉Java执行环境，在哪些目录下可以找到您所要执行的Java程序所需要的类或者包。<br><br>如果没有配置classpath环境变量，JVM只在当前目录下查找要运行的类文件。<br>如果配置了classpath环境，JVM会先在classpath环境变量值的目录中查找要运行的类文件。<br>（• 值的结尾处如果加上分号，那么JVM在classpath目录下没有 找到要指定的类文件，会在当前目录下在查找一次。<br>• 值的结尾出如果没有分号，那么JVM在classpath目录下没有找到要指定的类文件，不会在当前目录下查找，即使当前目录下有，也不会运行。<br>• 建议：配置classpath环境变量时，值的结尾处不要加分号，如果需要访问当前目录可以用 “.”表示。） |
<br>





#引用
[五步搞定Android开发环境部署——非常详细的Android开发环境搭建教程](http://www.cnblogs.com/zoupeiyang/p/4034517.html)
[JAVA开发环境的搭建（配置JAVA开发环境）](https://www.cnblogs.com/Belieflee/p/4778315.html)



