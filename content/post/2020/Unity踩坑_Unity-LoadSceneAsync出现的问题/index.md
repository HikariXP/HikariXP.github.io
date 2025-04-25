---
author: "CharSui"
title: "Unity-LoadSceneAsync出现的协程问题"
date: "2022-09-20"
description: "需要缓一帧"
tags:
  - "Unity"
  - "踩坑"
categories:
  - "Unity"
series:
  - "Themes Guide"
aliases:
  - "migrate-from-jekyl"
---

## 出现的问题

如同其他游戏一般，想要在A场景往C场景移动的时候，冒出来一个作为加载场景的B，实现A->B->C的效果，但是实测的时候会出现问题，有但不限于：

A、如果C场景太少内容，B没机会出来就直接加载C了，即使已经禁止了allowSceneActivation依然会加载。

B、异步进度一直为零。



## 分析问题

Unity单次载入场景只能为一个，如果顺序错误似乎会造成程序认为在同时加载B和C（不确定的原因）。



加载B的途中，即使B的内容很少可以瞬间加载，但是脚本动作更快，在B加载完之前C就加载完了，跳过了这一步的同时也造成即使allowSceneActivation为False也可以载入。



## 问题解决方案

在输入载入作为Loading场景的B后，把后面的代码延迟一小会，目前测试延迟0.1s也可以，没测试过延迟一帧，但是如果出现问题的确是因为第二个原因，那么延迟一帧不一定稳定，延迟0.1s既不影响体验也可以保证稳定性。



一下是代码内容：

```c#
    IEnumerator LoadScene_IEnumerator()
    {
        time = 0;
        SceneManager.LoadScene("Load");
        yield return new 			WaitForSecondsRealtime(0.1f);
        asyncOperation = null;
        asyncOperation =         SceneManager.LoadSceneAsync(SceneName);
        asyncOperation.allowSceneActivation = false;
        Debug.Log("Now Loading: "+ SceneName);
        while (!asyncOperation.isDone)
        {
            // Check if the load has finished
            if (asyncOperation.progress >= 0.9f)
            {
                //Wait to you press the space key to activate the Scene
                if (time >= LeastTimeNeedWait)
                {
                    WaitToClick = true;
                    if (Input.anyKey)
                    {
                        asyncOperation.allowSceneActivation = true;
                        WaitToClick = false;
                        asyncOperation = null;
                        StopCoroutine(LoadScene_IEnumerator());
                    }
                }
            }
            yield return null;
        }
    }
```

__注意：__

协程中的While请记得添加yield return，不然会导致Unity崩溃，不强行关闭内存占用会一直上升。



曾经出现这个问题，疑惑了两三天，上网搜索无果只能不断尝试。

愿能为出现这个问题的人提供帮助。



