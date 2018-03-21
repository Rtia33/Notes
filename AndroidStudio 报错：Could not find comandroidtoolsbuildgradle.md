<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [【AndroidStudio 报错:Could not find com.android.tools.build:gradle】](#androidstudio-%E6%8A%A5%E9%94%99could-not-find-comandroidtoolsbuildgradle)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->



# 【AndroidStudio 报错:Could not find com.android.tools.build:gradle】

报错
```
Error: Could not find com.android.tools.build:gradle:2.2.1. 
Searched in the following locations: 
file:/D:/software/android/android-studio-ide-145.3276617-windows/android-studio/gradle/m2repository/com/android/tools/build/gradle/2.2.1/gradle-2.2.1.pom 
file:/D:/software/android/android-studio-ide-145.3276617-windows/android-studio/gradle/m2repository/com/android/tools/build/gradle/2.2.1/gradle-2.2.1.jar 
https://jcenter.bintray.com/com/android/tools/build/gradle/2.2.1/gradle-2.2.1.pom 
https://jcenter.bintray.com/com/android/tools/build/gradle/2.2.1/gradle-2.2.1.jar
```
解决方法：进入```%android-studio安装目录%\gradle\m2repository\com\android\tools\build\gradle```
![](http://upload-images.jianshu.io/upload_images/9028834-d38103c5f3916456?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将项目中的build.gradle文件中

```
dependencies {
        classpath 'com.android.tools.build:gradle:2.2.1'
}
```

改为现有的

```
dependencies {
        classpath 'com.android.tools.build:gradle:2.2.0'
}
```

即可。

注意：gradle的版本指的是仓库中的gradle编译工具版本，不是gradle版本。

![](http://upload-images.jianshu.io/upload_images/9028834-8d3a3de7107487c0?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

再补充一点

build.gradle

```
// Top-level build file where you can add configuration options common to all sub-projects/modules.
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.2.0'

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}
allprojects {
    repositories {
        jcenter()
    }
}

android {
    compileSdkVersion 23
    buildToolsVersion '23.0.3'
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_7
        targetCompatibility JavaVersion.VERSION_1_7
    }
}
dependencies {
}
```

中

android { buildToolsVersion ‘23.0.3’ }

这个是在sdk的build-tools目录下

![](http://upload-images.jianshu.io/upload_images/9028834-d763e182a652926d?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

引用：
[Android Studio Error:Could not find com.android.tools.build:gradle](http://blog.csdn.net/chy555chy/article/details/52789696)



