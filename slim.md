%Slim 中文文档%

#Slim 中文文档

##Slim 安装##

**使用 Composer 进行安装**

首先在你的项目中安装 Composer：

    curl -s https://getcomposer.org/installer | php

然后在你的项目根目录中建立名为 composer.json 的文件：

    {
        "require": {
            "slim/slim": "2.*"
        }
    }

通过 composer 进行安装：

    php composer.phar install

添加下列代码到应用的 index.php 文件中：

    <?php
    require 'vendor/autoload.php';

**手动安装**

下载并解压 Slim 框架到你的项目目录，然后在应用的 index.php 文件中 require Slim框架。同时你还需要注册 Slim 框架自带的自动加载类。

    <?php
    require 'Slim/Slim.php';
    \Slim\Slim::registerAutoloader();

##系统依赖

    PHP >= 5.3.0
    如果你需要对cookies进行加密，则还需要 mcrypt 拓展。

##Hello World

生成一个 Slim 应用实例：

    $app = new \Slim\Slim();

定义一个 HTTP GET 请求路由：

    $app->get('/hello/:name', function($name){
                    echo "Hello, $name";
                });

执行 Slim 应用

    $app->run();

##配置

**配置概述**

Slim 框架提供了两种方式对其进行配置。一种是在生成实例的时候进行参数设置，另一种则是在生成实例之后。所有的设置参数都可以在生成实例的时候以数组的形式传递给 Slim 的构造函数（constructor）。所有的设置参数都可以在生成实例之后获取或者修改，但是有些设置并不能简单的只依靠应用实例的 config 函数来完成，因此有必要在后面额外说明。在我罗列这些有效设置参数之前，我想简单的介绍下怎么定义或检查 Slim 应用的参数设置。

**实例生成时**

生成实例时定义设置，只需要向 Slim 的构造器传递一个关联数组。

    <?php
    $app = new Slim(array(
                        'debug' => true
                    ));

**实例生成之后**

要在实例生成之后定义参数设置，大部分设置都可以使用应用实例的 config 函数；config 函数的第一个参数是设置的名称，第二个参数是设置的参数值。

    <?php
    $app->config('debug', false);

你也可以通过关联数组同时设置多个参数：

    <?php
    $app->config(array(
                    'debug' => true,
                    'templates.path' => ' ../templates'
                    ));

当你想要获取应用某项设置时，你同样可以使用 config 函数；不过这时你只需要传递一个参数 - 你需要检查的设置名称。如果你请求的设置不存在时将返回 null 值。

    <?php
    $settingValue = $app->config('templates.path'); // 返回 "../templates"

你并不仅仅局限在下面这些设置；你也可以定义属于自己的参数设置。

##应用设置

**mode**

它是应用当前操作模式的标识符。mode 设置并不影响 Slim 应用的内部功能。相应的，mode 设置只是为了让你能随意调用自己传递给 configureMode() 函数的函数代码。

应用的 mode 参数和环境变量一样，是在生成应用实例时通过以参数的形式传递给 Slim 应用的构造器（constructor）来声明的，并且不能再次修改。mode 设置参数可以是任何你想要的 --最经典的便是“development”，“test”和“production”，但是你可以随意使用其他想要的（比如：“foo”）。

    <?php
    $app = new \Slim\Slim(array(
                        'mode' => 'development'
                    ));
    // 数据类型：string，默认值：“development”。

**debug**

    注意！Slim 将错误信息都转换成了 ‘ErrorException’ 实例。

如果调试状态（debugging）被启用，Slim 将会使用 Slim 内置的错误处理函数（error handler）来显示未捕捉异常的诊断信息。如果调试状态被关闭，Slim 将会调用你自定义的错误处理函数，并将未捕捉的异常作为第一个，也是唯一一个参数传递给它。

    <?php
    $app = new \Slim\Slim(array(
                        'debug' => true
                    ));
    // 数据类型：boolean, 默认值：true
    
**log.writer**

