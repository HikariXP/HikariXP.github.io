---
title: 工作总结 - 2022 in PeroPero
author: CharSui
date: 2025-02-23
description: 2022年工作总结，今年8月正式从毕业生变成从业者，在PeroPero当客户端开发，在MD项目组
tags:
  - 年总结
categories:
  - 工作相关
series:
  - Themes Guide
aliases:
  - migrate-from-jekyl
image: cover.png
---

你猜2022年工作总结是什么时间写的？2333333
之前其实也没有写工作总结的习惯，只是工作了大半年，突然发现日常很多事情都不记得，随养成了写工作日记的习惯。后续在看一个叫原子能的Up主的视频：[# 让你职业生涯少走弯路的几个锦囊妙计](https://www.bilibili.com/video/BV1QC4y1z7Qc/)
突然觉得，也对，别一股脑不知去向地工作，多回顾一下。就好像读书时期，其实死做题没用的，做完得回顾，效率就很高了。

由于刚入职的时候没有养成写日记的习惯，所以能考据的只有当年的周报了。
鉴于刚入职也是弱智一个，所以以月为单位出一个总结。
# 2022更新日志

2022年8.1，正式以"叉烧"作为代号，加入PeroPeroGames，踏入游戏开发行业。

## MainChange

入行，学到了如何使用Git去团队开发。
今年主要是熟悉项目业务为主，还在入门。
业务开发还停留在"照葫芦画瓢"的阶段_

## Feature

- 设计模式相关知识
- Steam功能接入
- Android/iOS打包配置学习
- 学习到了使用Logcat去Debug
- Git使用
- 了解到了Protobuf

## Bugfix
- 代码写的太烂被主程说教了，悲，但是从他的代码中学习到了很多。很多看设计模式时的知识点，其实看过就忘了，死读书，但是看他写的代码可以意识到：”噢，原来这就是设计模式“
- 自己配置Android环境会比Unity自带的SDK配置灵活很多，比如对高API Level的支持。（谷歌会要求上架应用不低于某个版本）
- Xcode打包使用的xproj项目配置文件里面的key和在Xcode里面显示的不一样的，后处理的时候要直接进去这个文件里面看，不要依赖Xcode显示的Key。举个例子：Code Signing Entitlements = CODE_SIGN_ENTITLEMENTS，Xcode里面的少了个ing