# \GatewayWorker\Lib\Gateway::leaveGroup

## 说明:
```php
void Gateway::leaveGroup(string $client_id, mixed $group);
```

将client_id从某个组中删除


## 参数

* ```$client_id```

客户端的client_id

* ```$group```

只能是数字或者字符串。

## 范例
```php
use \GatewayWorker\Lib\Gateway;
class Event
{
    ...

    public static function onMessage($client_id, $message)
    {
        // $message = '{"type":"leave","group":"xxxxx"}'
        $req_data = json_decode($message, true);
        Gateway::leaveGroup($client_id, $req_data['group']);
    }

    ...
}

```
