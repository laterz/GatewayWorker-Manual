# \GatewayWorker\Lib\Gateway::bindUid

## 说明:
```php
void Gateway::bindUid(string $client_id, mixed $uid);
```

将client_id与uid绑定，以便通过Gateway::sendToUid发送数据。

该方法能方便的将client_id与其他系统uid绑定，通过uid来发送数据。

注意：

1、uid与client_id是一对多的关系，系统允许一个uid下有多个client_id。

2、如果业务需要一对一的关系，可以通过Gateway::getClientIdByUid获得某uid已经绑定的client_id，然后开发者自行决定是踢掉之前的client_id还是忽略重复绑定。

3、client_id下线（连接断开）时会自动执行解绑，开发者无需调用Gateway::unbindUid解绑。

## 参数

* ```$client_id```

客户端的client_id

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
