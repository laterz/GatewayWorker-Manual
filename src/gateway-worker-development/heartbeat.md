# 服务端到客户端的心跳检测
GatewayWorker支持**服务端**向**客户端**定时发送心跳包检测连接是否存活

## 为什么需要心跳检测？
正常的情况客户端断开连接会向服务端发送一个fin包，服务端收到fin包后得知客户端连接断开，则立刻触发onClose事件回调。

但是有些极端情况如客户端掉电、网络关闭、拔网线、路由故障等，这些情况客户端无法发送fin包给服务端，服务端便无法知道连接已经断开。如果客户端与服务端定时有心跳数据传输，则会比较及时的发现连接断开，触发onClose事件回调。

另外路由节点防火墙会关闭长时间不通讯的socket连接，导致socket长连接断开。所以需要客户端与服务端定时发送心跳数据保持连接不被断开。


## 心跳检测的原理是什么？
服务端向客户端发送心跳检测，客户端接收到心跳数据后，可以忽略不做任何处理，也可以回应心跳检测（向服务端发送一段任意数据）。这就分为两种情况，

1、当服务端不要求客户端必须回应心跳检测时，假如客户端遇到掉电等极端情况，这时服务端向客户端发送的心跳数据在TCP层面就会发送超时，遇到这种超时情况TCP会重试多次（次数及间隔依赖操作系统的配置），多次无果后会断开连接。这种极端情况从连接断开到服务端检测到可能要持续至少10分钟才触发onClose事件回调。

2、当服务端要求必须回应检测时，如果服务端在规定的时间内没有收到客户端的任何数据，则立刻判定客户端已经断开，服务端就立即断开连接触发onClose事件回调。

## WorkerMan中如何配置心跳检测？
目前只有Gateway/BusinessWorker模型实现了应用层心跳检测，Worker默认没有应用层的心跳检测，如有需要可以自行开发。

### 例子：
心跳检测在初始化Gateway时设置，例如
```php
$gateway = new Gateway("Websocket://0.0.0.0:8585");

$gateway->pingInterval = 10;

$gateway->pingNotResponseLimit = 2;

$gateway->pingData = '{"type":"ping"}';
```

### 说明：

``` Gateway::$pingInterval ```

服务端向客户端发送心跳数据的时间间隔 单位：秒。如果设置为0代表不发送心跳检测


``` Gateway::$pingNotResponseLimit ```

客户端连续```$pingNotResponseLimit```次```$pingInterval```时间内不回应心跳则断开链接。
如果设置为0代表客户端不用发送回应数据，即通过TCP层面检测连接的连通性（极端情况至少10分钟才能检测到）

``` Gateway::$pingData ```

要发送的心跳请求数据，心跳数据是任意的，只要客户端能识别即可。

### 注意:
如果客户端有发来数据，那么证明客户端存活，服务端会省略一个心跳，下个心跳大约```1.5*$gateway->pingInterval```秒后发送。


## 技巧1

如果客户端有定时向服务端发送心跳检测，则服务端可以不必向客户端发送心跳检测，即利用客户端主动发送的数据判断客户端是否存活。这时我们需要设置```pingData=''```,例如如下配置
```php
$gateway = new Gateway("Websocket://0.0.0.0:8585");

$gateway->pingInterval = 10;

$gateway->pingNotResponseLimit = 2;

$gateway->pingData = '';
```
代表服务端不发送任何心跳数据，但是客户端如果 ```pingInterval*pingNotResponseLimit=20``` 秒内连接上没有任何请求则断开连接

## 技巧2
服务端可以只发送心跳检测，而不要求客户端必须回应，则可以像下面这样设置。
```php
$gateway = new Gateway("Websocket://0.0.0.0:8585");

$gateway->pingInterval = 10;

$gateway->pingNotResponseLimit = 0;

$gateway->pingData = '{"type":"ping"}';
```

其中```pingNotResponseLimit = 0```代表服务端允许客户端不响应心跳，也就是通过TCP层面检测连接的状态，这样如果客户端因为断电等极端情况断开连接，可能需要等待TCP超时重传多次才能感应到连接断开，耗时较长。
