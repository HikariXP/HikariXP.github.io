---
author: CharSui
title: 部署Hugo和使用Stack改版主题
date: 2024-05-09
description: 记录第一次部署博客以及使用主题遇到的障碍
tags:
  - Hugo
  - Github-Pages
categories:
  - 技术
series:
  - Themes Guide
aliases:
  - migrate-from-jekyl
image: pawel-czerwinski-8uZPynIu-rQ-unsplash.jpg
---

突然兴致而来，和同事小武聊起来博客就谈到了Hexo，随后和基友毛聊起来又谈到了Hugo。
没有经过什么详细的调查，单纯只是想找一个方便的博客框架，有不错的外观就行，将精力集中在写笔记留档上，于是随缘用上了Hugo。

一开始甚至没留意到Stack主题(即使排的很前)，后来在配置主题上废了一整个晚上也没搞懂怎么配置，文档也没看懂，因为没有任何前端知识储备，挫败感很强。

之后就一边遇到问题一边谷歌搜解决方案，效果不太好，其他人似乎没有遇到我那么多问题。但搜到了一篇博客
[建站技术 | 使用 Hugo+Stack 简单搭建一个博客](https://blog.reincarnatey.net/2023/build-hugo-blog-with-stack-mod/#%E7%94%A8hugo%E5%88%9B%E5%BB%BA%E5%8D%9A%E5%AE%A2)

![](Snipaste_2024-05-09_23-11-29.png)

原谅我没有深看，因为昨天的配置失败让急于求成，但浅看发现:"好像比原装Stack要更加清晰和好用?"，于是便立即前往大佬的GitHub上下载。

[Mantyke/Hugo-stack-theme-mod](https://github.com/Mantyke/Hugo-stack-theme-mod)

由于刚刚的那篇博客已经说了怎么搭建了，我也没什么前端知识，一个臭Unity开发的，就不浪费时间了再讲那部分了。甚至部署也有非常多的博客可以搜得到。

[Hugo官方GithubPages部署教程](https://gohugo.io/hosting-and-deployment/hosting-on-github/)

总流程是:
1.	先使用Hugo创建一堆需要的基础文件
2.	新建一个Github的仓库，并设置为Pages
3.	将第1步生成的内容放入此仓库，并做好设置
4.	Enjoy your blog

如果你问:"那你写这篇笔记来做什么?"
它就是我博客的 "Hello World"

# 遇到的问题

## 1、笔记无法加载

倒是确实遇到了问题，比如这套主题，需要Markdown笔记具有一些前置信息去提供给主题去显示。

```
+++
author = "CharSui"
title = "部署Hugo和使用Stack改版主题"
date = "2024-05-09"
description = "记录第一次部署博客以及使用主题遇到的障碍"
tags = [
	"Hugo","Github-Pages"
]
categories = [
"博客"
]
series = ["Themes Guide"]
aliases = ["migrate-from-jekyl"]
image = "pawel-czerwinski-8uZPynIu-rQ-unsplash.jpg"

+++
```
第一次用的时候不知道，导致觉得很奇怪，为什么我明明放了笔记却不显示呢？明明上一个主题都可以的。

但是我本地写笔记已经习惯使用Obsidian，Obsidian并不支持读取TOML格式，所以让DS把他们都转成了YAML。

## 2、无法部署

因为是直接套模板，没什么前端知识储备（就是什么都不懂）。第一次替换旧主题的并且提交的时候出现了错误，因为config.yaml的baseurl没有修改为正确的地址。改了就可以了。报错内容是这样的:

```
Environment URL '' is not a valid http(s) URL, so it will not be shown as a link in the workflow graph.
```

## 3、图片不显示
后来写笔记的时候遇到了这个问题。
不知道是不是这个Stack主题的问题，它没办法识别`[[]]`这种图片引用。
然后这个应该时markdown的约束。如果改为`![]()`作为图片的引用，则不支持图片名字中有空格。