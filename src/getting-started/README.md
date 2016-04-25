# 入门指引

## 目录结构
<pre>
.
├── Applications // 这里是所有开发者应用项目
│   └── YourApp  // 其中一个应用目录，目录名可以自定义
│       ├── Events.php // 开发者只需要关注这个文件
│       ├── start_gateway.php // gateway进程启动脚本
│       ├── start_businessworker.php // businessWorker进程启动脚本
│       └── start_register.php // 注册服务启动脚本
│
├── GatewayWorker // GatewayWorker框架代码
│   ├── BusinessWorker.php  // BusinessWorker进程实现
│   ├── Gateway.php         // Gateway进程实现
│   ├── Register.php        // 注册服务进程实现
│   ├── Lib
│   │   ├── Context.php     // Gateway与BusinessWorker通信上下文
│   │   ├── DbConnection.php// 一个数据库连接类
│   │   ├── Db.php          // 数据库连接管理类
│   │   └── Gateway.php     // Gateway通信接口类，给Event.php调用
│   └──── Protocols
│         └── GatewayProtocol.php // Gateway与BusinessWorker通信协议
│
├── Workerman // workerman内核目录
│
└── start.php // 全局启动脚本
</pre>

## 说明

开发者只需要关注Applications/YourApp目录的文件即可（YourApp是项目名，可自定义），其它目录如GatewayWorker和Workerman目录为框架目录，开发者不要改动，这样方便后续框架升级。

一般来说开发者只需要关注Applications/YourApp/Events.php。因为所有业务代码都在这里开始的。

其它start_gateway.php start_businessworker.php start_register.php分别是进程启动脚本，开发者一般不需要改动这三个文件。三个脚本统一由根目录的start.php启动。


## Events.php

例如下面是一个简单的聊天室示例

```php
<?php
use \GatewayWorker\Lib\Gateway;
class Events
{
    /**
     * 当客户端连接时触发
     * 如果业务不需此回调可以删除onConnect
     * @param int $client_id 连接id
     */
    public static function onConnect($client_id)
    {
        // 向当前client_id发送数据
        Gateway::sendToClient($client_id, "Hello $client_id");
        // 向所有人发送
        Gateway::sendToAll("$client_id login");
    }

   /**
    * 当客户端发来消息时触发
    * @param int $client_id 连接id
    * @param string $message 具体消息
    */
   public static function onMessage($client_id, $message)
   {
        // 向所有人发送
        Gateway::sendToAll("$client_id said $message");
   }

   /**
    * 当用户断开连接时触发
    * @param int $client_id 连接id
    */
   public static function onClose($client_id)
   {
       // 向所有人发送
       GateWay::sendToAll("$client_id logout");
   }
}
```

Events.php中定义5个事件回调方法，

  * onWorkerStart businessWorker进程启动事件
  * onConnect 连接事件
  * onMessage 消息事件
  * onClose   连接断开事件
  * onWorkerStop businessWorker进程退出事件


```5个回调接口说明参见 Events类的回调接口 一节```

其中消息事件onMessage是必须的，其它事件回调可以不实现。


```php
<?php
use \GatewayWorker\Lib\Gateway;
class Events
{
   /**
    * 当客户端发来消息时触发
    * @param int $client_id 连接id
    * @param string $message 具体消息
    */
   public static function onMessage($client_id, $message)
   {
        // 向所有人发送
        Gateway::sendToAll("$client_id said $message");
   }
}
```

## start_gateway.php

start_gateway.php为gateway进程启动脚本，主要定义了客户端连接的端口号、协议等信息，具体参见 Gateway类的使用一节。

## start_businessworker.php
start_businessworker.php为businessWorker进程启动脚本，也即是业务处理进程，具体参见 BusinessWorker类的使用一节。

## start_register.php
start_register.php为注册服务启动脚本，参加Register类使用一节

