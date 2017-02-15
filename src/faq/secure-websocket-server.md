# 创建wss服务

**准备工作：**

1、Workerman版本不小于3.3.7

2、PHP安装了openssl扩展

3、已经申请了证书（pem/crt文件及key文件）放在了/etc/nginx/conf.d/ssl下


**start_gateway.php中设置以下代码。**

```php
$context = array(
    'ssl' => array(
        'local_cert' => '/etc/nginx/conf.d/ssl/server.pem', // 或者crt文件
        'local_pk'   => '/etc/nginx/conf.d/ssl/server.key',
    )
);
// websocket协议
$gateway = new Gateway("websocket://0.0.0.0:7272", $context);
// 开启SSL，websocket+SSL 即wss
$gateway->transport = 'ssl';
```

``` 注意：证书一般是与域名绑定的，测试的时候请使用域名连接测试。 ```
