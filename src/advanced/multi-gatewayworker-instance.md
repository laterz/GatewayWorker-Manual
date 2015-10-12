# 运行多个gatewayWorker实例
可以运行多个GatewayWorker实例，步骤如下。

假设已有Applications/Chat，想增加Applications/Chat2

1、拷贝Applications/Chat到Applications/Chat2

2、更改Applications/Chat2/Config/Store.php中的配置，使之与Applications/Chat/Config/Store.php不同
```php
class Store
{
    const DRIVER_FILE = 1;
    const DRIVER_MC = 2;
    const DRIVER_REDIS = 3;
    public static $driver = self::DRIVER_FILE;
    public static $gateway = array(
        '127.0.0.1:6379',
    );
    public static $room = array(
        '127.0.0.1:6379',
    );
    public static $storePath = '';
}
// 这里改成workerman-chat2
Store::$storePath = sys_get_temp_dir().'/workerman-chat2/';
```
如果配置为$driver = self::DRIVER_FILE，则需要改数据文件存储路径Store::$storePath。

如果$driver = self::DRIVER_MC或者DRIVER_REDIS,

则不需要更改Store::$storePath，但要更改
```php
// 这里改为了6380
public static $gateway = array(
    '127.0.0.1:6380',
);
// 这里改为了6380
public static $room = array(
    '127.0.0.1:6380',
);
```
并且memcache或者redis要启动相应端口(这里是6380)的服务端的实例


3、更改Applications/Chat2/start_gateway.php中的属性，包括gateway端口、gateway->startPort、和进程名如下

```php
// 更改gateway进程端口，这里改为8282
$gateway = new Gateway("Websocket://0.0.0.0:8282");
// 更改名称，方便status时查看
$gateway->name = 'ChatGateway2';
$gateway->count = 4;
$gateway->lanIp = '127.0.0.1';
// 更改内部通讯起始端口，这里改为4000
$gateway->startPort = 4000;
$gateway->pingInterval = 10;
$gateway->pingData = '{"type":"ping"}';
```

4、更改Applications/Chat2/start_web.php中的WebServer端口，

```php
// 这里改成55252
$web = new WebServer("http://0.0.0.0:55252");
$web->count = 2;
$web->addRoot('www.your_domain.com', __DIR__.'/Web');
```

5、由于gateway端口更改了，所以前端js连接gateway的端口也要做相应的更改

打开 /Applications/Chat2/Web/index.php

```javascript
// 改成gateway端口8282
ws = new WebSocket("ws://"+document.domain+":8282");
```

## 注意：
不要在start_*.php中直接加载使用Applications/XXX/Config/Store.php，
否则会引起多个实例数据互通。

造成数据互通的原因是start_*.php中直接加载使用Applications/XXX/Config/Store.php，会造成Config/Store类在主进程中就加载进来，而多个GatewayWorker实例的子进程都是主进程派生出来的，造成每个子进程都继承了同样的Config/Store配置，不会自动加载实际的配置，造成数据互通。
