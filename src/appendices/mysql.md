## Gateway/Worker模型 数据库使用示例

## 说明
常驻内存的程序在使用mysql时经常会遇到```mysql gone away```的错误，这个是由于程序与mysql的连接长时间没有通讯，连接被mysql服务端踢掉导致。GatewayWorker提供了一个mysql类，可以解决这个问题，当发生```mysql gone away```错误时，会自动重试一次。

## 提示
该mysql类依赖[pdo](http://php.net/manual/zh/book.pdo.php)和[pdo_mysql](http://php.net/manual/zh/ref.pdo-mysql.php)两个扩展，缺少扩展会报```Undefined class constant 'MYSQL_ATTR_INIT_COMMAND' in ....```错误。

命令行运行```php -m```会列出所有php cli已安装的扩展，如果没有pdo 或者 pdo_mysql，请自行安装。

**centos系统**
```
yum install php-pdo
yum install php-mysql
```
如果找不到包名，请尝试用```yum search php mysql```查找

**ubuntu/debian系统**

PHP5.x
```
apt-get install php5-mysql
```

PHP7.x
```
apt-get install php7.0-mysql
```

如果找不到包名，请尝试用```apt-cache search php mysql```查找

**以上方法无法安装？**

如果以上方法无法安装，请参考[workerman手册-附录-扩展安装-方法三源码编译安装](http://doc3.workerman.net/appendices/install-extension.html)。

## 注意
请在```onXXXX```回调中使用数据库类。在```Worker::runAll();```前初始化数据库单例会导致数据错乱，参看下面例子中错误用法部分和正确用法部分。

```php
use \Workerman\Worker;
use \GatewayWorker\BusinessWorker;
use \Workerman\Autoloader;
use \GatewayWorker\Lib\Db;

require_once __DIR__ . '/../../Workerman/Autoloader.php';
Autoloader::setRootPath(__DIR__);

/*
 * ## 错误用法 ###
 * 没有在onXXX回调环境中(在Worker::runAll()之前)实例化单例数据库会导致数据库错误风险
 * 原因：这部分代码属于主进程部分，主进程初始化了数据库单例，
 * 子进程会继承这个数据库单例(包括其中的数据库链接资源)，
 * 当mysql返回数据时，所有子进程都可以通过这个连接读取返回数据，
 * 导致数据错乱。
 * 同理：其它单例的连接资源(memcache\redis等)也不能在Worker::runAll运行前初始化。
 */
Db::instance('db1')->select('name,age')->from('users')->where('age>12')->query();

$worker = new BusinessWorker();
$worker->name = 'YourAppBusinessWorker';
$worker->count = 4;
$worker->registerAddress = '127.0.0.1:1238';

/*
 * ## 正确用法 ##
 * onXXX中是可以使用数据库单例，
 * 因为onXXX都是在Worker::runAll()之后运行的，属于子进程代码。
 * 这里初始化的数据库单例只属于当前子进程自己所有，
 * 不会因为进程继承导致的连接上数据错乱。
 *
 * Event:onXXX也是运行在子进程的，可以安全的使用数据库类。
 */
$worker->onWorkerStart = function()
{
    Db::instance('db1')->select('name,age')->from('users')->where('age>12')->query();
};

// 如果不是在根目录启动，则运行runAll方法
if(!defined('GLOBAL_START'))
{
    Worker::runAll();
}
```

## 数据库使用方法

1、数据库配置Applications/项目/Config/Db.php（如果没有此文件请手动创建），例如聊天室的话就是Applications/Chat/Config/Db.php
```php
<?php
namespace Config;
/**
 * mysql配置
 * @author walkor
 */
class Db
{
    /**
     * 数据库的一个实例配置，则使用时像下面这样使用
     * $user_array = Db::instance('db1')->select('name,age')->from('users')->where('age>12')->query();
     * 等价于
     * $user_array = Db::instance('db1')->query('SELECT `name`,`age` FROM `users` WHERE `age`>12');
     * @var array
     */
    public static $db1 = array(
        'host'    => '127.0.0.1',
        'port'    => 3306,
        'user'    => 'your_user_name',
        'password' => 'your_password',
        'dbname'  => 'your_db_name',
        'charset'    => 'utf8',
    );
}
```
**注意：**上面配置中```Config/Db::$db1```是数据库配置名，获取实例的方法与其对应是```Db::instance('db1')```，而不是```Db::instance('your_db_name')```。


2、项目/Event.php
```php
<?php
use \GatewayWorker\Lib\Gateway;
use \GatewayWorker\Lib\Db;

/**
 * 数据库示例，假设有个your_db_name库，里面有个user表
 */
class Event
{

   /**
    * 有消息时触发该方法，根据发来的命令打印2个用户信息
    * @param int $client_id 发消息的client_id
    * @param mixed $message 消息
    * @return void
    */
   public static function onMessage($client_id, $message)
   {
        // 发来的消息
        $commend = trim($message);
        if($commend !== 'get_user_list')
        {
            Gateway::sendToClient($client_id, "unknown commend\n");
            return;
        }
        // 注意，这里的db1对应的是Config/Db::$db1 的配置
        $ret = Db::instance('db1')->select('*')->from('users')->where('uid>3')->offset(5)->limit(2)->query();
        // 打印结果
        return Gateway::sendToClient($client_id, var_export($ret, true));
   }

}
```

# 数据库类使用的一些示例
## 配置
在Config/Db.php中配置数据库信息，如果有多个数据库，可以在Db.php中配置多个实例
例如下面配置了两个数据库实例

```php
<?php
namespace Config;
class Db
{
    // 数据库实例1
    public static $db1 = array(
        'host'    => '127.0.0.1',
        'port'    => 3306,
        'user'    => 'mysql_user',
        'password' => 'mysql_password',
        'dbname'  => 'database_name1',
        'charset'    => 'utf8',
    );

    // 数据库实例2
    public static $db2 = array(
        'host'    => '127.0.0.1',
        'port'    => 3306,
        'user'    => 'mysql_user',
        'password' => 'mysql_password',
        'dbname'  => 'database_name2',
        'charset'    => 'utf8',
    );
}
```
## 使用方法

```php
use \GatewayWorker\Lib\Db;
// 注意，这里的db1对应的是Config/Db::$db1 的配置
$db1 = Db::instance('db1');
// 注意，这里的db2对应的是Config/Db::$db2 的配置
$db2 = Db::instance('db2');

// 获取所有数据
$db1->select('ID,Sex')->from('Persons')->where('sex= :sex')->bindValues(array('sex'=>'M'))->query();
//等价于
$db1->select('ID,Sex')->from('Persons')->where("sex= 'F' ")->query();
//等价于
$db1->query("SELECT ID,Sex FROM `Persons` WHERE sex='M'");


// 获取一行数据
$db1->select('ID,Sex')->from('Persons')->where('sex= :sex')->bindValues(array('sex'=>'M'))->row();
//等价于
$db1->select('ID,Sex')->from('Persons')->where("sex= 'F' ")->row();
//等价于
$db1->row("SELECT ID,Sex FROM `Persons` WHERE sex='M'");


// 获取一列数据
$db1->select('ID')->from('Persons')->where('sex= :sex')->bindValues(array('sex'=>'M'))->column();
//等价于
$db1->select('ID')->from('Persons')->where("sex= 'F' ")->column();
//等价于
$db1->column("SELECT `ID` FROM `Persons` WHERE sex='M'");

// 获取单个值
$db1->select('ID,Sex')->from('Persons')->where('sex= :sex')->bindValues(array('sex'=>'M'))->single();
//等价于
$db1->select('ID,Sex')->from('Persons')->where("sex= 'F' ")->single();
//等价于
$db1->single("SELECT ID,Sex FROM `Persons` WHERE sex='M'");

// 复杂查询
$db1->select('*')->from('table1')->innerJoin('table2','table1.uid = table2.uid')->where('age > :age')->groupBy(array('aid'))->having('foo="foo"')->orderByASC/*orderByDESC*/(array('did'))->limit(10)->offset(20)->bindValues(arra
y('age' => 13));
// 等价于
$db1->query(SELECT * FROM `table1` INNER JOIN `table2` ON `table1`.`uid` = `table2`.`uid` WHERE age > 13 GROUP BY aid HAVING foo="foo" ORDER BY did LIMIT 10 OFFSET 20“);

// 插入
$insert_id = $db1->insert('Persons')->cols(array('Firstname'=>'abc', 'Lastname'=>'efg', 'Sex'=>'M', 'Age'=>13))->query();
等价于
$insert_id = $db1->query("INSERT INTO `Persons` ( `Firstname`,`Lastname`,`Sex`,`Age`) VALUES ( 'abc', 'efg', 'M', 13)");

// 更新
$row_count = $db1->update('Persons')->cols(array('sex'))->where('ID=1')->bindValue('sex', 'F')->query();
// 等价于
$row_count = $db1->update('Persons')->cols(array('sex'=>'F'))->where('ID=1')->query();
// 等价于
$row_count = $db1->query("UPDATE `Persons` SET `sex` = 'F' WHERE ID=1");

// 删除
$row_count = $db1->delete('Persons')->where('ID=9')->query();
// 等价于
$row_count = $db1->query("DELETE FROM `Persons` WHERE ID=9");

// 事务
$db1->beginTrans();
....
$db1->commitTrans(); // or $db1->rollBackTrans();

```
