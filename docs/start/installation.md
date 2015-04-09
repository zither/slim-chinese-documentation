# 系统需求

* 支持 URL 重写的 Web 服务器
* PHP 5.4.0 或者更新版本

# Composer 安装

推荐使用 Composer 来安装 Slim 框架，你只需要切换到项目目录并执行以下 bash 命令，就可以将 Slim 框架及其第三方依赖都下载到项目的 `vendor/` 目录中。

    composer require slim/slim

然后在你的 PHP 脚本中载入 Composer 的自动加载器。

    <?php
    require 'vendor/autoload.php';

# 手动安装

在没有 Composer 的环境中，同样可以安装 Slim 框架。先将 Slim 框架的所有文件下载到项目目录，然后在 PHP 脚本中载入 `Slim\Autoloader.php` 并调用它的 `register()` 静态方法。

    <?php
    require 'Slim/Autoloader.php';
    \Slim\Autoloader::register();

如果你采用手工安装 Slim 的方式，还必须安装下面这些第三方依赖，并解决自动加载的相关问题：

* [Pimple 3.x](http://pimple.sensiolabs.org/)
* [PSR HTTP Message 0.x](https://github.com/php-fig/http-message)
* [FastRoute 4.x](https://github.com/nikic/FastRoute/)