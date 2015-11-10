# Event::onConnect

## 说明:
```php
void Event::onConnect(string $client_id);
```

当客户端连接上gateway进程时触发。


## 参数

``` $client_id ```

client_id固定为20个字的字符串，用来全局标记一个socket连接，每个客户端连接都会被分配一个全局唯一的client_id。

如果client_id对应的客户端连接断开了，那么这个client_id也就失效了。当这个客户端再次连接到Gateway时，将会获得一个新的client_id。也就是说client_id和客户端的socket连接生命周期是一致的。

只要有client_id，并且对应的客户端在线，就可以调用```Gateway::sendToClient($client_id, $data);```向这个客户端发送数据。


## 返回值
无返回值，任何返回值都会被视为无效的

## 注意

``` $client_id ```是自动生成的并且无法自定义。


## onConnect范例
```php
use \GatewayWorker\Lib\Gateway;

class Event
{

    public static function onConnect($client_id)
    {
       Gateway::sendToCurrentClient("Your client_id is $client_id");
    }

}
```
