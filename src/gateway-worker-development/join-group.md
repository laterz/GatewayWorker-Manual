# \GatewayWorker\Lib\Gateway::joinGroup

## 说明:
```php
void Gateway::joinGroup(string $client_id, mixed $group);
```

将client_id加入某个组，以便通过```Gateway::sendToGroup```发送数据。

可以通过```Gateway::getClientInfoByGroup```获得该组所有在线成员数据。

该方法对于分组发送数据例如房间广播非常有用。

**注意：**

同一个client_id可以加入多个分组，以便接收不同组发来的数据。

当client_id下线（连接断开）后，该client_id会自动从该分组中删除，开发者无需调用```Gateway::leaveGroup```。

## 参数

* ```$client_id```

客户端的client_id

* ```$group```

只能是数字或者字符串。

注意:group不能为空值。例如```0```,```0.0```,```'0'```,```"0"```,```false```,```null```是非法的group值。

## 范例
```php
use \GatewayWorker\Lib\Gateway;
class Event
{
    ...

    public static function onMessage($client_id, $message)
    {
        // $message = '{"type":"join","group":"xxxxx"}'
        $req_data = json_decode($message, true);
        Gateway::joinGroup($client_id, $req_data['group']);
    }

    ...
}

```
