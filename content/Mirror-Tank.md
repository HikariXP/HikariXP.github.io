# Unity-Mirror-学习笔记(Tank)

通过Mirror内置的例子来学习其用法，此为笔记。



### 游戏对象以及其使用到的Network组件

***

* [NetworkManager](#networkManager) [^0]

> NetworkManagerHUD[^4]
>
> [Telepathy Transport](#Telepathy Transport)
>
> NetworkManager

* Spawn(GameObject)[^1]

> [NetworkStartPosition](#NetworkStartPostition)

* UI-Canvas(GameObject)[^2]

> [TankGameManager(Script)](#TankGameManager)

* Tank(Perfab-GameObject)[^3]

> [NetWorkIdentity](#NetworkIdentity)
>
> [NetworkTransform](#NetworkTransform)
>
> [Tank(Script)](#Tank(Script))



### 各组件以及脚本的用处

***

* <span id = "networkManager">__NetworkManager__</span>

  > * [游戏状态管理](#游戏状态管理)
  >
  > - [生成管理](#生成管理)
  > - [场景管理](#场景管理)
  > - 调试信息
  > - 自定义个性化

  NetWorkManager是联机的核心组件，可以通过Inspector界面调整具体参数。

  每个Scene只能有一个NetworkManager，多余的会自动删除。由于具有NetworkIdentity组件的对象在加载场景的时候会自动禁用，所以NetworkManager不能放在游戏对象（会有操作）上。

  NetworkManager可以通过派生来根据项目实际需求来修改，自定义化，但如果项目不大，还是保持原样效率会更高。

  #### <span id = "Telepathy Transport">*Telepathy Transport*</span>

  在给游戏对象添加NetworkManager的时候会自动加上Telepathy Transport组件。这是一个管理数据传输的组件，如果有需要可以使用自己已有的，Inspector上的NetworkManager-NetworkInfo-Transport上切换即可。

  #### <span id  = "游戏状态管理">__游戏状态管理__</span>

  NetworkManager有三种运行模式-分别是：Server（仅服务器），Host（同时作为服务器和客户端），Client（仅客户端）。

  如果使用NetworkManagerHUD[^4]则可直接在游戏运行时选择运行模式，自己调用为最优（一般来说）。

  调用的方法：

  > * NetworkManager.StartClient();
  >
  > * NetworkManager.StartServer();
  >
  > * NetworkManager.StartHost();

  如果脚本没有提前建立存放NetworkManager引用位[^5]的话,只需要直接调用单例[^6]就好。

    

  以Client模式开启前最好改变一下这个属性：NetworkManager.networkAddress

  也就是IP地址，同一台电脑上测试只需要填写localhost即可，但基本上用的时候肯定不是同一设备，所以可以增加一项功能：开启主机端，会在游戏内展示本机IP。

  C#获取自机IP的语句：

  ```c#
  public string GetLocalIp()
  {
      ///获取本地的IP地址
      string IP = string.Empty;
      foreach (IPAddress _IPAddress in Dns.GetHostEntry(Dns.GetHostName()).AddressList)
      {
          if (_IPAddress.AddressFamily.ToString() == "InterNetwork")
          {
              AddressIP = _IPAddress.ToString();
          }
      }
      return AddressIP;
  }
  ```

  我的做法是弹出输入框让玩家输入主机端玩家提供的IP，玩家多一步操作。

  流程基本就是：服务端/主机端开启，客户端同一网络下输入IP进入（非局域网联机尚未尝试）

  #### <span id = "生成管理">__生成管理__</span>

  NetworkManager具有管理对象生成的功能（动态生成）。

  如果要自主操作全局玩家的生成，则需要了解NetworkConnection相关知识（当前尚不讨论这一点）

  Spawn Info窗口管理着相关信息：

  > PlayerPrefab：玩家的预制体（需要有NetworkIdentity）
  >
  > AutoCreatePlayer:bool（一般为开启，除非需要自主生成）
  >
  > PlayerSpawnMethod:两个属性：
  >
  > * A：Random：场景上每一个具有NetworkStartPosition的对象随机成为生成点
  > * B：RoundRobin：从Hierarchy<u>从上往下</u>每一个具有NetworkStartPosition组件的对象轮流作为生成点
  >
  > Rigistered Spawnable Perfabs:
  >
  > 这是一个列表，存放一些非玩家的需要动态生成的物体，比如子弹，金币奖励等。__注意：这些物体同样需要NetworkIdentity组件__

  ##### <span id = "NetworkStartPostition">NetworkStartPostition</span>

  在这里就要提到NetworkStartPosition，加入这个组件的物体将成为生成点。

  NetworkManager会调用GetStartPostiton()来获取Transform组件。

  #### <span id = "">__场景管理__</span>

  NetworkManager内置联机场景管理功能，一般而言只有两个槽位

  > Offline Scene
  >
  > Online Scene

  也就是一个离线场景和一个线上场景。

  启动服务器（Server）或者主机端（Host）的时候会直接加载Online Scene，同时客户端连接后也会加载至Online Scene。

  Offline Scene也直白：客户端和服务端断开连接的时候加载的场景。

  如需要在游戏中切换场景的话，则需要调用ServerChangeScene函数，让当前所有连接的客户端切换至新的Scene。需要切换的场景需要把名字加载进networkSceneName。

  但需要注意：以及开始运行的NetworkManager就别再调用StartHost或者StartClient等的操作了，不然可能会引起奇怪的事情。

  特别是NetworkManager会自动切换线上线下场景，所以NetworkManager在场景之间也要保持不变，不然会掉线。尽可能改设置而不是直接整个NetworkManager改变。



* <span id = "TankGameManager"> __TankGameManager(Script)__</span>

  这个组件（脚本）是起到类似GM的作用，也就是游戏的管理。

  在这个例子里，玩家的数据是直接由玩家本体存储，也就是Tank脚本内存储以及同步。

  这个GM主要的作用是：更新UI，判定游戏输赢（以UI展示，没有实际用于服务器的判定GM）。

  

  在这里简单讲一下Mirror实际的效果：

  A,B两个玩家，我们设A为Host，也就是主机端，实际A操作的依然是客户端，只是网络上它也在做服务器做的事而已，B作为客户端加入同一个场景。

  就好像玩遥控车一样，遥控车是玩家预制体，遥控是UI。但是我们彼此在各自的视野里只看得见对手的车，其他都看不见。

  换个角度，我在一个赛道（Scene）里玩遥控车（有UI），还有其他自己会走动的遥控车（无UI），他们在我的Scene只有预制体，只不过他们受“遥控”而已。

  

  实测之后这个GM有漏洞，漏洞在于假设出现这种在玩家预制体内的__同步数据__:

  ```
  [SyncVar]
  public int PlayerHP;
  ```

  这些数据都需要由Server端改变，也就是发送指令，由服务端接受指令并执行，客户端是无权修改的。

  如果是Unity内运行，主机端也只能修改自己的数据，对其他加入的玩家的数据的修改是无效的。

* <span id = "NetworkIdentity">__NetworkIdentity__</span>

  一个网络认证组件，相当于进入游戏的通行证，有了它，Mirror管理的时候才可以把各个物体区分开来。

  只有一个选项可以勾选，

  > ServerOnly

  如果勾选了的话这个物体只能在服务端生成，客户端是不会生成的，例如局域网游戏的裁判等，这种游戏对象一般来讲客户端只负责接受数据和处理前端，判断输赢则仅从服务端即可，就好像一个隐形的旁观者。（具体没操作过这个Serveronly，可以尝试一下）

* <span id = "NetworkTransform">__NetworkTransform__</span>

  这是一个用于同步位置的组件，可以同步对象的位置移动缩放。

  直白点就是同步Transform的数据

  这里有一个很坑的地方：如果你没有勾选Client Authority这个选项，客户端的位置是不会同步到服务端的，也就是客户端做任何事实际上都是没有效果的。

  > Client Authority客户端移动权限。
  >
  > NetworkTransform组件还可以修改灵敏度Sensitivity，只有一帧内变化超过阈值才会同步。
  >
  > SyncMode则可以修改同步的对象，是告诉所有人（Observers观察者），还是只是服务器（Owner私有）
  >
  > SyncInterval则是同步频率，以秒为单位，0-2取值。

  如果有子对象需要跟着移动，则需要在父对象上添加Network Transform Child，并将Target设置为子物体。

* <span id = "Tank(Script)">__Tank(Script)__</span>

  这是例子中的玩家脚本。在需要网络上使用的对象，其脚本应该继承NetworkBehaviour，使用起来跟MonoBehaviour相差无几。

  这里就存在三种特性：[SyncVar] , [Command] , [ClientRpc]

  [SyncVar]：同步数据。

  刚刚提到过，有这种特性的数据的修改只能从服务端修改，客户端是没有权限修改的，只能读取。

  而怎么修改呢？则要通过有[ServerCallBack],[Server]的方法。

  有这些特性的方法都只会在服务器端执行。

  比如子弹中的碰撞，OnTriggerEnter（），在前面加一个[ServerCallBack]的特性，这个判定则在服务端判定，相当于告诉服务器，要是撞到了就调用这个方法。

  [Server]的方法则直接在服务器执行。

  这些是禁止客户端调用的，那如果客户端发射子弹呢？则需要用到[Command]的特性，有这个特性的方法会告诉服务器我要做这个方法，然后服务器就会帮你做。举个例子：

  我要杀A，我告诉B（服务端）我要用手枪杀A，B就接受我的手枪把A干掉，如果我还要B把手枪还我，则**还我**的这个方法需要有[ClientRpc]的特性，相当于这是客户端接受服务端的反馈并且执行。

  所以杀人要用[Command]，杀完人手枪还我要加[ClientRpc]。









[^0]: 联机状态管理组件
[^1]: 复活点
[^2]: UI界面
[^3]: 玩家预制体-坦克
[^4]: NetworkManager的GUI版，在场景运行时实时展示的UI，可以直接改变NetworkManager的属性。  
[^5]:public NetworkManager networkManager;
[^6]:例子:NetworkManager.singleton.StartHost();