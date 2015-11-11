# 1.0升级到2.0注意事项

## 如何知道自己使用的版本是1.0还是2.0

打开文件 GatewayWorker/Gateway.php，查看是否有常量VERSION，并且常量值大于等于2.0.0

```php
class Gateway extends Worker
{

    /**
     * 版本
     * @var string
     */
    const VERSION = '2.0.0';
```

## 2.0和1.0相比有哪些改变

1、框架自身不在依赖Store存储，并且把相关代码全部删除了，如果业务有依赖Store可以自行保留。

2、增加了Register服务，用于注册Gateway、BusinessWorker服务，方便二者建立通讯。

3、client_id由原来的整型改为长度固定为20的字符串，类似```7f00000108ff00000006```

4、Gateway::getOnlineStatus方法被删除，在2.0中请使用Gateway::getAllClientInfo替代，使用方法参见手册

5、Lib/Gateway类增加unbindUid、getClientIdByUid、joinGroup、leaveGroup、sendToGroup、getClientCountByGroup、getClientInfoByGroup、getAllClientInfo等方法。

6、多端口（多协议）应用中，可以通过```$_SERVER['GATEWAY_PORT']```户识别客户端连接的是哪个端口（使用的哪种协议）
