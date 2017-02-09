# processTimeoutHandler


## 说明:
```php
callback BusinessWorker::$processTimeoutHandler
```
``` (需要GatewayWorker版本>=2.0.2) ```


设置超时处理函数，即当发生超时事件时执行的回调函数，默认值为```Workerman\\Worker::log```，即记录日志到```GatewayWorker/workerman.log```（可以设置```Worker::$logFile```更改日志文件）


**注意：**

1、需要GatewayWorker版本>=2.0.2，如何查看版本号参考《常见问题》一章

2、不支持windows系统


## 回调函数参数

``` $trace_str ```

字符串类型，内容是超时的调用栈。

``` $exception ```

Exception对象，可以通过$exception->getTrace()获得更详细的超时调用栈信息，包括完整的参数。


## 回调函数返回值
如果返回true，则继续执行业务代码，结果是很可能永久卡在那里。

如果返回假(包括无返回)，则执行进程重启。

由于```BusinessWorker::$processTimeoutHandler```默认回调```Workerman\\Worker::log```的返回值为null，所以业务超时后默认执行进程重启操作。


## 范例
start_businessworker.php
```php
use \Workerman\Worker;
use \GatewayWorker\BusinessWorker;

$worker = new BusinessWorker();
$worker->name = 'ChatBusinessWorker';
$worker->count = 4;
$worker->registerAddress = '127.0.0.1:1236';

// 设置业务超时时间10秒
$worker->processTimeout = 10;
// 业务超时回调，可以把超时日志保存到自己想要的地方
$worker->processTimeoutHandler = function($trace_str, $exeption)
{
    file_put_contents('/your/path/process_timeout.log', $trace_str, FILE_APPEND);
    // 返回假，让进程重启，避免进程继续无限阻塞
    return false;
};

if(!defined('GLOBAL_START'))
{
    Worker::runAll();
}
```



