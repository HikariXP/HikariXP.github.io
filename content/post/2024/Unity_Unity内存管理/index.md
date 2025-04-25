---
author: "CharSui"
title: "Unity内存管理"
date: "2024-04-21"
description: "Unity游戏开发中的内存优化策略与常见问题分析"
tags:
  - "Unity"
categories:
  - "Unity"
  - "视频笔记"
series:
  - "Themes Guide"
aliases:
  - "migrate-from-jekyl"
---



https://www.bilibili.com/video/BV1aJ411t7N6/

https://www.bilibili.com/read/cv3260881/ GC文章翻译（第三方）

### 物理内存
CPU的内存读取其实很慢，每次都会访问CPU中的Shared Cache再去内存找，也就是核心自己的内存。
台式设备和移动设备内存架构差异大：没有独显，没有显卡，CPU板上面积更小，缓存级数更少，大小更小。
所以有时候Android的报错溢出不是内存，是显存爆炸了。

### 虚拟内存
内存交换（放到硬盘上的内存）移动设备的IO也很慢，移动设备的内存交换性能也有消耗，硬盘读写寿命也更少。
iOS可以进行内存压缩，但是Android没有（）
iOS能把不用的程序占用的内存可以先压缩，也是存在内存里面。

### 内存寻址范围
32位和64位讲的是计算器运算位数而不是MCU位数，MemoryControlUnit。



### Android内存管理
Android基于Linux，4K一个Page，Page是最基本的内存单位
内存杀手 - Low Memory Killer

4K Per page
回收和分配都是以page为单位
用户态和内核态区分。内核态内存，用户态是严格不许访问的。

AKA Lmk  - low memory killer
按照软件优先度杀软件，按理来说可以把Wifi功能都给你杀了。
App原地重启都有可能。壁纸都给你杀掉。然后你手机就重启了。

### 内存指标
Resident Set Size（RSS）
当前APP调用的所有内存，RSS算上所有公共库的内存，也就是你的插件也算在你的APP的PSS。

Proportional Set Size（PSS）
如果公共库有其他APP用，那就是平均分到每个APP上。

Unique Set Size（USS）
USS是只有你自己用的，纯粹只有你自己申请的。

例子：Procrank
可以列出所有内存使用信息。
如果PSS很高，USS很低，那就很可能你是被背锅了。一般平台用的是PSS检查。

### Unity内存管理
Unity是C++引擎，底层代码完全由C++所写，每一行。
通过Wrapper提供给用户API。所以所有API都是在\[.Bindings]里面放的。
用户代码仍会转换为Cpp代码(il2cpp)
VM仍然存在。

用il2cpp之后，几乎没有纯正的C#在跑。有了VM跨平台会更加方便。

按分配方式分为
NativeMemory和ManagedMemory，在Editor和Runtime是不同的。
不同不只是说，统计数据不一样，是分配方式都不一样。

Asset在Runtime不加载不放进内存，在Editor会全部加载。（演讲者：巨型项目见过加载一礼拜的）
2019.3推出AssetPipeline2.0，会加载基础的，后面的拓展的才会加载，详细可以自己搜搜。

Unity内存按照管理者可以分为：**引擎管理内存**、**用户管理内存**
#### 引擎内存
引擎自己调用请求用的，一般开发者不需要触碰。

#### 用户管理内存
用户写的寄的代码就会导致很容易爆炸，确切需要关心的。

Unity检测不到的内存：用户分配的native内存，比如用户自己写的C++插件，这个Unity检查不到，还有Lua，Lua是自己管理的。

### Unity Native Memory管理
Allocator与memory lable，Unity重载了几乎所有的内存分配符，比如alloc或者new。所以每个申请内存的操作都需要一个额外的"memory lable"的参数。标记着申请的内存应该放到哪一块内存池里面。
Allocator会根据memory lable分配到对应的池，每个池单独跟踪。
不明白（?_?）

#### 最佳实践
##### Scene
C++引擎，所有实体都会反应在C++里面。不会反应在托管堆。
我们构建一个GO的时候，底层会建立一个到多个的GO来存储这个GO的信息，所以场景中存在多个GO的时候，Native内存就会显著上升。
很多UnityNative内存大量生成的时候，去检查Scene，经常就是这个问题。

##### Audio
DSP buffer，是可以设置几档位的，是声音的缓冲。
填充太大了，声音大小没那么大，就会延迟。
如果太小了，会经常向CPU发请求，导致CPU压力上升。

##### ForceToMono
音效师为了声音效果，将声音设置为双通道，但很多游戏音效实际是单声道也没有任何影响的（比如UI音效）。意味着一个1m的声音变成了2m，不仅影响包体还影响内存。
强制单声道。

##### Format
不同平台会有不同的Format支持，比如ios一般会推荐MP3,安卓hrbrs？这是什么？猜测是Vorbis（Ogg，Mkv）。
对于MP3，ios是有硬件支持的，速度会更好（Android没有）

##### Compression Format
音频在游戏中是压缩的还是怎么样。

