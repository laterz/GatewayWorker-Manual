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


## 1.0到2.0升级注意事项
### 框架不再依赖Store相关配置，统一使用Regiser注册服务，设置方法如下

1、需要增加一个register服务启动脚本start_register.php
```php
<?php
use \Workerman\Worker;
use \GatewayWorker\Register;
// 自动加载类
require_once __DIR__ . '/../../Workerman/Autoloader.php';
// 必须是text协议，端口与start_gateway.php和start_businessworker.php里面registerAddress一致
$register = new Register('text://0.0.0.0:1236');
// 如果不是在根目录启动，则运行runAll方法
if(!defined('GLOBAL_START'))
{
    Worker::runAll();
}
```

2、start_gateway.php中需要设置```$gateway->registerAddress```与start_register.php中端口一致
```php
<?php
use \Workerman\Worker;
use \GatewayWorker\Gateway;
use \Workerman\Autoloader;
require_once __DIR__ . '/../../Workerman/Autoloader.php';

$gateway = new Gateway("Websocket://0.0.0.0:7272");
$gateway->name = 'ChatGateway';
$gateway->count = 4;
$gateway->lanIp = '127.0.0.1';
$gateway->startPort = 2300;
$gateway->pingInterval = 10;
$gateway->pingData = '{"type":"ping"}';
/**
 * 服务注册地址
 * 单机部署ip为127.0.0.1
 * 端口与start_register.php中监听端口一致
 */
$gateway->registerAddress = '127.0.0.1:1236';
```

3、start_businessworker.php也需要配置registerAddress，方法同上

### client_id由原来整型改为字符串
如果业务有依赖client_id类型需要注意
