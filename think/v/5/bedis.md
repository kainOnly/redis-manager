# Bedis 缓存模型

## SmsString 短信验证

手机短信验证码缓存类

### 设置手机验证码缓存

- factory($phone, $code, $timeout)
  - phone `string`，手机号
  - code `string`，验证码
  - timeout `int`，超时时间，默认 60 秒
  - return `bool`

```php
$sms = new SmsString();
$sms->factory('12345678910', '13125');
```

### 验证手机验证码

- check($phone, $code, $once)
  - phone `string`，手机号
  - code `string`，验证码
  - once `bool`，验证成功后失效，默认`false`
  - return `bool`

```php
$sms = new SmsString();
$checked = $sms->check('12345678910', '11224');
dump($checked);
// false
$checked = $sms->check('12345678910', '13125');
dump($checked);
// true
$checked = $sms->check('12345678910', '13125', true);
dump($checked);
// true
$checked = $sms->check('12345678910', '13125');
dump($checked);
// false
```

### 获取验证时间

- time($phone)
  - phone `string`，手机号
  - return `bool|array`

```php
$sms = new SmsString();
$sms->factory('12345678910', '13125', 3600);

$data = $sms->time('12345678910');
dump($data);
// array (size=2)
//   'publish_time' => int 1548644216
//   'timeout' => int 3600
```

- publish_time 指发布时间
- timeout 指有效时间