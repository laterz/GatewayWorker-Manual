# Event::onConnect

## 说明:
```php
void Event::onConnect(int $client_id);
```

当客户端连接上gateway进程时触发。


## 参数

``` $client_id ```

client_id为当前客户端socket连接的标识，是一个全局唯一的整数，唯一标识某个客户端socket连接。

如果client_id对应的客户端连接断开了，那么这个client_id也就失效了。当这个客户端再次连接到Gateway时，将会获得一个新的client_id。也就是说client_id和客户端的socket连接生命周期是一致的。

只要有client_id，并且对应的客户端在线，就可以调用```Gateway::sendToClient($client_id, $data);```向这个客户端发送数据。


## 返回值
无返回值，任何返回值都会被视为无效的

## 注意

``` $client_id ```是自动生成的并且无法自定义。

**自定义uid：**

有些项目有自己的用户系统，每个用户有自己的全局唯一的uid，如果想实现通过uid而非client_id来推送数据，需要进行 uid与client_id的映射。映射关系可以存储在Mysql、Redis等存储里面。

**映射方法：**

**映射：**当客户端连接上Gateway时，发送一个身份验证数据（用户名+密码或者Web系统的session_id），服务端```Event::onMessage($client_id, $data)```中得到身份验证数据后验证用户是否合法，不合法调用```Gateway::closeClient($client_id)```关掉连接并返回。合法则会得到uid,此时将uid和client_id的关系放在存储中。

**映射关系：** uid 与 client_id可以一对多，也就是同时支持同一个用户同时登录多个客户端（PC、App等）。
也可以一对一，也即是一个用户只能登录一个客户端。

**解除映射：**当客户端与Gateway断开时，会触发```Event::onClose($client_id)```，此时删除存储中client_id与uid的对应关系。

**根据uid推送数据：**
有了uid与client_id的映射关系，就可以通过uid获得client_id，调用```Gateway::sendToClient($client_id, $data);```实现根据uid推送数据。


## onConnect范例
```php
use \GatewayWorker\Lib\Gateway;

class Event
{

    public onConnect($client)
    {
       Gateway::sendToCurrentClient('hello');
    }

}
```
