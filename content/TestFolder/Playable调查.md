[开发者手册](https://docs.unity.cn/cn/2020.3/Manual/Playables.html)

## 官方描述-Offical Define

Playables API 提供一种通过组织和评估树状结构（称为 PlayableGraph）中的数据源来创建工具、效果或其他游戏机制的方法。PlayableGraph 允许您混合、融合和修改多个数据源，并通过单个输出播放它们。

Playables API 支持动画、音频和脚本。Playables API 还提供通过脚本与[动画系统](https://docs.unity3d.com/cn/current/Manual/AnimationSection.html)和音频系统进行交互的能力。

尽管 Playables API 目前仅限于`动画`、`音频`和`脚本`，但它是一种通用 API，最终可供视频和其他系统使用。







Playable核心播放项需要继承`IPlayable`、而输出项需要继承`IPlayableOutput`

核心播放类：

![](LocalFigureBed/PlayablesGraph1.png)



核心输出类：

![](LocalFigureBed/PlayablesGraph2.png)

`Playable`是所有可播放项的基本类型，所以所有播放项变种都可以在需要的时候隐式转换为`Playable`

`PlayableOutput`也是如此，是所有可播放项输出的基本类型，定义了基本方法。

*`Playable` 和 `PlayableOutput` 未暴露大量方法。但“PlayableExtensions”和“PlayableOutputExtensions”静态类提供了扩展方法。*

所有非抽象可播放项都有一个公有静态方法 `Create()`，该方法创建相应类型的可播放项。“Create()”方法始终将 PlayableGraph 作为其第一个参数，该图拥有新创建的可播放项。某些类型的可播放项可能需要其他参数。非抽象可播放项输出还会暴露 `Create()` 方法。

有效的可播放项输出应链接到可播放项。如果可播放项输出未链接到可播放项，则可播放项输出不执行任何操作。要将可播放项输出链接到可播放项，请使用 `PlayableOutput.SetSourcePlayable()` 方法。对于该特定可播放项输出，链接的可播放项充当可播放项树的根。



### 流程

`PlayGraph`是可以播放的，通过`PlayableGraph.Create(string GraphName)`获取。

然后可以将一些`Playable`节点添加到`PlayableGraph`激活Graph的`.Play()`

`ScriptPlayable : Playable`一个可播放的行为，我们通过`ScriptPlayable<T\>.Create(graphThatItShouldGoTo);`来创建

随后创建一个继承于`PlayableBehaviour`的自定义Playable行为就行，把这个继承`PlayableBehaivour`的组件放入上述的T中即可。

这些都是动态的，也就是写进代码，运行的时候生成的。所以你也只能在运行的时候才能从PlayableGraphVisualizer里面看到。

#### 保存

`PlayableAsset`正是为此设计，其继承于`ScriptableObject`，你可以通过`PlayableAsset.CreatePlayable(graph,gameObject);`来直接加载。



### 常用方法

要将两个可播放项连接在一起，请使用 `PlayableGraph.Connect()` 方法。请注意，某些可播放项不能有输入。

使用 `PlayableGraph.Create()` 静态方法来创建 PlayableGraph。

使用 `PlayableGraph.Play()` 方法来播放 PlayableGraph。

使用 `PlayableGraph.Stop()` 方法来停止播放 PlayableGraph。

使用 `PlayableGraph.Evaluate()` 方法来评估 PlayableGraph 在特定时间的状态。

使用 `PlayableGraph.Destroy()` 方法来手动销毁 PlayableGraph。此方法会自动销毁 PlayableGraph 创建的所有可播放项和可播放项输出。必须手动调用此销毁方法来销毁 PlayableGraph，否则 Unity 会发出一条错误消息。







Playble Core Type & Playable Output Type 都继承C#的strcuts，为值类型，可以避免造成GC。



PlayableGraph是可以播放的，但是Playable不行。



Director的更新模式

| 名称                                                         | 效果                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [DSPClock](https://docs.unity3d.com/ScriptReference/Playables.DirectorUpdateMode.DSPClock.html) | 更新基于 DSP（数字声音处理）时钟。将此用于需要与音频同步的图形。 |
| [GameTime](https://docs.unity3d.com/ScriptReference/Playables.DirectorUpdateMode.GameTime.html) | 更新基于 Time.time。将此用于需要在游戏过程中同步的图表，以及需要在游戏暂停时暂停的图表。 |
| [UnscaledGameTime](https://docs.unity3d.com/ScriptReference/Playables.DirectorUpdateMode.UnscaledGameTime.html) | 更新基于 Time.unscaledTime。将此用于即使在游戏暂停时也需要更新的图表。示例：即使游戏暂停，也需要更新菜单转换。 |
| [Manual](https://docs.unity3d.com/ScriptReference/Playables.DirectorUpdateMode.Manual.html) | 更新模式是手动的。您需要使用自己的 deltaTime 手动调用 PlayableGraph.Evaluate。这对于与游戏的其余部分完全断开的图表很有用。例如，本地化的子弹时间。 |







## 比较

Playable相较传统动画机更灵活

Playable结构更简单，可动态链接子图到主图之中。

Playable可以运行时修改，改逻辑结构。

Animator通过变量来间接控制权重，Playable可以直接控制动画权重和时间。

Playable在混合上具备更强灵活性。不仅可以混合两个AnimationClip，还能Clip和AnimatorController之间混合，甚至多个AnimatorController之间混合。











## 结论

Playable提供了一套非常基础，非常底层的API供开发者使用。比较适合用来二次开发，并不适合直接使用（并不是不能用）。这带来了非常高的自由度，但也使下限比较低。

基于它可以开发类似于Unity自带的动画机等的工具，它有基于ScriptableObject的PlayableAsset保存方式，全程基于struct的值类型存储，避免GC。

使用的效果有点像是预定下来的一个类似状态机一样的模板。

官方基于它在推出新的工具（Animation Rigging、DataFlowGraph），而旧的工具（Mecanim，默认动画机）已经是基于Playable构建的了。存在第三方工具（Animancer）









#### 叉烧记录

文档非常稀少，案例也非常稀少。



使用起来更像是可以编程自定义动画的组件。

设计上感觉很像状态机，毕竟Animation就是基于它的。

对Script、Audio、Animation提供支持，但也有其他使用途径。

如果有个可视化编译Playable图的工具应该会很好用，感觉对热更友好。



#### 参考文章

##### UnityForum

- Playable API use case : [Click](https://forum.unity.com/threads/playable-api-use-case.456338/)
- Why does the Playables API exist, when should I use it rather than Mecanim (Animator Controllers)?：[Click](https://forum.unity.com/threads/why-does-the-playables-api-exist-when-should-i-use-it-rather-than-mecanim-animator-controllers.912857/)
- What-about-playable-api：[Click](https://forum.unity.com/threads/what-about-playable-api.697424/?gq=playable)

##### UnityDocumentation

Playables.Playable：[Click](https://docs.unity3d.com/ScriptReference/Playables.Playable.html)

##### 使用教程相关

- GameTorrahod：[Click](https://gametorrahod.com/timeline-marker-and-everything-leading-up-to-it/)