使用自定义的 log writer 可以直接把日志内容输出到需要的目标上。默认情况下，Slim 的 logger 会把日志内容输出到 STDERR。如果你使用自定义的 log writer，它必须实现一下接口：

    public write(mixed $message, int $level);

write() 函数主要负责发送日志信息（不一定是字符串）发送到适当的输出目标（比如：文本文件，数据库或者远程的 web 服务）。

应用实例生成之后再指定 log writer，你就必须直接使用 Slim logger 的 setWriter() 函数：

    <?php
    // During instantiation
    $app = new \Slim\Slim(array(
                        'log.writer' => new \My\LogWriter();
                    ));

    // After instantiation
    $log = $app->getLog();
    $log->setWriter(new \My\LogWriter());

    // 数据类型：mixed, 默认值：\Slim\LogWriter

**log.level**

    注意！请使用 `\Slim\Log` 中定义的常量来代替数字。

Slim 有5个日志信息等级：

    \Slim\Log::DEBUG
    \Slim\Log::INFO
    \Slim\Log::WARN
    \Slim\Log::ERROR
    \Slim\Log::FATAL

应用设置中的 log.level 设置决定了日志信息中哪些会被关注，哪些会被忽略。比如，如果 log.level 设置是 \Slim\Log::INFO，那么 debug 信息就会被忽略，而 info，warn 和 fatal 信息则会被记录。

应用实例生成之后，你需要直接使用 Slim logger 的 setLevel()函数来修改此设置。

    <?php
    // During instantiation
    $app = new \Slim\Slim(array(
                        'log.level' => \Slim\Log::DEBUG;
                    ));

    // After instantiation
    $log = $app->getLog();
    $log->setLevel(\Slim\Log::WARN);

    // 数据类型：integer，默认值：\Slim\Log::DEBUG

**log.enabled**

这项设置决定 Slim 日志功能的开启和关闭。应用实例生成后，你需要直接使用 SLim logger 的 setEnabled()函数来修改此设置。

    <?php
    // During instantiation
    $app = new \Slim\Slim(array(
                        'log.enabled' => true;
                    ));

    // After instantiation
    $log = $app->getLog();
    $log->setEnabled(true);

    // 数据类型：boolean，默认值：true

**templates.path**

Slim 应用模板文件所在文件目录的相对或者绝对地址。这个路径会在 Slim 应用读取和渲染视图的时候被使用。

应用实例生成之后，你需要直接使用 Slim 视图（view）的 setTemplatesDirectory()函数来修改此设置。

    <?php
    // During instantiation
    $ap = new \Slim\Slim(array(
                        'templates.path' => './templates';
                    ));

    // After instantiation
    $view = $app->view();
    $view->setTemplatesDirectory('./templates');

    // 数据类型：string，默认值：'./templates'

**view**

试图类（View class）或者其实例都是供 Slim 使用。应用实例生成之后你需要使用 Slim 的 view()函数来修改。

    <?php
    // During instantiation
    $app = new \Slim\Slim(array(
                        'view' => new \My\View();
                    ));

    // After instantiation
    $app->view(new \My\View());

    // 数据类型：string | \Slim\View，默认值：\Slim\View

**cookies.lifetime**

该设置决定了 Slim 应用创建的 HTTP cookies 生命周期。如果值为整数，它必须是表示 cookie 过期时间的有效 UNIX 时间戳。如果值为字符串，那么其值经过 strtotime() 函数转换后也必须是 cookie 过期时间的有效 UNIX 时间戳。

    <?php
    // During instantiation
    $app = new \Slim\Slim(array(
                        'cookies.lifetime' => '20 minutes';
                    ));

    // After instantiation
    $app->config('cookies.lifetime', '20 minutes');

    // 数据类型：integer|string，默认值：'20 minutes'

**cookies.path**

如果 Slim 应用在调用 setCookie() 或者 setEncryptedCookie() 函数时没有指定 path 参数，那么该设置就是 HTTP cookie 的默认路径。

    <?php
    // During instantation
    $app = new \Slim\Slim(array(
                        'cookies.path' => '/';
                    ));

    // After instantiation
    $app->config('cookies.path', '/');

    // 数据类型：string，默认值：'/'

