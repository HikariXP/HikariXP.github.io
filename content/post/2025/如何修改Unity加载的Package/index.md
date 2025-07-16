---
title: 如何修改Unity加载的Package
author: CharSui
date: 2025-07-16
description: 部分情况下，需要修改工具或者Package以适应业务需求，这时候会用上。
tags:
  - Unity
categories:
  - Unity
  - 技术
series:
  - Themes Guide
aliases:
  - migrate-from-jekyl
image: cover.jpg
---

# 概述

通常项目中的Package有三种：
- Unity官方包
- 公司内建的包
- 个人开发的包

但无论是什么包，你都能看见包体里面的内容，比如里面的代码，资源等。

除了通过`Add package from disk...`加载的包体之外，其他包体的修改都只会在当前Editor生命周期内存在。也就是一旦关闭了Editor，你的修改就会打回原形，无法应用到导出的包体内。（好像有点歧义）

而`Add package from disk...`是特例。官方相关位置的文档并没有指出可以通过此方式开发包体，但官方人员在论坛里回答了：[传送门](https://discussions.unity.com/t/how-to-develop-for-packager-environment/929541/3)

![](content/post/2025/如何修改Unity加载的Package/1.png)

Unity加载包体就看目标文件夹的package.json文件以及特定的子文件夹结构等信息，所以你只需要拿到package的原内容，就可以通过`Add package from disk...`的方式，将这些包加载并保留对其的修改。不过采用这种方式加载的包体似乎就没法获取来自远端的升级了。

所以我们得到两个操作手法。
# 1、先卸再加

顾名思义

先去项目的Library找到包体的缓存
![](content/post/2025/如何修改Unity加载的Package/2.png)

复制出来，然后在PackageManager里移除这个包体，再通过`Add package from disk...`添加刚刚复制出来的包体的package.json即可。
## 2、修改清单

这里给出一个示例，照猫画虎就行了。
manifest.json

```JSON
    // origin 
    "com.baba.boi": "https://git.bababoi.com/unity-packages/bababoi.git",
    
    // modify (bababoi文件夹里面有package.json)
    "com.prpr.autobuild": "file:D:/Tool/bababoi",
```

packages-lock.json
```JSON
// origin
"com.baba.boi": {
       "version": "https://git.bababoi.com/unity-packages/bababoi.git",
       "depth": 0,
       "source": "git",
       "dependencies": {
         "com.aa.aaaa": "1.19.15",
         "com.bb.bbbb": "2.0.0"
       },
       "hash": "1145141919810"
       }
     },
     
     // modify
     "com.prpr.autobuild": {
       "version": "file:D:/Tool/bababoi",
       "depth": 0,
       "source": "local",
       "dependencies": {
         "com.aa.aaaa": "1.19.15",
         "com.bb.bbbb": "2.0.0"
       }
     },
```