# OnlineGameProject_Ver0.1

---

纯C#

用户注册登录。

定期用户信息存档。

Socket TCP协议

金融交易系统，根据特定算法每天给出，或每（小时/天）给出数值。

解密，手绘风+彩色描边+机能风+赛博背景故事。

全景地图：存在着一次次的线索，观察细节部分揭露事实。



假设1：双金币资源不共用，金融系统用点数，其他用货币，玩家的最基本战力基础就算服务器矩阵的算力，租用服务器可以以低廉的价格用到好的服务器但是限时且如果做了一些攻击性事件的时候容易被查。自己买会很贵，而且要交电费（服务器本身、空调），还有房费等，但毕竟难寻找源头且受限小。服务器矩阵以及算法做成可自定义，增加可玩性。



UserAccount:





玩法：

解密

金融系统

日常运营小店铺（要考虑）



AcrylicServer需要具备的功能：

接受与发送信息：

* 监听特定端口（12345）
* 群发信息
* 对用户数据进行管理



(进化方向：xml or json)

对Msg类进行序列化处理

Msg信息包含：

- 日期DateTime
- 调用的方法 string 数字
- 内容 string

暂定方法编号：string

ToServer：

1001 登录请求

1002 注册请求

1003 金融账号登录请求

1004 金融账号注册请求



ToClient:

10011 登录成功

10010 登录失败

10021 注册成功

10020 注册失败

10031 金融账号登录成功

10030 金融账号登录失败

10041 金融账号注册成功

10040 金融账号注册失败



2021.3.19/开发日志：

需要修改序列化方法，改为json，再测试注册登录等信息。



2021.3.20/开发日志：

问题不在于序列化，在于连接服务器的时候服务器会发送一则不规范的信息，已修改。

服务器暂时正常，客户端的Client脚本需要优化，脱离MonoBehaviour(Unity内部好屑)



2021.3.21/开发日志:

暂时解决所有登录问题，获取用户数据正常。但曾经服务器端出现过

Additional text encountered after finished reading JSON content

这样的问题，还没发现触发条件，印象中是短时间内多次提交数据导致。出现的时候会导致双端失效。

弄个离线模式用于测试玩法。**之前出现过的服务器不规范信息，尝试用try序列化避免出错就一直失效的问题。**