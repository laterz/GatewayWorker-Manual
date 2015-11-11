# \GatewayWorker\Lib\Gateway::getClientCountByGroup

## 说明:
```php
int Gateway::getClientCountByGroup(mixed $group);
```

获取某分组当前在线成员数（多少client_id在线）。


## 参数

* ```$group```

分组名字

## 返回值

返回一个数字

## 范例
```php
use \GatewayWorker\Lib\Gateway;

class Event
{
    ...
    public onConnect($client_id)
    {
        $group = 'romm-1';
        Gateway::joinGroup($client_id, $group);
        var_export(Gateway::getClientCountByGroup($group));
    }
    ...
}
```


打印出的数据类似如下：
```php
16
```