**cookies.domain**

如果 Slim 应用在调用 setCookie() 或者 setEncryptedCookie() 函数时没有指定 domain 参数，那么该设置就是 HTTP domain 的默认值。

    <?php
    // During instantiation
    $app = new \Slim\Slim(array(
                        'cookies.domain' => 'domain.com';
                    ));

    // After instantiation
    $app->config('cookies.domain', 'domain.com');

    // 数据类型：string，默认值：null

**cookies.secure**

该设置决定 cookies 是否只在 HTTPS 请求中发送。你可以通过 setCookie() 或者 setEncryptedCookie() 函数的 secure 参数来覆盖该设置。

    <?php
    // During instantiation
    $app = new \Slim\Slim(array(
                        'cookies.secure' => false;
                    ));

    // After instantiation
    $app->config('cookies.secure', false);

    // 数据类型：boolean，默认值：false

**cookies.httponly**

该设置决定 cookies 是否只在 HTTP 协议中发送。你可以通过调用 Slim 应用的 setCookie() 或者 setEncryptedCookie() 函数来覆盖该设置。

    <?php
    // During instantiation
    $app = new \Slim\Slim(array(
                        'cookies.httponly' => false;
                    ));

    // After instantiation
    $app->config('cookies.httponly', false);

    // 数据类型：boolean，默认值：false

**cookies.secret_key**

供 cookie 加密的加密关键词。当你的 Slim 应用开启了 HTTP cookies 加密，务必要修改此设置。

    <?php
    // During instantiation
    $app = new \Slim\Slim(array(
                        'cookies.secret_key' => 'secret'；
                    ));

    // After instantiation
    $app->config('cookies.secret_key', 'secret');

    // 数据类型：string，默认值：'CHANGE_ME'

**cookies.cipher**

该加密密匙供 HTTP cookie 加密使用。查看[现有密匙](http://php.net/manual/en/mcrypt.ciphers.php)。

    <?php
    // During instantiation
    $app = new \Slim\Slim(array(
                        'cookies.cipher' => MCRYPT_DIJNDAEL_256;
                    ));

    //After instantiation
    $app->config('cookies.cipher', MCRYPT_DIJNDAEL_256);

    // 数据类型：integer，默认值：MCRYPT_DIJNDEAL_256

**cookies.cipher_mode**

该密匙模式供 HTTP cookie 加密使用。查看[现有密匙模式](http://php.net/manual/en/mcrypt.ciphers.php)。

    <?php
    // During instantiation
    $app = new \Slim\Slim(array(
                        'cookies.cipher_mode' => MCRYPT_MODE_CBC;
                    ));

    // After instantiation
    $app->config('cookies.cipher_mode', MCRYPT_MODE_CBC);

    // 数据类型：integer，默认值：MCRYPT_MODE_CBC

**http.version**

默认情况下，Slim 会想客户端返回 HTTP/1.1 响应头。如果你需要返回 HTTP/1.0，可以修改此设置。当你使用 PHPFog 或 nginx 服务器，要使用代理而非直接与 HTTP 客户端通信时，此设置尤为有用。

    <?php
    // During instantiation
    $app = new \Slim\Slim(array(
                        'http.version' => '1.1';
                    ));

    // After instantiation
    $app->config('http.version', '1.1');

    // 数据类型：string，默认值：'1.1'

##应用名称与作用域

当你建立了一个 Slim 应用时，可能会在代码中用到多种作用域（比如全局作用域和函数作用域）。你应该希望在任何作用域中引用你的 Slim 应用。这里提供了两种方式：

    - 把应用名称传递给 Slim 应用的 getInstance() 静态函数。
    - 通过使用 use 关键词 把应用实例导入到函数作用域。

**应用名称**

你可以为每一个 Slim 应用取名。这是可选的。应用名称可以帮助你在代码的任何作用域中引用 Slim 应用，下面是设置和获取应用名称的方法：

    <?php
    $app = new \Slim\Slim();
    $app->setName('foo');
    $name = $app->getName(); // 'foo'
