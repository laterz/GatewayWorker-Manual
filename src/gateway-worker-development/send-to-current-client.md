# Gateway::sendToCurrentClient

## 说明:
```php
void Gateway::sendToCurrentClient(string $send_data);
```

作用与Gateway::sendToClient相同，只不过是只能给当前用户发送

**注意：** 此接口不能用于定时器中，因为定时器执行的时候无法判断当前用户是哪个client_id
