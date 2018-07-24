

# 【Android 项目 应用安全】


转自：[如何保障Android应用安全](https://www.jianshu.com/p/bcf11bf96c1f)



在本文中，你将学习到一些提高Android应用安全级别的最佳实践。

在Android中安全是你无法保证的事情。作为一个开发者，你无法知道你的应用是否足够安全。每一个系统都会被破解但是你可以让攻击者的日子更加难熬。

## 网络

现在几乎所有的应用都会通过网络和服务器交换用户数据、令牌。

你应该考虑用户网络链接的安全以及怎么保护他们的数据不被盗取。



保证你的网络链接更加安全的第一步就是使用HTTPS，当然这还不够。

最流行的网络攻击之一就是[中间人攻击](https://link.jianshu.com?t=https://zh.wikipedia.org/wiki/%E4%B8%AD%E9%97%B4%E4%BA%BA%E6%94%BB%E5%87%BB)（MITM）。它可以是被动的也可以是主动的。

为了让你的应用免遭被动MITM攻击，你只需要使用[迪菲-赫尔曼密钥交换算法](https://link.jianshu.com?t=https://zh.wikipedia.org/wiki/%E8%BF%AA%E8%8F%B2-%E8%B5%AB%E7%88%BE%E6%9B%BC%E5%AF%86%E9%91%B0%E4%BA%A4%E6%8F%9B)（[视频介绍](https://link.jianshu.com?t=https://www.youtube.com/watch?v=MsqqpO9R5Hc)）就可以。

主动MITM攻击会强大一些。使用SSL锁定可以使你的数据免遭盗取。

有许多工具支持HTTPS和SSL锁定。[Retrofit](https://link.jianshu.com?t=https://square.github.io/retrofit/)和[OkHttp](https://link.jianshu.com?t=http://square.github.io/okhttp/)就是其中的两个，在UPTech我们几乎处处使用它们。

Retrofit使用起来非常简单，它还支持RxJava，也不需要花很长的时间来配置。

在OkHttp的帮助下，你还可以添加你自己信任的SSL证书。

> *“OkHttp默认是信任服务平台的证书授权的。证书认证可以提高安全性，但是它限制了服务端团队升级其TLS证书的能力。在没有得到服务端TLS管理员许可的情况下不要使用证书认证！”----Jesse Wilson, Square Inc*

关于SSL认证以及怎样安装你可以参考[这篇文章](https://link.jianshu.com?t=https://medium.com/@richa123/ssl-pinning-on-android-8baa822e3bd5#.4ehr004p0)。

## 意图（Intent）

你知道应用之间可以通过[Intent](https://link.jianshu.com?t=https://developer.android.com/reference/android/content/Intent.html)交互。Intent的类型有两种：显式的和隐式的。

### 显式意图

优点：不会被拦截。
缺点：只能在应用内部使用。

### 隐式意图

优点：可以在Android系统的任何地方使用。
缺点：很容易被拦截。

攻击者只需要简单的定义一个相同的intent-filter就可以拦截你的intent并获取所有数据。

为了避免这种情况，你应该尽可能的使用显式意图。例如，你可以指定包名。

```
Intent intent = new Intent(Intent.ACTION_SEND);
intent.setPackage("com.test.package");
sendBroadcast(intent);

```

不同外部模块交互的Broadcast Receiver或者Services不要暴露。

```
<service
    android:name=".service.SomeService"
    android:enabled="true"
    android:exported="false">

    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</service>

```

### 结论

在你的应用中尽可能的使用显式意图。明确的指定你想发送数据的接收者。

## 数据存储

### 内存缓存

内存缓存在这些场景下非常有用：当你从服务端获取到数据并想将这些数据保存在一段时间内有效，或者你在处理bitmaps 位图时想使用其他地方已经处理好的图片，或者你想保存一些用户数据，用户标识等等。

举个例子，怎么在内存缓存创建一个临时文件：

```
File outputDir = this.getCacheDir();
File outputFile = File.createTempFile("prefix", "extension", outputDir);

```

比起在外部存储器中打开一个文件就可以获取到密码，攻击者要dump内存则就难得多。Dump内存需要ROOT权限。
Android官方文档中关于内存缓存的说明可以[戳这里（以缓存图片为例）](https://link.jianshu.com?t=https://developer.android.com/training/displaying-bitmaps/cache-bitmap.html)。

### 加解密（Cipher）

此外，你可以在将数据存入缓存、数据库或者*SharedPreferences*之前将其加密。这里我推荐使用Cipher。

Cipher是一个内建的数据加密/解密工具。有关Cipher参数的更多信息请[戳这里](https://link.jianshu.com?t=https://docs.oracle.com/javase/7/docs/api/javax/crypto/Cipher.html)。

举一个使用Cipher的例子：

```
private static byte[] encrypt(byte[] key, byte[] input) throws Exception {
    SecretKeySpec skeySpec = new SecretKeySpec(key, "AES");
    Cipher cipher = Cipher.getInstance("AES");
    cipher.init(Cipher.ENCRYPT_MODE, skeySpec);
    byte[] encrypted = cipher.doFinal(input);
    return encrypted;
}

private static byte[] decrypt(byte[] key, byte[] encrypted) throws Exception {
    SecretKeySpec skeySpec = new SecretKeySpec(key, "AES");
    Cipher cipher = Cipher.getInstance("AES");
    cipher.init(Cipher.DECRYPT_MODE, skeySpec);
    byte[] decrypted = cipher.doFinal(encrypted);
    return decrypted;
}

```

你应该了解某些加密算法（*比如 AES*）需要固定的密钥长度，如128，192或者256bit。
更多Clipher所支持算法的详细信息请[戳这里](https://link.jianshu.com?t=https://docs.oracle.com/javase/7/docs/technotes/guides/security/StandardNames.html#Cipher)。

### Shared Preferences

和其他例子一样，需要加密所有的数据，不要将原始文本直接存储在Shared Preferences中。所有你想存放到Shared Preferences的数据都应该以*“MODE_PRIVATE”*属性写入，Android默认就是这个属性。

有了*“MODE_PRIVATE”*属性就能保证只有你的应用能够读写数据。当然如果没有ROOT权限谁也无法获取你的数据。

还有另外一个工具即SecureSharedPreferences，你可以在[这里](https://link.jianshu.com?t=https://github.com/scottyab/secure-preferences)了解更多的信息。

这个工具使用起来也很简单，它是在原生的Shared Preferences基础添加了很多加密算法。你不需要对它添加的新功能很了解，按照默认的Shared Preferences方式使用即可。

```
SharedPreferences prefs = new SecurePreferences(context, "userpassword", "my_user_prefs.xml");

```

这是它加密以后的样子：

```
<map>
    <string name="TuwbBU0IrAyL9znGBJ87uEi7pW0FwYwX8SZiiKnD2VZ7">
   pD2UhS2K2MNjWm8KzpFrag==:MWm7NgaEhvaxAvA9wASUl0HUHCVBWkn3c2T1WoSAE/g=rroijgeWEGRDFSS/hg
    </string>
    <string name="8lqCQqn73Uo84Rj">k73tlfVNYsPshll19ztma7U>
</map>

```

### 密钥

下一个问题就是将你加密算法的密钥存放在哪里。这是一个没有最佳答案的通用问题。不管你将密钥存放在哪里，攻击者总是能够找到它。唯一能够阻止攻击者破解应用的就是加密算法的复杂性和寻找密钥所需要的时间。



你可以使用KeyStore，但是只有在Android API 18以后的版本才支持。另外一个问题还是ROOT，获取到ROOT权限的攻击者可以获取到任何数据。

或者使用[Java Native Interface](https://link.jianshu.com?t=https://zh.wikipedia.org/wiki/Java%E6%9C%AC%E5%9C%B0%E6%8E%A5%E5%8F%A3)（JNI）。反编译C/C++编译出来的代码要困难的多。[Jadex](https://link.jianshu.com?t=https://github.com/skylot/jadx)和[dex2jar](https://link.jianshu.com?t=https://github.com/pxb1988/dex2jar)这种面向Java语言的反编译器这时就无法工作了。

还有一个可能的办法就是使用[隐写术](https://link.jianshu.com?t=https://zh.wikipedia.org/wiki/%E9%9A%90%E5%86%99%E6%9C%AF)算法，然后将密钥藏在任何你想藏的地方。例如：你可以将图片中的某些bit替换为你的密钥的bit。

上面描述的所有方法都可以为你的应用的“健康”增加时间。采取一些措施总比什么都不做要好。

### 结论

关于怎样隐藏或者加密密钥或者数据的算法数以万计。即使最好的、最变态的算法也无法保护你的数据。

最好的办法是不存储任何数据，但是有时是不可能的。

## 其他建议

关于增加应用安全性还有这些建议：

### 模拟器

检查你的应用是否正在模拟器上运行。你可以使用*android.os*包中的*Build*类来检查。它可以提供很多运行当前应用的设备的信息。如果有兴趣可以[戳这里](https://link.jianshu.com?t=https://github.com/framgia/android-emulator-detector)了解更多信息。

### Debug

检查当前设备是否有调试器连接。最简单的解决办法是：

```
Debug.isDebuggerConnected()

```

### Root

检查设备是否被root。你可以通过允许你使用终端的程序来检查设备是否已经被root。来看一个例子：

```
private static boolean isRooted() {
    return findBinary("su");
}

public static boolean findBinary(String binaryName) {
    boolean found = false;
    if (!found) {
        String[] places = {"/sbin/", "/system/bin/",
                "/system/xbin/", "/data/local/xbin/",
                "/data/local/bin/", "/system/sd/xbin/",
                "/system/bin/failsafe/", "/data/local/"};
        for (String where : places) {
            if (new File(where + binaryName).exists()) {
                found = true;
                break;
            }
        }
    }
    return found;
}

```

这段代码摘自[RootTools](https://link.jianshu.com?t=https://github.com/Stericson/RootTools)这个库。

*su*命令常常用于将当前用户的权限修改为root用户。[戳这里](https://link.jianshu.com?t=http://www.linfo.org/su.html)可以了解更多关于*su*的信息。

一旦这个检测到设备已经被root，通知服务器，暂停连接，中断服务。但是不要做的太过，以至损害用户体验，造成用户流失。

### 字符串

使用字符数组而不是使用字符串，或者使用 *new* 操作符来创建一个字符串，这样新创建的字符串是在*堆（heap）*里而不是在*字符串池（string pool）*里。

```
String a = new String("a");
a = new String("a");

```

在上面的例子中会创建两个对象。在第二行代码被执行后，垃圾回收器（GC）就会回收第一个对象。

```
String a = "a";
String b = "a";

```

在这个例子中，只会创建一个对象。因为使用了字符串，对象会保存在*字符串池（string pool）*中，垃圾回收器暂时还不会回收它。

### 混淆

使用[ProGuard](https://link.jianshu.com?t=https://www.guardsquare.com/en/proguard)，[DexGuard](https://link.jianshu.com?t=https://www.guardsquare.com/en/dexguard)或者[DexProtector](https://link.jianshu.com?t=https://dexprotector.com/)等混淆器。

ProGuard是Android Studio默认的代码精简工具，它也可以混淆你的代码。在创建新的Android Studio工程时会自动生成一个ProGuard配置文件。在你的工程中是以proguard-rules.pro命名的。

在这个文件中你可以指定编译APK时需要执行的规则。你也可以在[这里](https://link.jianshu.com?t=https://github.com/krschultz/android-proguard-snippets/tree/master/libraries)发现流行库所使用的一些规则。

开启ProGuard你需要这样做：

```
android {
   buildTypes {
      dev {
          minifyEnabled true  // enables ProGuard
          proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
      }
}

```

使用混淆你可以让攻击者困惑从而花更多的时间来破解你的应用。

有许多公司提供混淆工具。[这里](https://link.jianshu.com?t=http://proguard.sourceforge.net/alternatives.html)有一份最流行的混淆器的清单。其中最好的那个相当的贵。它们能把你的应用的代码逻辑转换得晦涩难懂。不仅如此，花更多的钱你还可以开启修改root/debug/emulator/MITM/Code等选项的功能。

## 一些有用的链接

[Android 关于安全的文档](https://link.jianshu.com?t=https://source.android.com/security/index.html)

[移动应用排名前十的风险](https://link.jianshu.com?t=https://www.owasp.org/index.php/OWASP_Mobile_Security_Project#tab=Top_10_Mobile_Risks)

[安全测试工具](https://link.jianshu.com?t=https://www.qdtricks.net/android-penetration-testing-apps/)

## 总结

我尽力在这篇8分钟长的文章里提供大量的信息。希望你能有所收获。并不是所有的方式你都需要严格遵循，但是你可以尝试一些新的东西（或者不是新的 :)）。

关于Android安全再啰嗦两句。在攻击者获得ROOT权限之前，每一条建议都很有用。一旦ROOT权限击溃你所有的保护，再无回天之力。希望未来发布的Android系统会更加安全。

如果你有任何问题、建议或者你知道某一领域我可以研究的都请告诉我。谢谢！

> 文本译自[How To Make Your Android Application Secured](https://link.jianshu.com?t=https://medium.com/uptech-team/how-to-make-your-android-application-secured-21c054b371e7#.65258ybeb)

