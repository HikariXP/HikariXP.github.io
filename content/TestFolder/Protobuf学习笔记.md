# Protobuf

## 定义

协议缓冲区提供了一种语言中立、平台中立、可扩展的机制，用于以向前兼容和向后兼容的方式序列化结构化数据。它类似于 JSON，只是它更小更快，并且生成本地语言绑定。



基本流程:

根据自定义数据类型简历`.proto`：Protocol Buffers文件

根据需要使用的平台，使用ProtoBuf编译器将定义好的`.proto`文件编译生成对应平台的库。

在编程软件类调用第二步生成的库是实现序列化。



命令行操作:

```
protoc protoFile.proto --csharp_out=targetDir
```



[中文参考教材](https://www.tizi365.com/archives/367.html)