##### Code Size
模板泛型乱用会导致影响打包速度以及Code Size，比如一个模板函数有4、5个泛型参数，结果导出一个Cpp 25m。
所有Class和template，最后都会展开，如果你一直排列组合，那么最后就会得到所有的排列组合，不重复。代码是一样的，但是类型不一样。IL2CPP就会给你展开。

##### AssetBundle
###### TypeTree
当前版本用到了哪些变量和对应数据类型，反序列化就根据TypeTree记录的内容。
没有就用默认值序列化。Build AssetBundle，当你确认同一个版本的Unity版本打出来的时候，TypeTree完全可以关掉，内存减少，包大小减少，build和运行时会变快。
每次序列化的时候，如果发现需要TypeTree序列化的时候，要序列化两次，先序列化TypeTree再序列化本体。

###### Lz4
Unity主推，效率快，压缩比率比Lzma差30%，速度快10倍左右。
###### Lzma
基本不用，解压和读取速度都慢，而且会占用大量内存，它不是用什么解压，而是全解压。chunkbase。Lzma不基于ChunkBase（那Lz4呢？），基于ChunkBase的访问旧的资源可以引用缓存减少内存消耗。

###### Size & count
一个Ab包多大。
AB包基本都分两部分，头文件和实际文件。如果你每个资源都打一个包，那你就多了很多没用的头文件了。
官方推荐1m-2m一个AssetBundle，现在5g网络好很多就可以放宽考虑。

##### Resources文件夹
这个文件夹在打包的时候会生成一个R-B Tree，红黑树，帮助resources检索资源，如果Resources文件夹很大，你得RBTree就很大，会持续存在内存之中，从App打开就加载，这个红黑树没加载完游戏不会启动。
基本只在Debug的时候用。

##### Texture
###### upload buffer
填满多大向GPU发送push一次

###### r/w: read / write
Texture没必要就不需要开。
正常流程Texture，读进内存，解析完了，放到uploadbuffer上传了就会从内存delete掉。
你开了r/w就不会卸载了，就在你显存和内存都有一份。

###### MipMaps
UI没必要开。

##### Mesh
###### r/w
同上

###### compression
有些版本开了不如不开，这个需要自己注意。

##### Assets

### Unity Managed Memory
Unity托管内存

#### VM内存池
Mono虚拟机的内存池

##### VM会返还内存给OS吗？
会

##### 返还条件
当一个block连续6次GC没有被访问到，才会返还给系统，这种情况基本看不到，尤其是MonoRuntime的时候，ILRuntime可能稍微还会多一点机会。

#### GC机制
GC不会把内存返还给系统
GC是一种机制，而这种机制有很多种不同的具体实现。
Untiy在用的叫Boehm-Demers-Weiser：[相位裂缝](https://www.hboehm.info/gc/)

Unity在用的Boehm是不分代的，有些GC会划分大中小块，还有不常用块，Unity没有，纯堆一起（快），而且它是非压缩式的GC，不会对内存进行重新调整位置。

##### 下一代GC
###### Incremental GC（渐进式GC）
解决的是主线程卡顿的问题，现在的GC会暂停主线程，遍历所有的GC Memory，决定多少被GC掉。
IGC分帧做了，比如分成10帧去做，总体时间没变，但是可以改善卡顿情况。

###### SGen或者升级Boehm
SGen很先进，分代的。策略速度快

###### IL2CPP
Unity重新写的，升级版的Boehm

##### GC机制考量
Throughput（回收能力）
一次回收回收多少内存

PauseTimes（暂停时长）
进行内存回收的时候对主线程的影响多大

Fragmentation（碎片化）
回收内存之后，会对整体内存池碎片化贡献量

MutatorOverhead（额外消耗）
统计，标记之类的消耗

Scalability（可拓展性）
扩展到多核多线程会不会有bug

Protability（可移植性）
不同平台是否可以使用


##### 内存碎片化
就是因为不会挤掉间隔，间隔不够大就无法分配了。
这就被严重碎片化了。
如果进行高密度内存加载操作，先加载大内存，再加载小内存。
这部分内存不算内存泄漏，这叫Zombie Memory，无用内存，没有释放。它没有丢失一直都在，但是你也不用，它就占着这个位置。

#### 最佳实践
Don't Null it，but Destroy it（用Destroy而不是设null）
Class VS Struct看雨松
Pool In Pool高频使用的小部件，自己创建内存池，自己重用
Closures and anonymous methods（闭包和匿名函数）
所有的匿名和闭包最后会被IL生成翻译全都是new一个class，实际是一个class
里面所有函数和变量，里面所有new的东西都要占内存。

Coroutines协程，只要没释放掉，所用的内存会一直存在，所有的变量。局部变量也是。用就生成，不用就去除掉。协程不是线程

Configurations（配置表）

Singleton慎用，C++年代就是万恶资源，扔进去就不管，全在内存里。

SetActive有GC，SetActive实际上会干很多工作，可以看源代码，有很多初始化的工作，比如子UI的递归情况。当你UISetActive很卡的时候，可以试试把UI放到屏幕外，这样就没有SetActive了。

Unity内使用TPL，跟协程比起来需要注意的地方。
IO返回倒是可以用异步做，但是协程会轮询。如果轮询没什么消耗没关系，有问题就异步。

Unity基本已经停止mono的支持了，以后会转ilruntime，