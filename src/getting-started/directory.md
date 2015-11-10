# 目录结构
<pre>
GatewayWorker            // Gateway/Worker模型公共类库
   ├── BusinessWorker.php   // Worker服务业务进程文件
   ├── Gateway.php          // Gateway服务进程文件
   ├── Register.php         // 服务注册进程文件
   └── Lib                  // 类库
       ├── Context.php      // Gateway与Worker通讯的上下文
       ├── DbConnection.php // 数据库连接类
       ├── Db.php           // 数据库连接管理类
       ├── Gateway.php      // 与客户端通讯的基础类
       ├── Lock.php         // 锁相关
       ├── Store.php        // 存储管理类
       └── StoreDriver      // 存储引擎相关
             ├── Redis.php    // Redis存储引擎
             └── File.php     // 文件存储引擎
</pre>
