# \GatewayWorker\Lib\Gateway::sendToGroup

## 说明:
```php
void Gateway::sendToGroup(mixed $group, string $message);
```

向某个分组的所有client_id发送数据。


## 参数

* ```$group```

group可以是字符串、数字、或者数组。如果为数组，则是给数组内所有group发送数据

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
        // $message = '{"type":"send_to_group","group":"xxxxx", "message":"...."}'
        $req_data = json_decode($message, true);
        Gateway::sendToGroup($req_data['group'], $req_data['message']);
    }

    ...
}

```
