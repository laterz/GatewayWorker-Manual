# \GatewayWorker\Lib\Gateway::sendToUid

## 说明:
```php
void Gateway::sendToUid(mixed $uid, string $message);
```

向uid绑定的所有client_id发送数据。

注意：默认uid与client_id是一对多的关系，如果当前uid下绑定了多个client_id，则多个client_id对应的客户端都会收到消息，这类似于PC QQ和手机QQ同时在线接收消息。

## 参数

* ```$uid```

uid可以是字符串、数字、或者包含uid的数组。如果uid为数组，则是给数组内所有uid发送数据

* ```$message```

要发送的数据（字符串类型），此数据会被Gateway所使用协议的encode方法打包后再发送给客户端

## 范例
```php
use \GatewayWorker\Lib\Gateway;
class Event
{
    ...

    public static function onMessage($client_id, $message)
    {
        // $message = '{"type":"send_to_uid","uid":"xxxxx", "message":"...."}'
        $req_data = json_decode($message, true);
        Gateway::sendToUid($req_data['uid'], $req_data['message']);
    }

    ...
}

```
