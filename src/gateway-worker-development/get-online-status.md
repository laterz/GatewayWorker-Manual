# \GatewayWorker\Lib\Gateway::getOnlineStatus

## 说明:
```php
array Gateway::getOnlineStatus(void);
```

获取当前所有在线client_id列表

**注意：**

开发者要慎用此函数，尤其在线连接数过万时。

当在线连接数过万时，此函数返回的数据可能少于实际在线数据。


## 范例
```php
use \GatewayWorker\Lib\Gateway;

class Event
{
    ...
    public onConnect($client)
    {
        // 打印在线client_id列表
        var_export(Gateway::getOnlineStatus());
    }
    ...

}
    ```

打印出的数据类似如下：
```php
array(
    0=>1001,
    1=>1009,
    2=>99,
);
```
