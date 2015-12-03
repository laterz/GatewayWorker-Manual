# \GatewayWorker\Lib\Gateway::getALLClientInfo

## 说明:
```php
array Gateway::getALLClientInfo(void);
```

获取当前所有在线client_id信息。


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
        $_SESSION['name'] = $message['name'];
        var_export(Gateway::getALLClientInfo());
    }
    ...
}
```


打印出的数据类似如下：
```php
array(
    '7f00000108fc00000008' => array('name'=>'Tom'),
    '7f00000108fc00000009' => array('name'=>'Joan'),
)
```
