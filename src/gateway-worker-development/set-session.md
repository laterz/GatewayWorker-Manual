# \GatewayWorker\Lib\Gateway::setSession

## 说明:
```php
void Gateway::setSession(string $client_id, array $session);
```
**``` (要求Gateway版本>=2.0.5) ```**

设置某个client_id对应的session。如果对应client_id已经下线或者不存在，则会被忽略。

## 参数

* ```$client_id```

客户端的client_id

* ```$session```

要设置的session数组


## 返回值

无返回

## 范例
```php
use \GatewayWorker\Lib\Gateway;

class Events
{
    ...
    public onMessage($client_id, $message)
    {
        Gateway::setSession($client_id, array('key1'=>'value1', 'key2'=>'value2'));
    }
    ...
}
```
