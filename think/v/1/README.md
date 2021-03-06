# Think Api Case

辅助 ThinkPHP(5) 框架的工具集合使用案例，构建简洁统一的中后台接口方案

> 前端对应开源项目 https://github.com/kainonly/ngx-bit

首选需要创建一个 thinkphp 官方的骨架项目

```shell script
composer create-project topthink/think tp
```

然后安装必备的 CURD API 的工具集 `kain/think-bit`

```shell script
composer require kain/think-bit
```

案例中使用 `kain/think-extra` 包含了一些常用的扩展工具

```shell script
composer require kain/think-extra
```

`kain/think-support` 辅助 ThinkPHP 的特性功能支持库

```shell script
composer require kain/think-support
```

推荐库

- [topthink/think-helper](https://www.kancloud.cn/manual/thinkphp6_0/1149630) Think 助手工具库
- [guzzlehttp/guzzle](http://docs.guzzlephp.org/en/stable/) GuzzleHttp 请求库
- [nesbot/carbon](https://carbon.nesbot.com/docs/) Carbon 时间处理库
- [overtrue/wechat](https://www.easywechat.com/docs) EasyWechat 微信第三方库
- [overtrue/easy-sms](https://github.com/overtrue/easy-sms) EasySMS 短信库
- [overtrue/pinyin](https://github.com/overtrue/pinyin) Pinyin 拼音库
- [casbin/casbin](https://github.com/php-casbin/php-casbin/blob/master/README_CN.md) PHP-Casbin 授权库
- [swiftmailer/swiftmailer](https://swiftmailer.symfony.com/docs/introduction.html) swiftmailer 邮件库
