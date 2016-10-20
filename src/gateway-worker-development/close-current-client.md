# Gateway::closeCurrentClient

## 说明:
```php
void Gateway::closeCurrentClient();
```

作用与Gateway::closeClient相同，只不过是断开当前客户端的连接

**注意：** 此接口不能用于定时器中，因为定时器执行的时候无法判断当前用户是哪个client_id



