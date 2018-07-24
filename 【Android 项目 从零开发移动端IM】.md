

# 【Android 项目 从零开发移动端IM】
转自：[新手入门一篇就够：从零开发移动端IM](http://www.52im.net/thread-464-1-1.html)

## 一、前言

IM发展至今，已是非常重要的互联网应用形态之一，尤其移动互联网时代，它正以无与论比的优势降低了沟通成本和交流门槛，对各种应用形态产生了深远影响。

做为IM开发者或即将成为IM开发者的技术人员，IM的价值和重要性不言自明。但从技术实现来说，IM系统的开发（尤其是移动端IM）还是存在许多技术难点和坑点的。也正因如此，优质的IM开发资料、实践成果，对于没有太多技术储备的新手来说，尤其难以获得。

本文将以新手的视角引导你阅读相关文章，便于你从零开发一个移动端IM做好方方面面的知识准备：包括但不限于网络编程基础、通信协议的选型、IM的架构设计等等。文笔有限，如有不妥之处还请批评指正，希望对你有用。

## 二、读完本文的收获

### 1您将获得

本文将假设你是毫无技术准备的新手，引导你通过一篇篇精选的文章，了解如何从零开发一个移动端IM所需要的各种技术、资料和实践性代码。

### 2您无法获得

鉴于IM技术的复杂性，IM开发相关的技术不是一篇文章所能展现的完整，限于篇幅原因本文将不包含任何实践性代码、也尽量不对某项技术作深入的展开，相关的实践性代码、资料、技术详解等请依据本文作者准备的文章逐个深入阅读和学习，而这也恰恰是本文想达到的目的。

## 三、题外话

随着近两年IM云服务的发展，很多团队基于种种原因，直接选择了短平快的云IM接入APP中。然而，考虑到云IM无论从商业模式还是运营模式上，还需经过多年的沉淀，才可能真正实现客户与服务商的运营和服务良性循环的双赢局面。因则，如何选择云IM服务商，这就是个头疼的问题了，不过这不是本文将要讨论的重点，如果需要，你也可以加入本文提到的讨论交流群，与大家一起交流群：![image](http://upload-images.jianshu.io/upload_images/9028834-710f3bb8df6c9eb3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 [215891622](http://shang.qq.com/wpa/qunwpa?idkey=4cc788473d261129ab3ded26fbb22168d0fa52c799d28f92a8f193dc36865bcb)。 

好了，以下是正文内容。

## 四、网络编程理论准备

即时通讯技术归根结底还是网络编程技术的应用，如果您很好奇承载这些网络协议的物理设备是怎么工作的，可以先看看《[网络编程懒人入门(六)：史上最通俗的集线器、交换机、路由器功能原理入门](http://www.52im.net/thread-1629-1-1.html)》。

### 1UDP、TCP理论基础

我们都知道，IM系统的业务本质就是客户端与客户端进行消息的实时传递，而技术基础就是基于Socket连接的实时数据读写，那么基本的网络编程理论基础是作为新手的你必须掌握的知识点。当然，作为IM开发来说，基础的网络理论就够用了，也没有必要像网络工程师一样精通所谓的OSI七层参考模型。

**如果你还不知道什么是UDP、TCP协议，请阅读以下文章：**

*   《[TCP/IP详解](http://www.52im.net/topic-tcpipvol1.html) - [第11章·UDP：用户数据报协议](http://docs.52im.net/extend/docs/book/tcpip/vol1/11/)》
*   《[TCP/IP详解](http://www.52im.net/topic-tcpipvol1.html) - [第17章·TCP：传输控制协议](http://docs.52im.net/extend/docs/book/tcpip/vol1/17/)》
*   《[TCP/IP详解](http://www.52im.net/topic-tcpipvol1.html) - [第18章·TCP连接的建立与终止](http://docs.52im.net/extend/docs/book/tcpip/vol1/18/)》
*   《[TCP/IP详解](http://www.52im.net/topic-tcpipvol1.html) - [第21章·TCP的超时与重传](http://docs.52im.net/extend/docs/book/tcpip/vol1/21/)》
*   《[网络编程懒人入门(一)：快速理解网络通信协议（上篇）](http://www.52im.net/thread-1095-1-1.html)》
*   《[网络编程懒人入门(二)：快速理解网络通信协议（下篇）](http://www.52im.net/thread-1103-1-1.html)》
*   《[网络编程懒人入门(三)：快速理解TCP协议一篇就够](http://www.52im.net/thread-1107-1-1.html)》
*   《[网络编程懒人入门(四)：快速理解TCP和UDP的差异](http://www.52im.net/thread-1160-1-1.html)》。

这几篇文章有助于对UDP、TCP协议建立基本的认识，当然如果时间允许，能全书阅读网络编程理论经典《[TCP/IP详解 卷1：协议](http://www.52im.net/topic-tcpipvol1.html)》则再好不过了。另外，UDP、TCP作为基础计算机数据传输协议，在其之上衍生了很多应用层协议，相关的协议族关系图可以在此文中找到：《[计算机网络通讯协议关系图（中文珍藏版）](http://www.52im.net/thread-180-1-1.html)》，可作为您日常的备查手册使用。

### 2深入理解TCP传输协议

透彻理解TCP传输协议的连接和断开过程非常有助于您日后IM算法的优化和实现，这个过程被形象的总结为“3次握手与4次挥手”。

**以下文章有助于您深入理解之：**

*   《[理论经典：TCP协议的3次握手与4次挥手过程详解](http://www.52im.net/thread-258-1-1.html)》
*   《[理论联系实际：Wireshark抓包分析TCP 3次握手、4次挥手过程](http://www.52im.net/thread-275-1-1.html)》
*   《[通俗易懂-深入理解TCP协议（上）：理论基础](http://www.52im.net/thread-513-1-1.html)》
*   《[通俗易懂-深入理解TCP协议（下）：RTT、滑动窗口、拥塞处理](http://www.52im.net/thread-515-1-1.html)》
*   《[不为人知的网络编程(一)：浅析TCP协议中的疑难杂症(上篇)](http://www.52im.net/thread-1003-1-1.html)》
*   《[不为人知的网络编程(二)：浅析TCP协议中的疑难杂症(下篇)](http://www.52im.net/thread-1004-1-1.html)》
*   《[不为人知的网络编程(三)：关闭TCP连接时为什么会TIME_WAIT、CLOSE_WAIT](http://www.52im.net/thread-1007-1-1.html)》
*   《[不为人知的网络编程(四)：深入研究分析TCP的异常关闭](http://www.52im.net/thread-1014-1-1.html)》。

### 3深入理解UDP传输协议

相比TCP协议，UDP数据传输协议就显得非常轻量和易于理解，UDP通常被用于需要快速响应的数据传输场景下，对应于IM中的应用形态有：P2P通信、实时音视频等。另外，通常的IM都会被应用于互联网上（而非局域网），那么了解所谓的NAT路由技术原理等，也将有助于您对P2P打洞、UDP端口老化等概念有一个清楚的认知。

**以下文章有助于您在接下来开发IM的实际应用中提供一定的实践依据：**

*   《[UDP中一个包的大小最大能多大？](http://www.52im.net/thread-29-1-1.html)》
*   《[NAT详解：基本原理、穿越技术(P2P打洞)、端口老化等](http://www.52im.net/thread-50-1-1.html)》
*   《[不为人知的网络编程(五)：UDP的连接性和负载均衡](http://www.52im.net/thread-1018-1-1.html)》
*   《[不为人知的网络编程(六)：深入地理解UDP协议并用好它](http://www.52im.net/thread-1024-1-1.html)》。

### 4深入理解基于广域网的互联网编程知识

未来您所编写的网络应用程序或产品基本都要运行基于广域网的互联网中（而非单纯而简单的局域网），而广域网因物理网络的复杂性，要编写能适应各种网络拓扑的程序代码，需要过硬的广域网络基础知识，比如NAT路由转发、P2P打洞等等。

**下面的文章，有助于您深入理解NAT原理、点对点通信等方面的知识：**

《[P2P技术详解(一)：NAT详解——详细原理、P2P简介](http://www.52im.net/thread-50-1-1.html)》
《[P2P技术详解(二)：P2P中的NAT穿越(打洞)方案详解](http://www.52im.net/thread-542-1-1.html)》
《[P2P技术详解(三)：P2P技术之STUN、TURN、ICE详解](http://www.52im.net/thread-557-1-1.html)》

### 5有关高性能网络编程的知识

实际上，大用户量高并发生产环境下的网络编程，你所开发的产品要想真正堪用并非易事，你很有必要了解高性能网络编程领域所关注的热点问题和努力的方向。

**以下是即时通讯网整理的高性能网络编程领域的系列文章：**

《[高性能网络编程(一)：单台服务器并发TCP连接数到底可以有多少](http://www.52im.net/thread-561-1-1.html)》
《[高性能网络编程(二)：上一个10年，著名的C10K并发连接问题](http://www.52im.net/thread-566-1-1.html)》
《[高性能网络编程(三)：下一个10年，是时候考虑C10M并发问题了](http://www.52im.net/thread-568-1-1.html)》

好消息是，现时的网络编程为了解决高性能问题，有很多成型的Socket应用层模式存在，比如：NIO、AIO等，文章《[Java新一代网络编程模型AIO原理及Linux系统AIO介绍](http://www.52im.net/thread-306-1-1.html)》简单介绍了传统的阻塞式IO、NIO，并着重介绍了最新的AIO技术，如有时间您很有必要予以了解。（更多同类文章：[点此进入…](http://www.52im.net/forum.php?mod=collection&action=view&ctid=9)）

### 6深入理解移动互联网络的特性

移动互联网络因为无线通信的复杂性：慢、高延迟、易受干扰、带宽窄等特点，使得与传统的有线网络下的应用功能实现有明显差异，您很有必要详细了解一下无线网络方面的特性。

**以下文章有助于加深您对移动网络特性的理解：**
《[移动端IM开发者必读(一)：通俗易懂，理解移动网络的“弱”和“慢”](http://www.52im.net/thread-1587-1-1.html)》
《[移动端IM开发者必读(二)：史上最全移动弱网络优化方法总结](http://www.52im.net/thread-1588-1-1.html)》
《[现代移动端网络短连接的优化手段总结：请求速度、弱网适应、安全保障](http://www.52im.net/thread-1413-1-1.html)》

## 五、网络编程基础实践

如果你认真读完了上一层的文章，是时候写些代码，来理论联系实际理解Socket通信的原理和实践了。

**有关TCP的Socket通信Demo文章和代码：**

*   《[网络编程懒人入门(八)：手把手教你写基于TCP的Socket长连接](http://www.52im.net/thread-1722-1-1.html)》
*   《[Android端与服务端基于TCP协议的Socket通讯](http://blog.csdn.net/ryantang03/article/details/8274517)》
*   iOS平台的[CocoaAsyncSocket](https://github.com/52im/CocoaAsyncSocket)托管代码中有许多TCP的官方Demo代码，值得一看。

当然，以上只是随手找的Demo代码，网络上有关TCP数据通信的演示性代码很容易找到，在此就不过多举例了。

**本文作者专门编写的有关跨移动端平台的UDP Socket通信Demo：**

*   《[NIO框架入门(一)：服务端基于Netty4的UDP双向通信Demo演示](http://www.52im.net/thread-367-1-1.html)》
*   《[NIO框架入门(二)：服务端基于MINA2的UDP双向通信Demo演示](http://www.52im.net/thread-373-1-1.html)》
*   《[NIO框架入门(三)：iOS与MINA2、Netty4的跨平台UDP双向通信实战](http://www.52im.net/thread-378-1-1.html)》
*   《[NIO框架入门(四)：Android与MINA2、Netty4的跨平台UDP双向通信实战](http://www.52im.net/thread-388-1-1.html)》。

## 六、IM到底该用UDP还是TCP协议？

好了，上面的网络编程基础掌握后，就要开始为你的IM进行传输协议选型了。说到IM该用UDP还是TCP作为传输协议，这是个颇有争议的话题，各大社区每当此问题的出现必定是大片的不同声音。

当然，UDP和TCP各有各的应用场景，作为IM来说，早期的IM因为服务端资源（服务器硬件、网络带宽等）比较昂贵且没有更好的办法来分担性能负载，所以很多时候会考虑使用UDP，这其中主要是早期的QQ为代表。

时至今日，TCP的服务端负载已经有了很好的解决方案，加之服务器资源成本的下降，目前很多IM、消息推送解决方案也都在使用TCP作为传输层协议。不过，UDP也并未排除在IM、消息推送的解决方案之外，比如：弱网络通信（包括跨国的高延迟网络环境）、物联网通信、IM中的实时音视频通信等等场景下，UDP依然是首选项。

**以下文章或许有助于您对传输层协议的选型：**

*   《[简述传输层协议TCP和UDP的区别](http://www.52im.net/thread-580-1-1.html)》
*   《[为什么QQ用的是UDP协议而不是TCP协议？](http://www.52im.net/thread-279-1-1.html)》
*   《[移动端即时通讯协议选择：UDP还是TCP？](http://www.52im.net/thread-33-1-1.html)》
*   《[网络编程懒人入门(五)：快速理解为什么说UDP有时比TCP更有优势](http://www.52im.net/thread-1277-1-1.html)》
*   《[微信对网络影响的技术试验及分析（论文全文）](http://www.52im.net/thread-195-1-1.html)》
*   《[为何基于TCP协议的移动端IM仍然需要心跳保活机制？](http://www.52im.net/thread-281-1-1.html)》。

当然，关于IM到底该选择UDP还是TCP，这是个仁者见仁智者见智的问题，没有必要过于纠结，请从您的IM整体应用场景、开发代价、部署和运营成本等方面综合考虑，相信能找到你要的答案。

## 七、IM的数据通信格式选型

IM应用开发的前期技术选型时，关于数据通信格式的选择，在同行的眼里，同样是个极富争议的话题。

**精略分析一下，究其原因，大概在于以下几点：**

*   可选择的协议或封装格式多种多样：
    可选择的余地大：XMPP、Protobuf、JSON、私有2进制、MQTT、定格化XML、Plain text等等；
*   同一种格式并不能适用于大多数的场景：
    不同的场景有同的考虑而协议的选择往往跟这挂钩在一起的，如：移动端IM或移动端消息推送的应用场景下如果用XMPP协议，多数情况下都会被喷；
*   开发者对所选格式有各自的偏好：
    有的人或团队对某种或某几种格式有不一样的经验和技术积累，也促成了他们对某种或某几种协议的偏好。

该选什么样的数据通信格式，同样是跟你的应用场景和使用的架构方案相关联。不过，目前以作者掌握的信息看来，作为需要运行在移动设备的IM，几乎目前所有主流讨论里都不建议使用XMPP协议，具体原因就不在此展开了，下面推荐的文章里会详细为你解答原因。

**以下文章会对你的IM的数据通信格式选型有所帮助：**

*   《[如何选择即时通讯应用的数据传输格式](http://www.52im.net/thread-276-1-1.html)》
*   《[Protobuf通信协议详解：代码演示、详细原理介绍等](http://www.52im.net/thread-323-1-1.html)》
*   《[强列建议将Protobuf作为你的即时通讯应用数据传输格式](http://www.52im.net/thread-277-1-1.html)》
*   《[全方位评测：Protobuf性能到底有没有比JSON快5倍？](http://www.52im.net/thread-772-1-1.html)》
*   《[移动端IM开发需要面对的技术问题（含通信协议选择）](http://www.52im.net/thread-133-1-1.html)》
*   《[简述移动端IM开发的那些坑：架构设计、通信协议和客户端](http://www.52im.net/thread-289-1-1.html)》
*   《[理论联系实际：一套典型的IM通信协议设计详解](http://www.52im.net/thread-283-1-1.html)》
*   《[58到家实时消息系统的协议设计等技术实践分享](http://www.52im.net/thread-298-1-1.html)》
*   《[金蝶随手记团队分享：还在用JSON? Protobuf让数据传输更省更快(原理篇)](http://www.52im.net/thread-1510-1-1.html)》
*   《[扫盲贴：认识MQTT通信协议](http://www.52im.net/thread-318-1-1.html)》。

（更多同类文章：[点此查看…](http://www.52im.net/forum.php?mod=collection&action=view&ctid=18)）

## 八、移动端IM的心跳保活和后台消息推送

### 1为什么需要心跳保活？

由于移动网络的复杂性，心跳保活对于移动端IM来说显的尤为重要，加之手机省电、省流量策略的设计，如何实现心跳保活则也非常重要，文章《[基于TCP协议的移动端IM仍然需要心跳保活机制](http://www.52im.net/thread-281-1-1.html)》或许可以解答你的疑问。

### 2iOS端的后台消息推送

因为iOS平台的特殊性，iOS应用一旦退到后台，应用本身是无法用代码来实现网络保活的，也就无法自行实现后台消息推送了。

**以下文章将有助于你理解iOS平台的后台消息推送原理：**

*   《[iOS的推送服务APNs详解：设计思路、技术原理及缺陷等](http://www.52im.net/thread-345-1-1.html)》
*   《[扫盲贴：浅谈iOS和Android后台实时消息推送的原理和区别](http://www.52im.net/thread-286-1-1.html)》。

### 3Android端的心跳保活和后台消息推送

鉴于Android平台众所周之的分化和互不兼容问题，Android端IM在处理心跳保活和后台消息推送时，遇到了不少的麻烦。而且，由于Android应用的生命周期管理是由系统控制，因而如何保证您的IM所在进程或后台服务不被系统杀死，是实现心跳保活和后台消息推送的实现基础。

**以下文章可为你的Android端IM的心跳保活和后台推送方案的设计提供参考：**

*   《[应用保活终极总结(一)：Android6.0以下的双进程守护保活实践](http://www.52im.net/thread-1135-1-1.html)》
*   《[应用保活终极总结(二)：Android6.0及以上的保活实践(进程防杀篇)](http://www.52im.net/thread-1138-1-1.html)》
*   《[应用保活终极总结(三)：Android6.0及以上的保活实践(被杀复活篇)](http://www.52im.net/thread-1140-1-1.html)》
*   《[Android进程保活详解：一篇文章解决你的所有疑问](http://www.52im.net/thread-438-1-1.html)》
*   《[深入的聊聊Android消息推送这件小事](http://www.52im.net/thread-771-1-1.html)》
*   《[Android端消息推送总结：实现原理、心跳保活、遇到的问题等](http://www.52im.net/thread-341-1-1.html)》
*   《[为何基于TCP协议的移动端IM仍然需要心跳保活机制？](http://www.52im.net/thread-281-1-1.html)》
*   《[微信团队原创分享：Android版微信后台保活实战分享(进程保活篇)](http://www.52im.net/thread-210-1-1.html)》
*   《[微信团队原创分享：Android版微信后台保活实战分享(网络保活篇)](http://www.52im.net/thread-209-1-1.html)》
*   《[移动端IM实践：实现Android版微信的智能心跳机制](http://www.52im.net/thread-120-1-1.html)》
*   《[移动端IM实践：WhatsApp、Line、微信的心跳策略分析](http://www.52im.net/thread-121-1-1.html)》

（更多同类文章：[此进入…](http://www.52im.net/forum.php?mod=collection&action=view&ctid=17)）

## 九、移动端IM系统的架构设计

IM其本质是一套消息发送与投递系统，或者说是一套网络通信系统，归根结底就是两个词：存储与转发。但一个成熟的移动端IM系统要想正常运转，涉及的内容则远不止这些，而最考验技术功底的就是服务端架构的设计与实现。

没有过IM系统开发经验的人，可能对以上观点嗤之以鼻，在此借用TeamTalk的设计者的一段话：“IM服务器开发，从功能抽象的角度看可能非常简单，可以认为是管理大量的客户端连接和在不同的客户端之间传递消息，但具体到实现细节就比较复杂了。打个不恰当的比喻，OS的功能抽象也非常简单，无非是进程间的调度和硬件资源的管理，但要是自己去实现一个，一般人也就只能呵呵了。”

我们以一个典型方案为例，首先来提炼一下一个IM系统的主要需求：包括账号、关系链、在线状态显示、消息交互（文本、图片、语音）、实时视频电话......。

**要处理好上述需求，我们通常需要从以下方面进行考量从而设计出合适的架构：**

*   如果采用可靠传输协议TCP，需要考虑到负载问题：短连接实现账号、关系链相关业务，长连接实现上线、信息推送；
*   后台架构的灵活性、可扩展性：支持分布式部署——把网络层、业务逻辑层、数据层分离，网络层和业务层支持负载均衡策略、数据层支持分布式存储；
*   客户端SDK的易用性：把网络层、数据层分离、业务逻辑层分离。

**另外，一个典型的IM系统架构设计，还有以下性能方面的热点问题需要设计者重点关注：**

*   **编码角度：**采用高效的网络模型，线程模型，I/O处理模型，合理的数据库设计和操作语句的优化；
*   **垂直扩展：**通过提高单服务器的硬件资源或者网络资源来提高性能；
*   **水平扩展：**通过合理的架构设计和运维方面的负载均衡策略将负载分担，有效提高性能；后期甚至可以考虑加入数据缓存层，突破IO瓶颈；
*   **系统的高可用性：**防止单点故障；
*   在架构设计时做到业务处理和数据的分离，从而依赖分布式的部署使得在单点故障时能保证系统可用。
*   对于关键独立节点可以采用双机热备技术进行切换。
*   数据库数据的安全性可以通过磁盘阵列的冗余配置和主备数据库来解决。

鉴于篇幅有限，架构设计方面的内容本文就不深入展开了。

**以下文章将为你的移动端IM的架构设计带来一定的参考意义：**

*   《[浅谈IM系统的架构设计](http://www.52im.net/thread-307-1-1.html)》
*   《[简述移动端IM开发的那些坑：架构设计、通信协议和客户端](http://www.52im.net/thread-289-1-1.html)》
*   《[一套海量在线用户的移动端IM架构设计实践分享(含详细图文)](http://www.52im.net/thread-812-1-1.html)》
*   《[一套原创分布式即时通讯(IM)系统理论架构方案](http://www.52im.net/thread-151-1-1.html)》
*   《[从零到卓越：京东客服即时通讯系统的技术架构演进历程](http://www.52im.net/thread-152-1-1.html)》
*   《[蘑菇街即时通讯/IM服务器开发之架构选择](http://www.52im.net/thread-31-1-1.html)》
*   《[腾讯QQ1.4亿在线用户的技术挑战和架构演进之路PPT](http://www.52im.net/thread-158-1-1.html)》
*   《[微信技术总监谈架构：微信之道——大道至简(演讲全文)](http://www.52im.net/thread-200-1-1.html)》
*   《[如何解读《微信技术总监谈架构：微信之道——大道至简》](http://www.52im.net/thread-201-1-1.html)》
*   《[快速裂变：见证微信强大后台架构从0到1的演进历程（一）](http://www.52im.net/thread-168-1-1.html)》
*   《[17年的实践：腾讯海量产品的技术方法论](http://www.52im.net/thread-159-1-1.html)》。

（更多同类文章： [点此进入…](http://www.52im.net/forum.php?mod=collection&action=view&ctid=7)）

## 十、移动端IM的通信安全

IM（尤其移动端IM）的安全性一直是开发者需要优先考虑的基础问题，如何正确地理解和使用加密技术则显的尤其重要。IM系统大都采用C/S、B/S、P2P等技术来实现即时通信的功能，软件编制没有统一的标准，使得IM系统本身存有多种安全漏洞，加上用户缺乏安全意识，导致在使用即时通信系统时出现各种安全问题。

当今的计算机密码学的主要作用有：加密（ Encryption）、认证（Authentication），鉴定（Identification） 。

**加密：**防止坏人获取你的数据。 
**认证：**防止坏人修改了你的数据而你却并没有发现。 
**鉴权：**防止坏人假冒你的身份。

这些基本概念和加密算法原理就不在此展开叙述了。

**以下文章或许有助于您设计出安全的移动端IM系统：**

*   《[即时通讯安全篇（一）：正确地理解和使用Android端加密算法](http://www.52im.net/thread-216-1-1.html)》
*   《[即时通讯安全篇（二）：探讨组合加密算法在IM中的应用](http://www.52im.net/thread-217-1-1.html)》
*   《[即时通讯安全篇（三）：常用加解密算法与通讯安全讲解](http://www.52im.net/thread-219-1-1.html)》
*   《[即时通讯安全篇（四）：实例分析Android中密钥硬编码的风险](http://www.52im.net/thread-312-1-1.html)》
*   《[即时通讯安全篇（五）：对称加密技术在Android平台上的应用实践](http://www.52im.net/thread-642-1-1.html)》
*   《[即时通讯安全篇（六）：非对称加密技术的原理与应用实践](http://www.52im.net/thread-653-1-1.html)》
*   《[传输层安全协议SSL/TLS的Java平台实现简介和Demo演示](http://www.52im.net/thread-327-1-1.html)》
*   《[理论联系实际：一套典型的IM通信协议设计详解（含安全层设计）](http://www.52im.net/thread-283-1-1.html)》
*   《[微信新一代通信安全解决方案：基于TLS1.3的MMTLS详解](http://www.52im.net/thread-310-1-1.html)》
*   《[来自阿里OpenIM：打造安全可靠即时通讯服务的技术实践分享](http://www.52im.net/thread-215-1-1.html)》
*   《[简述实时音视频聊天中端到端加密（E2EE）的工作原理](http://www.52im.net/thread-763-1-1.html)》
*   《[移动端安全通信的利器——端到端加密（E2EE）技术详解](http://www.52im.net/thread-764-1-1.html)》
*   《[Web端即时通讯安全：跨站点WebSocket劫持漏洞详解(含示例代码)](http://www.52im.net/thread-793-1-1.html)》
*   《[通俗易懂：一篇掌握即时通讯的消息传输安全原理](http://www.52im.net/thread-970-1-1.html)》

（更多同类文章：[点此进入…](http://www.52im.net/forum.php?mod=collection&action=view&ctid=6)）

## 十一、有关IM中的实时音视频技术

IM应用中的实时音视频技术，几乎是IM开发中的最后一道高墙。原因在于：实时音视频技术 = 音视频处理技术 + 网络传输技术 的横向技术应用集合体，而公共互联网不是为了实时通信设计的。实时音视频技术上的实现内容主要包括：音视频的采集、编码、网络传输、解码、播放等环节。这么多项并不简单的技术应用，如果把握不当，将会在在实际开发过程中遇到一个又一个的坑。

**以下文章有助于您从零理解IM的实时音视频开发的方方面面：**

*   《[即时通讯音视频开发（一）：视频编解码之理论概述](http://www.52im.net/thread-228-1-1.html)》
*   《[即时通讯音视频开发（二）：视频编解码之数字视频介绍](http://www.52im.net/thread-229-1-1.html)》
*   《[即时通讯音视频开发（三）：视频编解码之编码基础](http://www.52im.net/thread-232-1-1.html)》
*   《[即时通讯音视频开发（四）：视频编解码之预测技术介绍](http://www.52im.net/thread-235-1-1.html)》
*   《[即时通讯音视频开发（五）：认识主流视频编码技术H.264](http://www.52im.net/thread-237-1-1.html)》
*   《[即时通讯音视频开发（六）：如何开始音频编解码技术的学习](http://www.52im.net/thread-241-1-1.html)》
*   《[即时通讯音视频开发（七）：音频基础及编码原理入门](http://www.52im.net/thread-242-1-1.html)》
*   《[即时通讯音视频开发（八）：常见的实时语音通讯编码标准](http://www.52im.net/thread-243-1-1.html)》
*   《[即时通讯音视频开发（九）：实时语音通讯的回音及回音消除概述](http://www.52im.net/thread-247-1-1.html)》
*   《[即时通讯音视频开发（十）：实时语音通讯的回音消除技术详解](http://www.52im.net/thread-250-1-1.html)》
*   《[即时通讯音视频开发（十一）：实时语音通讯丢包补偿技术详解](http://www.52im.net/thread-251-1-1.html)》
*   《[即时通讯音视频开发（十二）：多人实时音视频聊天架构探讨](http://www.52im.net/thread-253-1-1.html)》
*   《[即时通讯音视频开发（十三）：实时视频编码H.264的特点与优势](http://www.52im.net/thread-266-1-1.html)》
*   《[即时通讯音视频开发（十四）：实时音视频数据传输协议介绍](http://www.52im.net/thread-267-1-1.html)》
*   《[即时通讯音视频开发（十五）：聊聊P2P与实时音视频的应用情况](http://www.52im.net/thread-269-1-1.html)》
*   《[即时通讯音视频开发（十六）：移动端实时音视频开发的几个建议](http://www.52im.net/thread-270-1-1.html)》
*   《[即时通讯音视频开发（十七）：视频编码H.264、VP8的前世今生](http://www.52im.net/thread-274-1-1.html)》
*   《[实时语音聊天中的音频处理与编码压缩技术简述](http://www.52im.net/thread-825-1-1.html)》
*   《[网易视频云技术分享：音频处理与压缩技术快速入门](http://www.52im.net/thread-678-1-1.html)》
*   《[学习RFC3550：RTP/RTCP实时传输协议基础知识](http://www.52im.net/thread-590-1-1.html)》
*   《[基于RTMP数据传输协议的实时流媒体技术研究（论文全文）](http://www.52im.net/thread-273-1-1.html)》
*   《[声网架构师谈实时音视频云的实现难点(视频采访)](http://www.52im.net/thread-399-1-1.html)》
*   《[浅谈开发实时视频直播平台的技术要点](http://www.52im.net/thread-475-1-1.html)》
*   《[还在靠“喂喂喂”测试实时语音通话质量？本文教你科学的评测方法！](http://www.52im.net/thread-507-1-1.html)》
*   《[实现延迟低于500毫秒的1080P实时音视频直播的实践分享](http://www.52im.net/thread-528-1-1.html)》
*   《[移动端实时视频直播技术实践：如何做到实时秒开、流畅不卡](http://www.52im.net/thread-530-1-1.html)》
*   《[如何用最简单的方法测试你的实时音视频方案](http://www.52im.net/thread-535-1-1.html)》
*   《[技术揭秘：支持百万级粉丝互动的Facebook实时视频直播](http://www.52im.net/thread-541-1-1.html)》
*   《[简述实时音视频聊天中端到端加密（E2EE）的工作原理](http://www.52im.net/thread-763-1-1.html)》
*   《[移动端实时音视频直播技术详解（一）：开篇](http://www.52im.net/thread-853-1-1.html)》
*   《[移动端实时音视频直播技术详解（二）：采集](http://www.52im.net/thread-955-1-1.html)》
*   《[移动端实时音视频直播技术详解（三）：处理](http://www.52im.net/thread-960-1-1.html)》
*   《[移动端实时音视频直播技术详解（四）：编码和封装](http://www.52im.net/thread-965-1-1.html)》
*   《[移动端实时音视频直播技术详解（五）：推流和传输](http://www.52im.net/thread-967-1-1.html)》
*   《[移动端实时音视频直播技术详解（六）：延迟优化](http://www.52im.net/thread-972-1-1.html)》
*   《[理论联系实际：实现一个简单地基于HTML5的实时视频直播](http://www.52im.net/thread-875-1-1.html)》
*   《[IM实时音视频聊天时的回声消除技术详解](http://www.52im.net/thread-939-1-1.html)》
*   《[浅谈实时音视频直播中直接影响用户体验的几项关键技术指标](http://www.52im.net/thread-953-1-1.html)》
*   《[如何优化传输机制来实现实时音视频的超低延迟？](http://www.52im.net/thread-1008-1-1.html)》
*   《[首次披露：快手是如何做到百万观众同场看直播仍能秒开且不卡顿的？](http://www.52im.net/thread-1033-1-1.html)》
*   《[Android直播入门实践：动手搭建一套简单的直播系统](http://www.52im.net/thread-1154-1-1.html)》
*   《[网易云信实时视频直播在TCP数据传输层的一些优化思路](http://www.52im.net/thread-1254-1-1.html)》
*   《[实时音视频聊天技术分享：面向不可靠网络的抗丢包编解码器](http://www.52im.net/thread-1281-1-1.html)》
*   《[P2P技术如何将实时视频直播带宽降低75%？](http://www.52im.net/thread-1289-1-1.html)》
*   《[专访微信视频技术负责人：微信实时视频聊天技术的演进](http://www.52im.net/thread-1201-1-1.html)》
*   《[腾讯音视频实验室：使用AI黑科技实现超低码率的高清实时视频聊天](http://www.52im.net/thread-1308-1-1.html)》
*   《[微信团队分享：微信每日亿次实时音视频聊天背后的技术解密](http://www.52im.net/thread-1311-1-1.html)》

（更多同类文章： [点此进入…](http://www.52im.net/forum.php?mod=collection&action=view&ctid=4)）

**另外，大家都喜欢开源免费的东西，WebRTC是为数不多的开源且质量上乘的实时音视频解决方案之一，资料如下：**

*   《[开源实时音视频技术WebRTC的现状](http://www.52im.net/article-126-1.html)》
*   《[简述开源实时音视频技术WebRTC的优缺点](http://www.52im.net/thread-225-1-1.html)》
*   《[访谈WebRTC标准之父：WebRTC的过去、现在和未来](http://www.52im.net/thread-227-1-1.html)》
*   《[良心分享：WebRTC 零基础开发者教程（中文）[附件下载]](http://www.52im.net/thread-265-1-1.html)》
*   《[WebRTC实时音视频技术的整体架构介绍](http://www.52im.net/thread-284-1-1.html)》
*   《[新手入门：到底什么是WebRTC服务器，以及它是如何联接通话的？](http://www.52im.net/thread-356-1-1.html)》
*   《[WebRTC实时音视频技术基础：基本架构和协议栈](http://www.52im.net/thread-442-1-1.html)》
*   《[浅谈开发实时视频直播平台的技术要点](http://www.52im.net/thread-475-1-1.html)》
*   《[[观点] WebRTC应该选择H.264视频编码的四大理由](http://www.52im.net/thread-488-1-1.html)》
*   《[基于开源WebRTC开发实时音视频靠谱吗？第3方SDK有哪些？](http://www.52im.net/thread-510-1-1.html)》
*   《[开源实时音视频技术WebRTC中RTP/RTCP数据传输协议的应用](http://www.52im.net/thread-589-1-1.html)》
*   《[简述实时音视频聊天中端到端加密（E2EE）的工作原理](http://www.52im.net/thread-763-1-1.html)》
*   《[实时通信RTC技术栈之：视频编解码](http://www.52im.net/thread-1034-1-1.html)》
*   《[开源实时音视频技术WebRTC在Windows下的简明编译教程](http://www.52im.net/thread-1125-1-1.html)》
*   《[网页端实时音视频技术WebRTC：看起来很美，但离生产应用还有多少坑要填？](http://www.52im.net/thread-1282-1-1.html)》
*   >> [更多同类文章 ……](http://www.52im.net/forum.php?mod=collection&action=view&ctid=5)

## 十二、移动端IM开发的其它热点问题

**移动端IM开发中还会遇到上述内容未提及的内容，以下文章或许您用的上：**
《[移动端IM开发需要面对的技术问题](http://www.52im.net/thread-133-1-1.html)》
《[开发IM是自己设计协议用字节流好还是字符流好？](http://www.52im.net/thread-150-1-1.html)》
《[请问有人知道语音留言聊天的主流实现方式吗？](http://www.52im.net/thread-175-1-1.html)》
《[IM消息送达保证机制实现(一)：保证在线实时消息的可靠投递](http://www.52im.net/thread-294-1-1.html)》
《[IM消息送达保证机制实现(二)：保证离线消息的可靠投递](http://www.52im.net/thread-594-1-1.html)》
《[如何保证IM实时消息的“时序性”与“一致性”？](http://www.52im.net/thread-714-1-1.html)》
《[IM单聊和群聊中的在线状态同步应该用“推”还是“拉”？](http://www.52im.net/thread-715-1-1.html)》
《[IM群聊消息如此复杂，如何保证不丢不重？](http://www.52im.net/thread-753-1-1.html)》
《[谈谈移动端 IM 开发中登录请求的优化](http://www.52im.net/thread-282-1-1.html)》
《[IM群聊消息如此复杂，如何保证不丢不重？](http://www.52im.net/thread-753-1-1.html)》
《[IM开发基础知识补课(一)：正确理解前置HTTP SSO单点登陆接口的原理](http://www.52im.net/thread-1351-1-1.html)》
《[IM开发基础知识补课(二)：如何设计大量图片文件的服务端存储架构？](http://www.52im.net/thread-1356-1-1.html)》
《[一种Android端IM智能心跳算法的设计与实现探讨（含样例代码）](http://www.52im.net/thread-783-1-1.html)》
《[移动端IM登录时拉取数据如何作到省流量？](http://www.52im.net/thread-787-1-1.html)》
《[通俗易懂：基于集群的移动端IM接入层负载均衡方案分享](http://www.52im.net/thread-802-1-1.html)》
《[完全自已开发的IM该如何设计“失败重试”机制？](http://www.52im.net/thread-280-1-1.html)》
《[微信对网络影响的技术试验及分析（论文全文）](http://www.52im.net/thread-195-1-1.html)》
《[即时通讯系统的原理、技术和应用（技术论文）](http://www.52im.net/thread-218-1-1.html)》
《[开源IM工程“蘑菇街TeamTalk”的现状：一场有始无终的开源秀](http://www.52im.net/thread-447-1-1.html)》
《[从客户端的角度来谈谈移动端IM的消息可靠性和送达机制](http://www.52im.net/thread-1470-1-1.html)》
《[现代移动端网络短连接的优化手段总结：请求速度、弱网适应、安全保障](http://www.52im.net/thread-1413-1-1.html)》
《[移动端IM开发者必读(一)：通俗易懂，理解移动网络的“弱”和“慢”](http://www.52im.net/thread-1587-1-1.html)》
《[移动端IM开发者必读(二)：史上最全移动弱网络优化方法总结](http://www.52im.net/thread-1588-1-1.html)》
>> [更多同类文章 ……](http://www.52im.net/forum.php?mod=collection&action=view&ctid=10) 

## 十三、参考技术方案

### 1MobileIMSDK工程（已开源）↘

<ignore_js_op style="word-wrap: break-word; font-family: &quot;Microsoft YaHei&quot;;">![新手入门一篇就够：从零开发移动端IM_a.png](http://upload-images.jianshu.io/upload_images/9028834-def5ec5222bf4611.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "新手入门一篇就够：从零开发移动端IM_a.png")

 </ignore_js_op>
MobileIMSDK首版开发于2013年（截止2018年2月已更新至v3.2版），它主要使用原生代码编写，应用于非Web网页方式的移动端即时通讯场景下，详细介绍请见：[http://www.52im.net/thread-52-1-1.html](http://www.52im.net/thread-52-1-1.html)。

### 2MobileIMSDK-Web工程（捐助作者得精编源码）↘

<ignore_js_op style="word-wrap: break-word; font-family: &quot;Microsoft YaHei&quot;;">![新手入门一篇就够：从零开发移动端IM_b.png](http://upload-images.jianshu.io/upload_images/9028834-e278a0039c789af3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "新手入门一篇就够：从零开发移动端IM_b.png")

 </ignore_js_op>
MobileIMSDK-Web首版开发于2016年（仍在持续升级中），完全使用JavaScript编写，主要应用于Web网页方式的即时通讯场景下（包括但不限于手机端、PC端的网页聊天或消息推送等），详细介绍请见：[http://www.52im.net/thread-959-1-1.html](http://www.52im.net/thread-959-1-1.html)。

### 3RainbowAV工程（捐助作者得精编源码）↘

<ignore_js_op style="word-wrap: break-word; font-family: &quot;Microsoft YaHei&quot;;">![新手入门一篇就够：从零开发移动端IM_222.png](http://upload-images.jianshu.io/upload_images/9028834-eeabd76a63604f39.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "新手入门一篇就够：从零开发移动端IM_222.png")

 </ignore_js_op>
RainbowAV是一套完整移动端实时音视频框架(含服务端)，支持分布式，不依赖于第3方服务，可私有化部署，使用方便，部署简单，轻量级、模块化设计，开发者可方便修改、演进甚至用于2次开发，详细介绍请见：[http://www.52im.net/thread-1027-1-1.html](http://www.52im.net/thread-1027-1-1.html)。

### 4RainbowChat产品级IM系统 ↘

<ignore_js_op style="word-wrap: break-word; font-family: &quot;Microsoft YaHei&quot;;">![新手入门一篇就够：从零开发移动端IM_111.png](http://upload-images.jianshu.io/upload_images/9028834-d587bfdf24e4ebcf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "新手入门一篇就够：从零开发移动端IM_111.png")

 </ignore_js_op>
RainbowChat是一套基于[MobileIMSDK](http://www.52im.net/thread-52-1-1.html)的产品级移动端IM系统。RainbowChat源于真实运营的产品，运营统计：[点此进入](http://www.52im.net/thread-21-1-1.html)，不同于市面上开源或售卖的demo级代码，RainbowChat的产品前身已被成千上万真实的客户使用过，解决了大量的屏幕适配、细节优化、机器兼容问题（[可自行下载体验](https://fir.im/2qmd)），详细介绍请见：[http://www.52im.net/thread-19-1-1.html](http://www.52im.net/thread-19-1-1.html)。

## 附录1：其它即时通讯文章

**[1] 有关WEB端即时通讯开发：**
《[新手入门贴：史上最全Web端即时通讯技术原理详解](http://www.52im.net/thread-338-1-1.html)》
《[Web端即时通讯技术盘点：短轮询、Comet、Websocket、SSE](http://www.52im.net/thread-336-1-1.html)》
《[SSE技术详解：一种全新的HTML5服务器推送事件技术](http://www.52im.net/thread-335-1-1.html)》
《[Comet技术详解：基于HTTP长连接的Web端实时通信技术](http://www.52im.net/thread-334-1-1.html)》
《[新手快速入门：WebSocket简明教程](http://www.52im.net/thread-831-1-1.html)》
《[WebSocket详解（一）：初步认识WebSocket技术](http://www.52im.net/thread-331-1-1.html)》
《[WebSocket详解（二）：技术原理、代码演示和应用案例](http://www.52im.net/thread-326-1-1.html)》
《[WebSocket详解（三）：深入WebSocket通信协议细节](http://www.52im.net/thread-332-1-1.html)》
《[WebSocket详解（四）：刨根问底HTTP与WebSocket的关系(上篇)](http://www.52im.net/thread-1258-1-1.html)》
《[WebSocket详解（五）：刨根问底HTTP与WebSocket的关系(下篇)](http://www.52im.net/thread-1266-1-1.html)》
《[WebSocket详解（六）：刨根问底WebSocket与Socket的关系](http://www.52im.net/thread-1273-1-1.html)》
《[socket.io实现消息推送的一点实践及思路](http://www.52im.net/thread-188-1-1.html)》
《[LinkedIn的Web端即时通讯实践：实现单机几十万条长连接](http://www.52im.net/thread-659-1-1.html)》
《[Web端即时通讯技术的发展与WebSocket、Socket.io的技术实践](http://www.52im.net/thread-690-1-1.html)》
《[Web端即时通讯安全：跨站点WebSocket劫持漏洞详解(含示例代码)](http://www.52im.net/thread-793-1-1.html)》
《[开源框架Pomelo实践：搭建Web端高性能分布式IM聊天服务器](http://www.52im.net/thread-849-1-1.html)》
《[使用WebSocket和SSE技术实现Web端消息推送](http://www.52im.net/thread-907-1-1.html)》
《[详解Web端通信方式的演进：从Ajax、JSONP 到 SSE、Websocket](http://www.52im.net/thread-1038-1-1.html)》
《[MobileIMSDK-Web的网络层框架为何使用的是Socket.io而不是Netty？](http://www.52im.net/thread-1248-1-1.html)》
《[理论联系实际：从零理解WebSocket的通信原理、协议格式、安全性](http://www.52im.net/thread-1341-1-1.html)》
>> [更多同类文章 ……](http://www.52im.net/forum.php?mod=collection&action=view&ctid=15)

**[2] 有关推送技术的文章：**
《[iOS的推送服务APNs详解：设计思路、技术原理及缺陷等](http://www.52im.net/thread-345-1-1.html)》
《[Android端消息推送总结：实现原理、心跳保活、遇到的问题等](http://www.52im.net/thread-341-1-1.html)》
《[扫盲贴：认识MQTT通信协议](http://www.52im.net/thread-318-1-1.html)》
《[一个基于MQTT通信协议的完整Android推送Demo](http://www.52im.net/thread-315-1-1.html)》
《[求教android消息推送：GCM、XMPP、MQTT三种方案的优劣](http://www.52im.net/thread-314-1-1.html)》
《[移动端实时消息推送技术浅析](http://www.52im.net/thread-288-1-1.html)》
《[扫盲贴：浅谈iOS和Android后台实时消息推送的原理和区别](http://www.52im.net/thread-286-1-1.html)》
《[绝对干货：基于Netty实现海量接入的推送服务技术要点](http://www.52im.net/thread-166-1-1.html)》
《[移动端IM实践：谷歌消息推送服务(GCM)研究（来自微信）](http://www.52im.net/thread-122-1-1.html)》
《[为何微信、QQ这样的IM工具不使用GCM服务推送消息？](http://www.52im.net/thread-117-1-1.html)》
《[极光推送系统大规模高并发架构的技术实践分享](http://www.52im.net/thread-602-1-1.html)》
《[从HTTP到MQTT：一个基于位置服务的APP数据通信实践概述](http://www.52im.net/thread-605-1-1.html)》
《[魅族2500万长连接的实时消息推送架构的技术实践分享](http://www.52im.net/thread-723-1-1.html)》
《[专访魅族架构师：海量长连接的实时消息推送系统的心得体会](http://www.52im.net/thread-750-1-1.html)》
《[深入的聊聊Android消息推送这件小事](http://www.52im.net/thread-771-1-1.html)》
>> [更多同类文章 ……](http://www.52im.net/forum.php?mod=collection&action=view&ctid=11)

**[3] 更多即时通讯技术好文分类：**
[http://www.52im.net/forum.php?mod=collection&op=all](http://www.52im.net/forum.php?mod=collection&op=all)

## 附录2：来自IM大厂的精华文章汇总

**[1] 有关QQ、微信的技术文章：**
《[微信团队分享：微信每日亿次实时音视频聊天背后的技术解密](http://www.52im.net/thread-1311-1-1.html)》
《[QQ音乐团队分享：Android中的图片压缩技术详解（上篇）](http://www.52im.net/thread-1208-1-1.html)》
《[QQ音乐团队分享：Android中的图片压缩技术详解（下篇）](http://www.52im.net/thread-1212-1-1.html)》
《[腾讯团队分享：手机QQ中的人脸识别酷炫动画效果实现详解](http://www.52im.net/thread-1190-1-1.html)》
《[腾讯团队分享 ：一次手Q聊天界面中图片显示bug的追踪过程分享](http://www.52im.net/thread-1181-1-1.html)》
《[微信团队分享：微信Android版小视频编码填过的那些坑](http://www.52im.net/thread-1173-1-1.html)》 
《[微信手机端的本地数据全文检索优化之路](http://www.52im.net/thread-1132-1-1.html)》 
《[企业微信客户端中组织架构数据的同步更新方案优化实战](http://www.52im.net/thread-1131-1-1.html)》
《[微信团队披露：微信界面卡死超级bug“15。。。。”的来龙去脉](http://www.52im.net/thread-1099-1-1.html)》
《[QQ 18年：解密8亿月活的QQ后台服务接口隔离技术](http://www.52im.net/thread-1093-1-1.html)》
《[月活8.89亿的超级IM微信是如何进行Android端兼容测试的](http://www.52im.net/thread-1086-1-1.html)》
《[以手机QQ为例探讨移动端IM中的“轻应用”](http://www.52im.net/thread-1076-1-1.html)》
《[一篇文章get微信开源移动端数据库组件WCDB的一切！](http://www.52im.net/thread-932-1-1.html)》
《[微信客户端团队负责人技术访谈：如何着手客户端性能监控和优化](http://www.52im.net/thread-921-1-1.html)》
《[微信后台基于时间序的海量数据冷热分级架构设计实践](http://www.52im.net/thread-895-1-1.html)》
《[微信团队原创分享：Android版微信的臃肿之困与模块化实践之路](http://www.52im.net/thread-893-1-1.html)》
《[微信后台团队：微信后台异步消息队列的优化升级实践分享](http://www.52im.net/thread-801-1-1.html)》
《[微信团队原创分享：微信客户端SQLite数据库损坏修复实践](http://www.52im.net/thread-789-1-1.html)》 
《[腾讯原创分享(一)：如何大幅提升移动网络下手机QQ的图片传输速度和成功率](http://www.52im.net/thread-675-1-1.html)》 
《[腾讯原创分享(二)：如何大幅压缩移动网络下APP的流量消耗（下篇）](http://www.52im.net/thread-697-1-1.html)》 
《[腾讯原创分享(二)：如何大幅压缩移动网络下APP的流量消耗（上篇）](http://www.52im.net/thread-696-1-1.html)》 
《[微信Mars：微信内部正在使用的网络层封装库，即将开源](http://www.52im.net/thread-620-1-1.html)》 
《[如约而至：微信自用的移动端IM网络层跨平台组件库Mars已正式开源](http://www.52im.net/thread-684-1-1.html)》 
《[开源libco库：单机千万连接、支撑微信8亿用户的后台框架基石 [源码下载]](http://www.52im.net/thread-623-1-1.html)》 
《[微信新一代通信安全解决方案：基于TLS1.3的MMTLS详解](http://www.52im.net/thread-310-1-1.html)》 
《[微信团队原创分享：Android版微信后台保活实战分享(进程保活篇)](http://www.52im.net/thread-210-1-1.html)》 
《[微信团队原创分享：Android版微信后台保活实战分享(网络保活篇)](http://www.52im.net/thread-209-1-1.html)》 
《[Android版微信从300KB到30MB的技术演进(PPT讲稿) [附件下载]](http://www.52im.net/thread-206-1-1.html)》 
《[微信团队原创分享：Android版微信从300KB到30MB的技术演进](http://www.52im.net/thread-205-1-1.html)》 
《[微信技术总监谈架构：微信之道——大道至简(演讲全文)](http://www.52im.net/thread-200-1-1.html)》
《[微信技术总监谈架构：微信之道——大道至简(PPT讲稿) [附件下载]](http://www.52im.net/thread-199-1-1.html)》 
《[如何解读《微信技术总监谈架构：微信之道——大道至简》](http://www.52im.net/thread-201-1-1.html)》
《[微信海量用户背后的后台系统存储架构(视频+PPT) [附件下载]](http://www.52im.net/thread-186-1-1.html)》
《[微信异步化改造实践：8亿月活、单机千万连接背后的后台解决方案](http://www.52im.net/thread-624-1-1.html)》 
《[微信朋友圈海量技术之道PPT [附件下载]](http://www.52im.net/thread-178-1-1.html)》 
《[微信对网络影响的技术试验及分析（论文全文）](http://www.52im.net/thread-195-1-1.html)》 
《[一份微信后台技术架构的总结性笔记](http://www.52im.net/thread-179-1-1.html)》 
《[架构之道：3个程序员成就微信朋友圈日均10亿发布量[有视频]](http://www.52im.net/thread-177-1-1.html)》 
《[快速裂变：见证微信强大后台架构从0到1的演进历程（一）](http://www.52im.net/thread-168-1-1.html)》
《[快速裂变：见证微信强大后台架构从0到1的演进历程（二）](http://www.52im.net/thread-170-1-1.html)》 
《[微信团队原创分享：Android内存泄漏监控和优化技巧总结](http://www.52im.net/thread-143-1-1.html)》 
《[全面总结iOS版微信升级iOS9遇到的各种“坑”](http://www.52im.net/thread-142-1-1.html)》 
《[微信团队原创资源混淆工具：让你的APK立减1M](http://www.52im.net/thread-139-1-1.html)》 
《[微信团队原创Android资源混淆工具：AndResGuard [有源码]](http://www.52im.net/thread-140-1-1.html)》 
《[Android版微信安装包“减肥”实战记录](http://www.52im.net/thread-138-1-1.html)》 
《[iOS版微信安装包“减肥”实战记录](http://www.52im.net/thread-137-1-1.html)》 
《[移动端IM实践：iOS版微信界面卡顿监测方案](http://www.52im.net/thread-136-1-1.html)》 
《[微信“红包照片”背后的技术难题](http://www.52im.net/thread-128-1-1.html)》 
《[移动端IM实践：iOS版微信小视频功能技术方案实录](http://www.52im.net/thread-126-1-1.html)》 
《[移动端IM实践：Android版微信如何大幅提升交互性能（一）](http://www.52im.net/thread-124-1-1.html)》
《[移动端IM实践：Android版微信如何大幅提升交互性能（二）](http://www.52im.net/thread-125-1-1.html)》
《[移动端IM实践：实现Android版微信的智能心跳机制](http://www.52im.net/thread-120-1-1.html)》 
《[移动端IM实践：WhatsApp、Line、微信的心跳策略分析](http://www.52im.net/thread-121-1-1.html)》 
《[移动端IM实践：谷歌消息推送服务(GCM)研究（来自微信）](http://www.52im.net/thread-122-1-1.html)》
《[移动端IM实践：iOS版微信的多设备字体适配方案探讨](http://www.52im.net/thread-116-1-1.html)》 
《[信鸽团队原创：一起走过 iOS10 上消息推送(APNS)的坑](http://www.52im.net/thread-862-1-1.html)》
《[腾讯信鸽技术分享：百亿级实时消息推送的实战经验](http://www.52im.net/thread-999-1-1.html)》
>> [更多同类文章 ……](http://www.52im.net/forum.php?mod=collection&action=view&ctid=12)

**[2] 有关QQ、微信的技术故事：**
《[2017微信数据报告：日活跃用户达9亿、日发消息380亿条](http://www.52im.net/thread-1199-1-1.html)》
《[腾讯开发微信花了多少钱？技术难度真这么大？难在哪？](http://www.52im.net/thread-1114-1-1.html)》
《[技术往事：创业初期的腾讯——16年前的冬天，谁动了马化腾的代码](http://www.52im.net/thread-613-1-1.html)》 
《[技术往事：史上最全QQ图标变迁过程，追寻IM巨人的演进历史](http://www.52im.net/thread-599-1-1.html)》 
《[技术往事：“QQ群”和“微信红包”是怎么来的？](http://www.52im.net/thread-874-1-1.html)》 
《[开发往事：深度讲述2010到2015，微信一路风雨的背后](http://www.52im.net/thread-290-1-1.html)》 
《[开发往事：微信千年不变的那张闪屏图片的由来](http://www.52im.net/thread-204-1-1.html)》 
《[开发往事：记录微信3.0版背后的故事（距微信1.0发布9个月时）](http://www.52im.net/thread-203-1-1.html)》 
《[一个微信实习生自述：我眼中的微信开发团队](http://www.52im.net/thread-115-1-1.html)》
《[首次揭秘：QQ实时视频聊天背后的神秘组织](http://www.52im.net/thread-961-1-1.html)》
>> [更多同类文章 ……](http://www.52im.net/forum.php?mod=collection&action=view&ctid=14) |
>> | 

[![即时通讯网 - 即时通讯开发者社区！](http://upload-images.jianshu.io/upload_images/9028834-2ce1aef4a93e70d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://www.52im.net/thread-464-1-1.html "即时通讯网 - 即时通讯开发者社区！")  来源：[即时通讯网](http://www.52im.net/thread-464-1-1.html "即时通讯网 - 即时通讯开发者社区！") - 即时通讯开发者社区！

 |



