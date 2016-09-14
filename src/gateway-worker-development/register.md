# Register类的使用
Register类其实也是基于基础的Worker开发的。Gateway进程和BusinessWorker进程启动后分别向Register进程注册自己的通讯地址，Gateway进程和BusinessWorker通过Register进程得到通讯地址后，就可以建立起连接并通讯了。

## 注意
注意，客户端不要连接Register服务的端口，Register服务是GatewayWorker内部通讯用的。参见[GatewayWorker工作原理](http://workerman.net/gatewaydoc/process-of-communication/README.html)。

## Register类可以定制的内容

Register类只能定制监听的ip和端口，并且目前只能使用text协议。



