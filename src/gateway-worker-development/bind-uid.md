# \GatewayWorker\Lib\Gateway::bindUid

## 说明:
```php
void Gateway::bindUid(int $client_id, mixed $uid);
```

将client_id与uid绑定，以便通过sendToUid发送数据。

该方法能方便的将client_id与其他系统uid绑定，通过uid来发送数据。

注意：uid与client_id是一对多的关系，系统允许一个uid下有多个client_id。如果业务需要一对一的关系，则需要业务自己实现，例如在mysql、redis等存储中做自己需要的映射关系。

## 参数

* ```$client_id```

客户端的client_id，当客户端连接Gateway的那一刻框架便为其分配了一个全局唯一的client_id用来全局标识一个客户端连接。

* ```$uid```

uid,可以是数字或者字符串。

## 范例
```php
use \GatewayWorker\Lib\Gateway;
class Event
{
    ...

    public static function onMessage($client_id, $message)
    {
        // $message = '{"type":"login","uid":"xxxxx"}'
        $req_data = json_decode($message, true);
        Gateway::bindUid($client_id, $req_data['uid']);
    }

    ...
}

```
