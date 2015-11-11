# 序言

GatewayWorker基于Workerman开发的一个项目，用于快速开发长连接应用，例如移动通讯、物联网、游戏服务端、聊天室等等。

# 进程模型
GatewayWorker分为Gateway进程和Worker进程和register注册服务进程，Gateway进程维持着客户端的连接并转发连接上发来的数据给Worker进程处理；Worker进程只负责处理转发来的客户端数据，并通过Gateway进程推送数据给任意客户端。register注册服务进程负责注册通知Gateway进程和Worker进程之间的通讯地址。

Gateway只负责网络IO（非阻塞），Worker负责处理业务。

# 客户端

GatewayWorker的通信协议是开放的，又是可定制的，因此，理论上GatewayWorker可以与使用任意协议的任意平台的客户端进行通信。当用户开发客户端时，可以根据相应的通信协议完成与服务端的通信。

# 本手册作用范围
本手册主要是针对GatewayWorker项目



