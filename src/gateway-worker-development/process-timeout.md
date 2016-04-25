# processTimeout


## 说明:
```php
int BusinessWorker::$processTimeout
```
**(注意：此特性需要GatewayWorker版本>=2.0.2，如何查看版本号参考《常见问题》一章)**

(此属性一般不用设置)


设置监控服务端业务超时时间（单位秒）。不设置默认是30秒，设置为0表示不监控。

此属性用于监控```Events::onConnect Events::onMessage Events::onClose```的执行时间，单次执行时间如果超过```BusinessWorker::$processTimeout```设定的值，则记录一条日志到workerman.log，日志中包含超时的调用栈，这对于排查业务长时间阻塞以及死循环等问题非常有帮助。

注意：此属性需要在Events.php文件头部设置一条语句```declare(ticks=1);```才能生效，见下面示例。

## 范例
start_businessworker.php
```php
use \Workerman\Worker;
use \GatewayWorker\BusinessWorker;

$worker = new BusinessWorker();
$worker->name = 'ChatBusinessWorker';
$worker->count = 4;
$worker->registerAddress = '127.0.0.1:1236';

// 设置业务超时时间10秒，需要配合业务
$worker->processTimeout = 10;

if(!defined('GLOBAL_START'))
{
    Worker::runAll();
}
```

Events.php

需要在文件头部增加```declare(ticks=1);```语句，添加后运行```php start.php reload```即可生效。
```php
<?php
/**
 * BusinessWorker::$processTimeout 属性需要配合declare(ticks=1)才能生效
 */
declare(ticks=1);

use \GatewayWorker\Lib\Gateway;

class Events
{
   public static function onMessage($client_id, $message)
   {
       self::testTimeout();
   }

   protected static function testTimeout()
   {
       // 故意放一个死循环
       while(1)
       {
           sleep(1);
       }
   }
}
```

以上应用运行后，会在GatewayWorker/workerman.log(设置Worker::$logFile可以更改日志路径)里面产生业务超时日志，日志内容类似如下。

```
process_timeout:
#1 /home/GatewayWorker/Applications/YourApp/Event.php(14): Events::testTimeout()
#2 [internal function]: Events::onMessage('7f0000010b54000...', 'test msg')
#3 /home/GatewayWorker/GatewayWorker/BusinessWorker.php(264): call_user_func('Event::onMessag...', '7f0000010b54000...', 'test msg')
#4 [internal function]: GatewayWorker\BusinessWorker->onGatewayMessage(Object(Workerman\Connection\AsyncTcpConnection), Array)
#5 /home/GatewayWorker/Workerman/Connection/TcpConnection.php(453): call_user_func(Array, Object(Workerman\Connection\AsyncTcpConnection), Array)
#6 [internal function]: Workerman\Connection\TcpConnection->baseRead(Resource id #49, 2, NULL)
#7 /home/GatewayWorker/Workerman/Events/Libevent.php(221): event_base_loop(Resource id #32)
#8 /home/GatewayWorker/Workerman/Worker.php(1507): Workerman\Events\Libevent->loop()
#9 /home/GatewayWorker/GatewayWorker/BusinessWorker.php(121): Workerman\Worker->run()
#10 /home/GatewayWorker/Workerman/Worker.php(900): GatewayWorker\BusinessWorker->run()
#11 /home/GatewayWorker/Workerman/Worker.php(860): Workerman\Worker::forkOneWorker(Object(GatewayWorker\BusinessWorker))
#12 /home/GatewayWorker/Workerman/Worker.php(387): Workerman\Worker::forkWorkers()
#13 /home/GatewayWorker/start.php(32): Workerman\Worker::runAll()
#14 {main}
```

从上面日志中可以看到业务代码在/home/GatewayWorker/Applications/YourApp/Events.php的第14行运行Event::testTimeout()时超时

