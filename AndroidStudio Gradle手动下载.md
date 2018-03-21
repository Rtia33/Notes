<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [【AndroidStudio Gradle手动下载】](#androidstudio-gradle%E6%89%8B%E5%8A%A8%E4%B8%8B%E8%BD%BD)
- [详细步骤：](#%E8%AF%A6%E7%BB%86%E6%AD%A5%E9%AA%A4)
  - [确定所需版本](#%E7%A1%AE%E5%AE%9A%E6%89%80%E9%9C%80%E7%89%88%E6%9C%AC)
  - [下载地址：https://services.gradle.org/distributions/](#%E4%B8%8B%E8%BD%BD%E5%9C%B0%E5%9D%80httpsservicesgradleorgdistributions)
  - [打开AndroidStudio自动生成文件夹 /.gradle/wrapper/dists/gradle-2.14.1-all/8bnwg5hd3w55iofp58khbp6yv](#%E6%89%93%E5%BC%80androidstudio%E8%87%AA%E5%8A%A8%E7%94%9F%E6%88%90%E6%96%87%E4%BB%B6%E5%A4%B9-gradlewrapperdistsgradle-2141-all8bnwg5hd3w55iofp58khbp6yv)
  - [复制文件](#%E5%A4%8D%E5%88%B6%E6%96%87%E4%BB%B6)
- [Gradle Plugin 和 Gradle的版本需求](#gradle-plugin-%E5%92%8C-gradle%E7%9A%84%E7%89%88%E6%9C%AC%E9%9C%80%E6%B1%82)
- [引用：](#%E5%BC%95%E7%94%A8)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->



# 【AndroidStudio Gradle手动下载】
1. 下载好的压缩包和解压后的文件夹复制到/.gradle/wrapper/dists/gradle-2.14.1-all/8bnwg5hd3w55iofp58khbp6yv文件夹下；
2. 将gradle-2.14.1-all.zip.part文件删除；
3. 复制一份gradle-2.14.1-all.zip.lck文件，重命名为gradle-2.14.1-all.zip.ok；
4. 重启as。

# 详细步骤：
## 确定所需版本
在Project的视图下，找到gradle，里面的cradle-wrapper.properties 里面的gradle版本即为我们所需
![](http://upload-images.jianshu.io/upload_images/9028834-c80e6081ec2749c1?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 下载地址：[https://services.gradle.org/distributions/](https://services.gradle.org/distributions/) 

## 打开AndroidStudio自动生成文件夹 /.gradle/wrapper/dists/gradle-2.14.1-all/8bnwg5hd3w55iofp58khbp6yv
当Android Studio下载过gradle-2.14.1-all之后（不管成功失败），就会在C:/Users/用户名/.gradle/wrapper/dists（或自己修改的.gradle缓存目录/wrapper/dists）中出现gradle-2.14.1-all文件夹，打开之后是一串以无规律字符命名的文件夹，我的是8bnwg5hd3w55iofp58khbp6yv，这个可能因人而异。打开之后就是真正的gradle文件。

## 复制文件
将下载好的压缩包和解压后的文件夹复制到上述文件夹下；
将gradle-2.14.1-all.zip.part文件删除；
复制一份gradle-2.14.1-all.zip.lck文件，重命名为gradle-2.14.1-all.zip.ok；
重启as。
![最终效果](http://upload-images.jianshu.io/upload_images/9028834-d5f84438961b85a0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# Gradle Plugin 和 Gradle的版本需求

参考官方网站，选择合适的[https://developer.android.com/studio/releases/gradle-plugin.html#updating-plugin](https://developer.android.com/studio/releases/gradle-plugin.html#updating-plugin)

| Plugin version | Required Gradle version |
-|-
| 1.0.0 - 1.1.3 | 2.2.1 - 2.3 |
| 1.2.0 - 1.3.1 | 2.2.1 - 2.9 |
| 1.5.0 | 2.2.1 - 2.13 |
| 2.0.0 - 2.1.2 | 2.10 - 2.13 |
| 2.1.3 - 2.2.3 | 2.14.1+ |
| 2.3.0+ | 3.3+ |


<br><br><br>
# 引用：
[Android gradle 手动下载（注意不是Android Gradle Plugin）](http://blog.csdn.net/rodulf/article/details/71023300)
[Android Studio 自动升级 gradle 失败解决方案](http://blog.csdn.net/chrisyuu/article/details/52711025)





