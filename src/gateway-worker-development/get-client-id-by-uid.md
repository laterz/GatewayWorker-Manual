# \GatewayWorker\Lib\Gateway::getClientIdByUid

## 说明:
```php
void Gateway::getClientIdByUid(mixed $uid);
```

获取与uid绑定的所有client_id

## 参数

* ```$uid```

uid,可以是数字或者字符串。

## 返回值
返回一个client_id的数组

## 范例
```php
use \GatewayWorker\Lib\Gateway;
class Event
{
    ...

    public static function onMessage($client_id, $message)
    {
        // $message = '{"type":"get_client_id","uid":"xxxxx"}'
        $req_data = json_decode($message, true);
        var_export(Gateway::getClientIdByUid($req_data['uid']));
    }

    ...
}

```

输出类似
```php
array(
    '7f00000108fc00000008',
    '7f00000108fc00000009'
)
```
