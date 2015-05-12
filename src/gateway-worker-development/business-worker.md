# BusinessWorker类的使用
BusinessWorker类其实也是基于基础的Worker开发的。由于BusinessWorker进程中无法直接操作Gateway进程的连接，也就无法获得连接对象，所以无法使用BusinessWorker的onConnect、onMessage、onClose属性，请开发者不要设置以上回调属性。开发者仍然可以设置onWorkerStart、onWorkerStop属性。

BusinessWorker的相关回调函数在项目中的Event.php中定义，具体内容参见下一节

## BusinessWorker类可以定制的内容

1、name

和Worker一样，可以设置BusinessWorker进程的名称，方便status命令中查看统计

2、count

和Worker一样，可以设置BusinessWorker进程的数量，以便充分利用多cpu资源

3、onWorkerStart

和Worker一样，可以设置BusinessWorker启动后的回调函数，一般在这个回调里面初始化一些全局数据

4、onWorkerStop

和Worker一样，可以设置BusinessWorker关闭的回调函数，一般在这个回调里面做数据清理或者保存数据工作

## 业务处理类 Event

Event类为业务处理的入口文件，当有客户端事件发生时会触发相应的回调如下：

1、当客户端连接到Gateway时，会触发```Event::onConnect($client_id)```回调。

2、当客户端发来数据时，会触发```Event::onMessage($client_id, $data)```回调。

3、当客户端关闭时，会触发```Event::onClose($client_id)```回调。

**Event详细文档参见下一节**




