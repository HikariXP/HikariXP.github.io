---
title: 工作总结 - 2022 in PeroPero
author: CharSui
date: 2025-02-23
description: 2022年工作总结，今年8月正式从毕业生变成从业者，在PeroPero当客户端开发，在MD项目组
tags:
  - 工作年总结
categories:
  - 经验
series:
  - Themes Guide
aliases:
  - migrate-from-jekyl
image: Image.png
---

你猜2022年工作总结是什么时间写的？2333333
之前其实也没有写工作总结的习惯，只是工作了大半年，突然发现日常很多事情都不记得，随养成了写工作日记的习惯。后续在看一个叫原子能的Up主的视频：[# 让你职业生涯少走弯路的几个锦囊妙计](https://www.bilibili.com/video/BV1QC4y1z7Qc/)
突然觉得，也对，别一股脑不知去向地工作，多回顾一下。就好像读书时期，其实死做题没用的，做完得回顾，效率就很高了。

由于刚入职的时候没有养成写日记的习惯，所以能考据的只有当年的周报了。
鉴于刚入职也是弱智一个，所以以月为单位出一个总结。
# 年总结
## 学到了什么？
设计模式、AES加密、ProtocolBuffer数据传输协议、Steam成就系统、TPNS推送接入、Android打包配置、Xcode项目打包后处理、做需求

# 8月
> 2025回顾：工厂模式啊什么的，主要是解决一个规范化获取数据的途径。比如我最近帧同步系统，获取帧执行指令想要做一些统计数据的时候，工厂模式就可以解决。

一些关键点：AES加密，AES加密前后数据不一致，ProtocolBuffer
做业务

## 总结：
学习为主，毕竟入职月
ProtocolBuffer（现在忘光了，知道是用来干什么的）
设计模式，五大原则，行为模式，结构型模式，创建型模式

在处理排行榜的问题，使用AES加密，但后续ACS在go和Csharp之间的实现不一样导致相同的密钥得到的数据都不一样就没使用了。
学习git操作和SourceTree（后续发现Fork更好用，只是没中文）
学习GC相关概念。也看性能相关什么的，3D数学，渲染Shader什么的，什么PeroTools2

调研Steam排行榜。
# 9月
> 做需求，steam接入，尝试重现MD框架。

开始实现Steam的成就（后来被DR骂了一顿全是代码硬编码写。）
梳理MD1的流程，学用ScriptableObject。
接入了Steam的成就
尝试实现MD的UI框架，但是没用，做兑换码
继续做
接入MD的数据到成就里面，做编辑器。

# 10月
看计算机图形学，跟学Games101，做Steam成就管理系统
TPNS接入调研，接入TPNSDemo开始研究怎么做。实现了法线贴图和几个简单的shader（兰伯特，高光菲涅尔），但是现在全忘记了。

# 11月
推送转接阿里云的。（原本接腾讯云，虽然最终使用的是腾讯云）
学ADB调试，Logcat（只会用AndroidStudio的Logcat看Log）

接入华为渠道遇到问题，接触到了SHA1和SHA256算法
MD侧的推送组件处理好了，准备看怎么接入方舟的角色在商店的显示。

重新配置Android打包环境，删除AS自动下载的工具手动下载指定版本
做了3.0MuseDash第一次方舟联动的PnlDlc角色展示

# 12月
> 安卓打包和Xcode打包的打包后处理以及Gradle环境的配置
> TPNS推送接入
> 配置Xcode的项目直接修改xproj文件

发现ResourcesManager刷资源会不断占用内存
未运行时修改Spine的参数有概率会触发Bug：在Hierachy生成大量Spine物体，导致卡顿，协助检修的方舟倒计时
然后方舟DLC的细节因扩展性太差被驳回了，被DR说教了一番。随后看到了凭空出现的需求，觉得扩展性真的很重要。

伸个懒腰摧毁了配的人体工学椅子。

做推送在改Gradle和打包前处理，
遇到了API Level 30打包问题，因为华为SDK需要指定Gradle版本，所以重新配置一套Gradle以及Android Plugin for Gradle
还有做Xcode的处理，自动添加需要的Framework和添加对UnityAppController.mm的引用。

然后吃到了新冠的Debuff
研究如何接入推送抵达

最终接入成功，获取到了抵达数据，Apple要求推送的Category不能为空才能有抵达数据。做了自动加Target，后处理中自动处理好推送的需求了。

Xcode项目中，有些显示在Xcode上的key值，结果在proj里面的key值是不一样的。
Code Signing Entitlements对应的配置项是CODE_SIGN_ENTITLEMENTS，少了个ing