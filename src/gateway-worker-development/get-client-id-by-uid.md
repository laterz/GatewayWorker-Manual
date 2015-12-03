# \GatewayWorker\Lib\Gateway::getClientIdByUid

## 说明:
```php
array Gateway::getClientIdByUid(mixed $uid);
```

返回一个数组，数组元素为与uid绑定的所有在线的client_id。如果没有在线的client_id则返回一个空数组。

此方法可以判断一个uid是否在线。

注意：返回值为与uid绑定的所有在线的client_id数组。因为已经下线的client_id会自动与uid解绑，所以已经下线的client_id不会出现在返回值中。

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
