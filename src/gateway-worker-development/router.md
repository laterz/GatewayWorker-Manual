# router

## 说明:
```php
callback Gateway::$router
```

设置Gateway到BusinessWorker路由规则。默认规则是Gateway随机选择一个BusinessWorker进程，然后把当前client_id与这个BusinessWorker进程**绑定**，以后这个client_id的所有数据（onConnect/onMessage/onClose事件）都交给这个绑定的BusinessWorker进程处理。

期待该回调函数从所有到BusinessWorker进程的连接对象中选择一个并返回。

**注意：之前（2015-09-22日之前）版本GatewayWorker默认使用的是随机路由，也就是同一个client_id的onConnect/onMessage/onClose事件可能被不同的BusinessWorker进程处理，带来的问题是onConnect/onMessage/onClose可能并发执行甚至乱序执行。并且与client_id有绑定关系的定时器时可能与预期效果不符，例如同一个client_id的onConnect回调设置的定时器在onClose中可能无法删除，原因是两个回调可能被分配到不同的BusinessWorker进程处理。以上问题在2015-09-22之后的版本通过绑定路由得到了修正，开发者到原来应用下载地址重新下载自己的应用即可得到最新的GatewayWorker版本**


## 回调函数的参数

``` $worker_connnections ```

是一个数组，里面包含了所有到BusinessWorker进程的连接对象。数组的key为BusinessWorker进程的通讯地址，格式为ip:port。回调函数最终将返回该数组中一个连接对象。


``` $client_connection ```

客户端连接对象,可以通过此对象获得客户端ip端口等信息

``` $cmd ```

当前什么类型的消息，是个数字，分别可能为

1: CMD_ON_CONNECTION，即连接事件

2: CMD_ON_MESSAGE，即消息事件

3: CMD_ON_CLOSE，即客户端关闭事件


``` $buffer ```

客户端发来的数据。注意只有当 ``` $cmd ``` 为``` 2 ```时 ```$buffer ```才有值

## 返回值
返回 ```$worker_connnections``` 中的一个连接对象



## 范例 1 随机路由

```php
use \GatewayWorker\Gateway;
$gateway = new Gateway("Websocket://0.0.0.0:8585");
// ===随机路由开始===
$gateway->router = function($worker_connections, $client_connection, $cmd, $buffer)
{
    return $worker_connections[array_rand($worker_connections)];
};
// ===随机路由结束===

$gateway->name = ...
...省略...

if(!defined('GLOBAL_START'))
{
    Worker::runAll();
}
```

## 范例 2 随机绑定
```php
use \GatewayWorker\Gateway;
$gateway = new Gateway("Websocket://0.0.0.0:8585");

// ==绑定==
$gateway->router = function($worker_connections, $client_connection, $cmd, $buffer)
{
    // 临时给客户端连接设置一个businessworker属性，用来存储该连接被绑定的worker进程
    if(!isset($client_connection->businessworker))
    {
        $client_connection->businessworker = $worker_connections[array_rand($worker_connections)];
    }
    return $client_connection->businessworker;
};
// ==绑定==

$gateway->name = ...
...省略...

if(!defined('GLOBAL_START'))
{
    Worker::runAll();
}
```


