## 提示
GatewayWorker提供的所有接口都是支持分布式调用的，所以业务代码不需要任何更改，直接就可以分布式部署。

# 如何分布式GatewayWorker
##关键点

### 1、设置Gateway启动脚本(一般是start_gateway.php)中的```lanIp```与当前服务器内网ip一致
### 2、将Gateway 和 businessWorker的注册服务地址设置成相同地址。

## 部署示例
以Applications/Demo为例，假如需要部署三台服务器(192.168.1.1-3)提供高可用服务。

1、选择一台服务器运行统一的注册服务，这里选择192.168.1.1，注册服务的端口为1236（实际上每台服务器上1236端口都运行这注册服务）

2、配置三台服务器start_gateway.php start_businessworker.php中的registerAddress为'192.168.1.1:1236'。

3、分别配置三台服务器start_gateway.php中的```lanIp```为当前服务器的内网ip(192.168.1.1-3)。

4、逐台启动，分布式部署完毕。

**说明：**

1、三台GatewayWorker机器都运行了Gateway进程和Worker进程，客户端连接上任意一台GatewayWorker的Gateway端口即可。

2、为了方便前端接入和扩容，可以在Gateway前加一层DNS、LVS等负载均衡策略

3、如果服务器不够用可以使用同样的方法增加服务器

4、如果需要下线服务器，可以停止GatewayWorker，然后执行后续停机等下线操作(由于Gateway进程维护着客户端连接，当对应服务器下线时，对应服务器的客户端会掉线一次。如何做到下线机器不影响用户参考下一节)。


