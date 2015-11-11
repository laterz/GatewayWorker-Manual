# \GatewayWorker\Lib\Gateway::getClientInfoByGroup

## 说明:
```php
array Gateway::getClientInfoByGroup(mixed $group);
```

获取某个分组所有在线用户信息。


## 返回值

返回值为client_id为key，client_id对应的$_SESSION为值的数组。
类似下面的格式
```php
array(
    '7f00000108fc00000008' => array(...),
    '7f00000108fc00000009' => array(...),
)
```

## 范例
```php
use \GatewayWorker\Lib\Gateway;

class Event
{
    ...
    public onMessage($client_id, $message)
    {
        $group = 'room-1';
        $_SESSION['name'] = $message['name'];
        $_SESSION['sex'] = $message['sex'];
        Gateway::joinGroup($client_id, $group);
        var_export(Gateway::getClientInfoByGroup($group));
    }
    ...
}
```


打印出的数据类似如下：
```php
array(
    '7f00000108fc00000008' => array('name'=>'Tom', 'sex'=>1),
    '7f00000108fc00000009' => array('name'=>'Joan', 'sex'=>0),
)
```
