# getUidByClientId

目前并没有这个接口，开发者可以将uid存储到session中，获取的时候从session获取即可。

如果是获取当前请求所属client_id的session，直接使用```$_SESSION```变量即可。

如果想获取任意client_id的session，可以通过```Gateway::getSession($client_id)```获取。

```php
class Events
{
    public static function onMessage($client_id, $data)
    {
        // session中没有uid，说明没用绑定uid，执行uid绑定并存储uid到session
        if (!isset($_SESSION['uid'])) {
            // 一般是根据$data获取uid，这里为了演示方便随机生成uid
            $uid = rand(1, 10000);
            // uid用session存储起来，避免重复绑定
            $_SESSION['uid'] = $uid;
            // 绑定uid
            Gateway::bindUid($client_id, $uid);
            // 通知客户端uid生成并存储成功
            return Gateway::sendToClient($client_id, 'uid设置成功'.$uid);
        }

        // session中有uid，证明已经绑定过uid
        return Gateway::sendToClient($client_id, '你的uid为'.$_SESSION['uid']);
    }
}
```
