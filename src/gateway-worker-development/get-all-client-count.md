# \GatewayWorker\Lib\Gateway::getAllClientCount

## 说明:
```php
int Gateway::getAllClientCount(void);
```
``` (要求Gateway版本>=2.0.4) ```

获取当前在线连接总数（多少client_id在线）。


## 参数

无参数

## 返回值

返回一个数字

## 范例
```php
use \GatewayWorker\Lib\Gateway;

class Events
{
    ...
    public onConnect($client_id)
    {
        var_export(Gateway::getAllClientCount());
    }
    ...
}
```


打印出的数据类似如下：
```php
32
```
