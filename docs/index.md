# Slim 框架

[![Build Status](https://travis-ci.org/slimphp/Slim.svg?branch=develop)](https://travis-ci.org/slimphp/Slim)

Slim 是一个非常优雅的 PHP 微框架，可以帮助你快速编写简单但功能强大的网页应用和 API 服务。通过以下链接可以了解更多信息：

- [官方网站](http://www.slimframework.com)
- [文档](http://docs.slimframework.com)
- [论坛支持](http://help.slimframework.com)
- [Twitter](https://twitter.com/slimphp)

## 安装

使用 [Composer](https://getcomposer.org/)

```bash
$ composer require slim/slim
```

需要 PHP 5.4.0 或者更新版本。

## 使用方法

```php
$app = new \Slim\App();
$app->get('/hello/{name}', function ($request, $response, $args) {
    echo "Hello, " . $args['name'];
});
$app->run();
```

## 测试

```bash
phpunit
```

## 参与贡献

请阅读 [CONTRIBUTING](https://github.com/slimphp/Slim/blob/master/CONTRIBUTING.md) 获取详细信息。

## 安全

如果你发现了任何与安全有关的问题，请勿使用 issue tracker，请直接发送邮件到 security@slimframework.com。

## 感谢（Credits）

- [Josh Lockhart](https://github.com/codeguy)
- [Andrew Smith](https://github.com/silentworks)
- [Gabriel Manricks](https://github.com/gmanricks) 
- [All Contributors](https://github.com/slimphp/Slim/graphs/contributors)

## 许可证

The MIT License (MIT). Please see [License File](https://github.com/slimphp/Slim/blob/master/LICENSE.md) for more information.
