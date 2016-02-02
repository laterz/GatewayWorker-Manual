# eventHandler


## 说明:
```php
callback BusinessWorker::$eventHandler
```
**(注意：此特性需要GatewayWorker版本>=2.0.2，如何查看版本号参考《常见问题》一章)**

(此属性一般不用设置)


设置使用哪个类来处理业务，默认值是```Event```，即默认使用Event.php中的Event类来处理业务。业务类至少要实现onMessage静态方法，onConnect和onClose静态方法可以不用实现。

## 范例
Applications\项目\start_businessworker.php
```php
use \Workerman\Worker;
use \GatewayWorker\BusinessWorker;

$worker = new BusinessWorker();
$worker->name = 'ChatBusinessWorker';
$worker->count = 4;
$worker->registerAddress = '127.0.0.1:1236';

// 设置处理业务的类为MyEvent
$worker->eventHandler = 'MyEvent';

if(!defined('GLOBAL_START'))
{
    Worker::runAll();
}
```

Applications\项目\MyEvent.php
```php
<?php
use \GatewayWorker\Lib\Gateway;

// MyEvent类
class MyEvent
{
    public static function onMessage($client_id, $message)
    {
        Gateway::sendToCurrentClient('works');
    }
}

```
