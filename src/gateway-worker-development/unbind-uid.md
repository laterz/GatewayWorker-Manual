# \GatewayWorker\Lib\Gateway::unbindUid

## 说明:
```php
void Gateway::unbindUid(string $client_id, mixed $uid);
```

将client_id与uid解绑


## 参数

* ```$client_id```

客户端的client_id

* ```$uid```

数字或者字符串。

## 范例
```php
use \GatewayWorker\Lib\Gateway;
class Event
{
    ...

    public static function onMessage($client_id, $message)
    {
        // $message = '{"type":"logout","uid":"xxxxx"}'
        $req_data = json_decode($message, true);
        Gateway::unbindUid($client_id, $req_data['uid']);
    }

    ...
}

```
