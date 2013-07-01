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
                        'log.writer' => new \My\LogWriter()
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
                        'log.level' => \Slim\Log::DEBUG
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
                        'log.enabled' => true
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
                        'templates.path' => './templates'
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
                        'view' => new \My\View()
                    ));

    // After instantiation
    $app->view(new \My\View());

    // 数据类型：string | \Slim\View，默认值：\Slim\View

**cookies.lifetime**

该设置决定了 Slim 应用创建的 HTTP cookies 生命周期。如果值为整数，它必须是表示 cookie 过期时间的有效 UNIX 时间戳。如果值为字符串，那么其值经过 strtotime() 函数转换后也必须是 cookie 过期时间的有效 UNIX 时间戳。

    <?php
    // During instantiation
    $app = new \Slim\Slim(array(
                        'cookies.lifetime' => '20 minutes'
                    ));

    // After instantiation
    $app->config('cookies.lifetime', '20 minutes');

    // 数据类型：integer|string，默认值：'20 minutes'

**cookies.path**

如果 Slim 应用在调用 setCookie() 或者 setEncryptedCookie() 函数时没有指定 path 参数，那么该设置就是 HTTP cookie 的默认路径。

    <?php
    // During instantation
    $app = new \Slim\Slim(array(
                        'cookies.path' => '/'
                    ));

    // After instantiation
    $app->config('cookies.path', '/');

    // 数据类型：string，默认值：'/'

**cookies.domain**

如果 Slim 应用在调用 setCookie() 或者 setEncryptedCookie() 函数时没有指定 domain 参数，那么该设置就是 HTTP domain 的默认值。

    <?php
    // During instantiation
    $app = new \Slim\Slim(array(
                        'cookies.domain' => 'domain.com'
                    ));

    // After instantiation
    $app->config('cookies.domain', 'domain.com');

    // 数据类型：string，默认值：null

**cookies.secure**

该设置决定 cookies 是否只在 HTTPS 请求中发送。你可以通过 setCookie() 或者 setEncryptedCookie() 函数的 secure 参数来覆盖该设置。

    <?php
    // During instantiation
    $app = new \Slim\Slim(array(
                        'cookies.secure' => false
                    ));

    // After instantiation
    $app->config('cookies.secure', false);

    // 数据类型：boolean，默认值：false

**cookies.httponly**

该设置决定 cookies 是否只在 HTTP 协议中发送。你可以通过调用 Slim 应用的 setCookie() 或者 setEncryptedCookie() 函数来覆盖该设置。

    <?php
    // During instantiation
    $app = new \Slim\Slim(array(
                        'cookies.httponly' => false
                    ));

    // After instantiation
    $app->config('cookies.httponly', false);

    // 数据类型：boolean，默认值：false

**cookies.secret_key**

供 cookie 加密的加密关键词。当你的 Slim 应用开启了 HTTP cookies 加密，务必要修改此设置。

    <?php
    // During instantiation
    $app = new \Slim\Slim(array(
                        'cookies.secret_key' => 'secret'
                    ));

    // After instantiation
    $app->config('cookies.secret_key', 'secret');

    // 数据类型：string，默认值：'CHANGE_ME'

**cookies.cipher**

该加密密匙供 HTTP cookie 加密使用。查看[现有密匙](http://php.net/manual/en/mcrypt.ciphers.php)。

    <?php
    // During instantiation
    $app = new \Slim\Slim(array(
                        'cookies.cipher' => MCRYPT_DIJNDAEL_256
                    ));

    //After instantiation
    $app->config('cookies.cipher', MCRYPT_DIJNDAEL_256);

    // 数据类型：integer，默认值：MCRYPT_DIJNDEAL_256

**cookies.cipher_mode**

该密匙模式供 HTTP cookie 加密使用。查看[现有密匙模式](http://php.net/manual/en/mcrypt.ciphers.php)。

    <?php
    // During instantiation
    $app = new \Slim\Slim(array(
                        'cookies.cipher_mode' => MCRYPT_MODE_CBC
                    ));

    // After instantiation
    $app->config('cookies.cipher_mode', MCRYPT_MODE_CBC);

    // 数据类型：integer，默认值：MCRYPT_MODE_CBC

**http.version**

默认情况下，Slim 会想客户端返回 HTTP/1.1 响应头。如果你需要返回 HTTP/1.0，可以修改此设置。当你使用 PHPFog 或 nginx 服务器，要使用代理而非直接与 HTTP 客户端通信时，此设置尤为有用。

    <?php
    // During instantiation
    $app = new \Slim\Slim(array(
                        'http.version' => '1.1'
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
    
**作用域解析**

那么你怎样才可以获取 Slim 应用的引用呢？下面这个例子演示了如何在路由的回调函数中获取一个 Slim 应用的引用。$app 变量已经在定义 HTTP GET路由的全局作用域中使用。但是在路由回调函数的函数作用域中也需要使用 $app 变量来渲染模板。

    <?php
    $app = new \Slim\Slim();
    $app->get('/foo', function(){
                    $app->render('foo.php'); // <---- 错误
                });

这个例子运行错误是因为 $app 变量在路由回调函数的函数作用域中是未定义的。

**柯里化（Currying）**

我们可以使用 use 关键词把 $app 变量注入到回调函数的作用域中：

    <?php
    $app = new \Slim\Slim();
    $app->get('/foo', function() use($app){
                    $app->render('foo.php'); // <---- 成功
                });

**按名称获取**

你也可以在 Slim 应用中使用 getInstance() 静态函数：

    <?php
    $app = new \Slim\Slim();
    $app->get('/foo', 'foo');
    function foo(){
        $app = Slim::getInstance();
        $app->render('foo.php');
    }

##应用模式

一般来说，网页应用运行在指定模式都决定于项目当前的状态。如果你在开发应用，那么你就应该在“development”模式中运行应用；如果你在测试应用，你就应该在“testing”模式中运行应用；如果你发布了应用，那么你的应用在“production”模式中运行应用。

Slim 提供了应用模式的概念，你可以定义需要的模式以便 Slim 为当前模式的运行作适当的准备。 比如，你想要在“development”模式中开启调试（debugging）功能，但需在“production”模式中关闭。下面的例子向您演示了如何为 Slim 设置不同的模式。

**什么是模式**

从技术上来说，应用模式只不过是由字符串组成的文字，就像“development”或者“production”，有一个与之相关联的回调函数为 Slim 应用的运行作适当的配置。应用模式可以是任何名字，就比如“testing”，“production”，“development”甚至是“foo”。

**我应该如何设置应用模式？**

    注意！应用模式只能在应用实例化时设置，之后无法更改。

**使用环境变量**

如果 Slim 检测到名为 “SLIM_MODE” 的环境变量，它就会把应用模式设置为该值。

    <?php
    $_ENV['SLIM_MODE'] = 'production';

**使用应用设置**

如果环境变量未定义时，Slim 会继续检查应用设置中的模式参数。

    <?php
    $app = new \Slim\Slim(array(
                        'mode' => 'production'
                    ));

**默认模式**

如果环境变量和应用设置中均未定义模式，Slim 就会将应用模式设置为“development”。

**配置指定模式**

当你实例化一个 Slim 应用之后，你可以使用 Slim 应用的 configureMode() 函数对 Slim 应用的指定模式进行配置。这个函数接受两个参数：目标模式的名称以及当前应用模式与第一个参数相匹配时需要执行的回调函数。

假如应用当前模式为“produciton”，那么只有与“production”模式相关联的回调函数会被调用。而与“development”模式关联的回调函数将会被忽略，直到应用模式改变为“development”。

    <?php
    // 设置当前模式
    $app = new \Slim\Slim(array(
                        'mode' => 'production'
                    ));

    // 只在“production”模式中执行
    $app->configureMode('production', function() use($app){
                    $app->config(array(
                                'log.enable' => true,
                                'debug' => false
                            ));
                });

    // 只在“development”模式中执行
    $app->configureMode('development', function() use($app){
                    $app->config(array(
                                'log.enable' => false,
                                'debug' => true
                            ));
                });

##路由

**路由概述**

Slim 框架帮助你将应用 URIs 映射到特定 HTTP 请求方法（比如：GET，POST，PUT，DELETE，OPTION 或 HEAD）的回调函数上。Slim 应用将会调用与当前 HTTP 请求 的 URI 和方法相匹配的第一个路由。

如果 Slim 应用未找到与 HTTP 请求 URI 和方法相配备的路由，它将自动返回一个 404 Not Found 响应。

**GET 路由**

使用 Slim 应用的 get() 函数会将一个回调函数映射到 一个由 HTTP GET 方法请求的资源 URI。

    <?php
    $app = new \Slim\Slim();
    $app->get('/book/:id', function($id){
                    // 根据书籍 $id 显示数据
                });

在这个例子中，当使用 HTTP GET 向“/books/1” 发送请求时，与它相关联的回调函数将会被执行，并会把“1”作为参数传递给回调函数。

Slim 应用 get() 函数的第一个参数是资源 URI。最后一个参数是经 is_callable() 检测后会返回 true 的任何东西。通常来说，最后一个参数是一个[匿名函数](http://php.net/manual/en/functions.anonymous.php)。

**POST 路由**

使用 Slim 应用的 post() 函数会将一个回调函数映射到 一个由 HTTP POST 方法请求的资源 URI。

    <?php
    $app = new \Slim\Slim();
    $app->post('/books', function(){
                    // 创建书籍
                });

在这个例子中，当使用 HTTP POST 向“/books”发送请求时，与它相关的回调函数将会被执行。

Slim 应用 post() 函数的第一个参数是资源 URI。最后一个参数是经 is_callable() 检测后会返回 true 的任何东西。通常来说，最后一个参数是一个[匿名函数](http://php.net/manual/en/functions.anonymous.php)。

**PUT 路由**

使用 Slim 应用的 put() 函数会将一个回调函数映射到 一个由 HTTP PUT 方法请求的资源 URI。

    <?php
    $app = new \Slim\Slim();
    $app->put('/books/:id', function($id){
                    // 更新 $id 对应的书籍
                });

在这个例子中，当使用 HTTP PUT 向“/books/1” 发送请求时，与它相关联的回调函数将会被执行，并会把“1”作为参数传递给回调函数。

Slim 应用 put() 函数的第一个参数是资源 URI。最后一个参数是经 is_callable() 检测后会返回 true 的任何东西。通常来说，最后一个参数是一个[匿名函数](http://php.net/manual/en/functions.anonymous.php)。

**Method 覆盖**

不幸的是当前的浏览器并不原生支持 HTTP PUT 请求。为了解决这个问题，请确保你的 HTML 表单的请求方式为“post”，然后像下面例子一样，在 HTML 表单中添加一项请求方式覆盖参数：

    <form action="/books/1" method="post">
        ... 其他表单域 ...
        <input type="hidden" name="_METHOD" value="PUT" />
        <input type="submit" value="Update Book"/>
    </form>

如果你正在使用 [Backbone.js](http://documentcloud.github.com/backbone/) 或者 HTTP 命令行客户端，你同样可以使用 X-HTTP-Method-Override 请求头来覆盖 HTTP 请求方式。

**DELETE 路由**

使用 Slim 应用的 delete() 函数会将一个回调函数映射到 一个由 HTTP DELETE 方法请求的资源 URI。

    <?php
    $app = new \Slim\Slim();
    $app->delete('/books/:id', function($id){
                    // 删除 $id 对应的书籍
                });

在这个例子中，当使用 HTTP DELETE 向“/books/1” 发送请求时，与它相关联的回调函数将会被执行，并会把“1”作为参数传递给回调函数。

Slim 应用 delete() 函数的第一个参数是资源 URI。最后一个参数是经 is_callable() 检测后会返回 true 的任何东西。通常来说，最后一个参数是一个[匿名函数](http://php.net/manual/en/functions.anonymous.php)。

**Method 覆盖**

不幸的是当前的浏览器并不原生支持 HTTP DELETE 请求。为了解决这个问题，请确保你的 HTML 表单的请求方式为“post”，然后像下面例子一样，在 HTML 表单中添加一项请求方式覆盖参数：

    <form action="/books/1" method="post">
        ... 其他表单域 ...
        <input type="hidden" name="_METHOD" value="DELETE" />
        <input type="submit" value="Update Book"/>
    </form>

如果你正在使用 [Backbone.js](http://documentcloud.github.com/backbone/) 或者 HTTP 命令行客户端，你同样可以使用 X-HTTP-Method-Override 请求头来覆盖 HTTP 请求方式。

**OPTIONS 路由**

使用 Slim 应用的 options() 函数会将一个回调函数映射到 一个由 HTTP DELETE 方法请求的资源 URI。

    <?php
    $app = new \Slim\Slim();
    $app->options('/books/:id', function($id){
                    // 返回响应头
                });

在这个例子中，当使用 HTTP OPTIONS 向“/books/1” 发送请求时，与它相关联的回调函数将会被执行，并会把“1”作为参数传递给回调函数。

Slim 应用 options() 函数的第一个参数是资源 URI。最后一个参数是经 is_callable() 检测后会返回 true 的任何东西。通常来说，最后一个参数是一个[匿名函数](http://php.net/manual/en/functions.anonymous.php)。

**Method 覆盖**

不幸的是当前的浏览器并不原生支持 HTTP OPTIONS 请求。为了解决这个问题，请确保你的 HTML 表单的请求方式为“post”，然后像下面例子一样，在 HTML 表单中添加一项请求方式覆盖参数：

    <form action="/books/1" method="post">
        ... 其他表单域 ...
        <input type="hidden" name="_METHOD" value="OPTIONS" />
        <input type="submit" value="Update Book"/>
    </form>

如果你正在使用 [Backbone.js](http://documentcloud.github.com/backbone/) 或者 HTTP 命令行客户端，你同样可以使用 X-HTTP-Method-Override 请求头来覆盖 HTTP 请求方式。

**自定义 HTTP 方法**

**一个路由对应多个 HTTP 方法**

有时候我可能需要通过一个路由响应多个 HTTP 方法；有时候我可能需要一个响应自定义 HTTP 方法的路由。你可以使用路由对象的 via() 函数来完成。这个例子演示了如何将一个资源 URI 映射到可以响应多种 HTTP 方法请求的回调函数。

    <?php
    $app = new \Slim\Slim();
    $app->map('/foo/bar', function(){
                    echo "I respond to multiple HTTP methods!";
                })->via('GET', 'POST');
    $app->run();

这个例子中定义的路由将会同时响应请求资源定位符 “/foo/bar” 的 GET 和 POST 两种方法。只需要把每个 HTTP 请求方法以字符串的形式作为参数传递给路由对象 via() 函数。同路由的其他函数一样（比如：name() 和 conditions()），via() 函数也支持链式调用。

    <?php
    $app = new \Slim\Slim();
    $app->map('/foo/bar', function(){
                    echo "Fancy,huh?";
                })->via('GET', 'POST')->name('foo');
    $app->run();

**路由参数**

你可以在路由的资源 URIs 中加入参数。在这个例子中，我在路由 URI 中加入了两个参数：“:one”和“:two”。

    <?php
    $app = new \Slim\Slim();
    $app->get('/books/:one/:two', function($one, $two){
                    echo "The first paramter is " . $one;
                    echo "The second paramter is " . $two;
                });

创建路由参数时，只需要在路由 URI 的参数前面加上“:”。当路由和当前 HTTP 请求相匹配时，路由中的每个参数都会从 HTTP 请求 URI 中提取出来，并把它们按照适当的顺序传递给相关联的回调函数。

**通配符路由参数**

你也可以在路由中使用通配符参数。与通配符参数相匹配的一个或者多个 URI 字段将会被捕捉并以数组的形式保存。通配符参数的标志就是以“+”为后缀，其他地方和前面讲过的普通参数相同。下面是一个例子：

    <?php
    $app = new \Slim\Slim();
    $app->get('/hello/:name+', function($name){
                    // Do something
                });

当你用“/hello/Josh/T/Lockhart”作为资源 URI 调用上面这个应用，回调函数的 $name 参数就等于 array('Josh', 'T', 'Lockhart')。

**可选路由参数**

    注意！路由的可选字段还在实验中。只应当用如下演示例子的方式使用。

你也可以定义可选路由参数。这是在博客归档时使用可选路由参数的例子，你可以用例子中的方式来指定可选路由参数：

    <?php
    $app = new \Slim\Slim();
    $app->get('/archive(/:year(/:month(/:day)))', function($year = 2010, $month = 12, $day = 05){
                    echo sprintf('%s-%s-%s', $year, $month, $day);
                });

每个路由字段都是可选的，这个路由将会接受如下 HTTP 请求：

    - /archive
    - /archive/2010
    - /archive/2010/12
    - /archive/2010/12/05

如果一个 HTTP 请求的可选参数被省略，回调函数中定义的默认值会被代替使用。

目前你只能如以上示例定义可选参数的方式来设置路由字段。如果使用和示例中不同的方式，你可以发现它并不能稳定运行。

**路由名称**

Slim 允许你为路由单独命名。为路由命名可以让你使用 urlFor 帮助函数来动态生成 URLs。当你使用 Slim 应用的 urlFor() 函数来创建应用 URLs，你可以自由的在不破坏应用的情况下修改路由参数。下面是一个命名路由：

    <?php
    $app = new \Slim\Slim();
    $app->get('/hello/:name', function($name){
                    echo "Hello, $name!";
                })->name('hello');

现在你可以使用 urlFor() 函数来生成 URLs，这点会在后面讲解。路由的 name() 函数同样支持链式调用：

    <?php
    $app = new \Slim\Slim();
    $app->get('/hello/:name', function($name){
                    echo "Hello, $name!";
                })->name('hello')->conditions(array('name' => '\w+'));

**路由条件**

Slim 可以让你指定路由参数的满足条件。如果未满足指定条件，那么路由就不会执行。例如，如果你需要一个路由，它的第二个字段 year 必须是4个整数，你可以这样设定条件：

    <?php
    $app = new \Slim\Slim();
    $app->get('archive/:year', function($year){
                    echo "You are viewing archives from $year";
                })->conditions(array('year' => '(19|20)\d\d'));

调用路由对象的 conditions() 函数，第一个也是唯一一个参数是一个关联数组，关键字是所有匹配的路由参数，值为正则表达式。

**应用泛路由条件**

如果你 Slim 应用的很多路由都接受同样的参数和使用同样的条件，你就可以像这样定义一个默认的泛路由条件：

    <?php
    \Slim\Route::setDefaultConditions(array(
                        'firstName' => '[a-zA-Z]{3,}'
                    ));

在你定义应用路由之前定义应用的泛路由条件。当你定义了一个路由，它将自动指定由 \Slim\Route::setDefaultCondtions() 定义的所有泛路由条件。不管出于什么原因，你都可以使用 \Slim\Route::getDefaultConditions() 来获取已定义的应用泛路由条件。这个函数将返回所有已定义的默认路由条件。

当定义路由时，你可以像下面一样通过重新定义路由条件来覆盖默认路由条件：

    <?php
    $app = new \Slim\Slim();
    $app->get('/hello/:firstName', $callable)
        ->conditions(array('firstName' => '[a-z]{10,}'));

你也可以通过如下方式来为路由追加条件：

    <?php
    $app = new \Slim\Slim();
    $app->get('/hello/:firstName/:lastName', $callable)
        ->conditions(array('lastName' => '[a-z]{10,}'));

**路由中间件**

Slim 可以让你为指定路由关联中间件。当路由和当前 HTTP 请求相匹配被调用时，Slim 将在第一时间按顺序调用与之相关联的中间件。

**什么是路由中间件**

路由中间件是 is_callable 会返回 true 的任何事物。路由中间件会在路由回调函数被调用前按顺序被执行。

**如何添加路由中间件**

当你在使用 Slim 应用的 get()，post()，put()，delete() 函数定义一个新的应用路由时，你必须定义一个路由模式以及当路由和 HTTP 请求匹配时调用的回调函数。

    <?php
    $app = new \Slim\Slim();
    $app->get('/foo', function(){
                    // Do something
                });

在上面的例子中，第一个参数是路由模式，最后一个参数时与当前 HTTP 请求匹配时要调用的回调函数。路由模式必须时第一个参数，回调函数必须是最后一个参数。

你可以把每个中间件分别作为中间参数传递给路由来指定中间件：

    <?php
    function mw1(){
        echo "This is middleware!";
    }
    function mw2(){
        echo "This is middleware!";
    }
    $app = new \Slim\Slim();
    $app->get('/foo', 'mw1', 'mw2', function(){
                    // Do something
                });

当/foo 路由被调用时，mw1 和 mw2 函数也将会在路由回调函数调用之前按顺序执行。

假如你想要在路由中对当前用户进行认证，你可以像下面一样使用匿名函数：

    <?php
    $authenticateForRole = function($role = 'member') {
        return function() use($role){
            $user = User::fetchFromDatabaseSomehow();
            if ($user->belongsToRole($role) === false) {
                $app = \Slim\Slim::getInstance();
                $app->flash('error', 'Login required');
                $app->redirect('/login');
            }
        }
    };
    $app = new \Slim\Slim();
    $app->get('/foo', $authenticateForRole('admin'), function(){
                    // Display admin control panel
                });

**什么参数会被传递给每个路由中间件函数？**

每个中间件函数在被调用时都可以传递一个参数，这个例子里是 \Slim\Route 对象。

    <?php
    $aBitOfInfo = function(\Slim\Route $route){
        echo "Current route is " . $route->getName();
    };
    $app->get('/foo', $aBitOfInfo, function(){
                    echo "foo";
                });

**路由辅助函数**

Slim 提供了一些辅助函数（通过 Slim 实例调用）来帮助你控制应用流程。

你应该意识到下面这些应用实例的辅助函数 halt()，pass()，redirect()，stop() 都是通过异常（Exceptions）来执行的。每个都会抛出 \Slim\Exception\Stop 或者 \Slim\Exception\Pass 异常。在这些情况下，抛出异常是让你的代码停止运行的简单方法，框架会接受并发送必要的响应信息到客户端。如果没有正确认识到这点，可能会得到奇怪结果。看一下下面的代码：

    <?php
    $app->get('/', function() use($app, $obj){
                    try {
                        $obj->thisMightThrowException();
                        $app->redirect('/success');
                    } catch(\Exception $e) {
                        $app->flass('error', $e->geMessage());
                        $app->redirect('/error');
                    }
                });

如果 $obj->thisMightThrowException() 抛出了一个异常，那么代码会按预想的情况运行。但是如果它没有抛出异常，$app->redirect('/success')在调用时也会抛出一个 \Slim\Exception\Stop 异常并被 catch 模块捕获，然后框架会将浏览器跳转到“/error”页面。你应该在应用需要抛出异常的地方使用不同类型的异常，那么 catch 模块就会捕捉指定异常而不是捕获所有的异常。在一些特殊情况下，thisMightThrowException 可能是你无法控制的外部组件，在这种情况下要抛出所有类型的异常是不可能的。那么我们可以细微的调整下代码，把 $app->redirect('/success') 移动到 try/catch 模块后面来解决这个问题。现在代码会按照预想的流程执行，除非捕获到 error redirect 抛出的异常。

    <?php
    $app->get('/', function() use($app, $obj){
                    try {
                        $obj->thisMightThrowException();
                    } catch(Exception $e) {
                        $app->flash('error', $e->getMessage());
                        $app->redirect('/error');
                    }
                    $app->redirect('/success');
                });

**Halt**

Slim 应用的 halt() 函数会立即返回给定的 HTTP 响应状态码（status code）和正文（body）。该函数接受两个参数：HTTP 状态码和可选消息。Slim 会立即停止当前应用并把HTTP 响应以及指定的状态码和可选消息（作为响应正文）发送到客户端。它会覆盖已存在的 \Slim\Http\Response 对象。

    <?php
    $app = new \Slim\Slim();

    // Send a default 500 error response
    $app->halt(500);

    // Or if you encouter a balrog
    $app->halt(403, 'You shall not pass!');

如果你需要用错误信息列表来渲染一个模板，你应该使用 Slim 应用的 render() 函数来代替。

    <?php
    $app = new \Slim\Slim();
    $app->get('/foo', function() use($app){
                    $errorData = array('error' => 'Permission Denied');
                    $app->render('errorTemplate.php', $errorData, 403);
                });
    $app->run();

halt() 函数可以发送任何类型的 HTTP 响应到客户端：infomational，success，redirect，not found，client error，或者 server error。

**Pass**

一个路由可以使用 pass() 函数告诉 Slim 应用继续执行下一个匹配的路由。当这个函数被调用，Slim 应用会立即停止执行当前匹配路由并直接调用下一个匹配路由。如果没有后续路由被匹配，就会像客户端发送一个 **404 Not Found** 响应。下面是一个例子，假如向“/hello/Frank”发送一个 HTTP GET请求：

    <?php
    $app = new \Slim\Slim();
    $app->get('/hello/Frank', function() use($app){
                    echo "You wont see this ...";
                    $app->pass();
                });
    $app->get('/hello/:name', function($name) use($app){
                    echo "But you will see this!";
                });
    $app->run();

**Redirect**

使用 Slim 应用的 redirect() 函数可以非常容易的让客户端跳转到另一个 URL。这个函数接受两个参数：第一个参数是客户端即将跳转的 URL；第二个可选参数是 HTTP 状态码。默认情况下，redirect() 函数会发送一个302临时跳转响应。

    <?php
    $app = new \Slim\Slim();
    $app->get('/foo', function() use($app){
                    $app->redirect('/bar');
                });
    $app->run();

但是如果你想要使用永久跳转，你必须指定把目标 URL 作为第一个参数然后把 HTTP 状态码作为第二个参数。

    <?php
    $app = new \Slim\Slim();
    $app->get('/old', function() use($app){
                    $app->redirect('/new', 301);
                });
    $app->run();

这个函数会自动设置 Location:header。HTTP 跳转响应会被立即发送到客户端。

**Stop**

Slim 应用的 stop() 函数会停止 Slim 应用并发送当前 HTTP 响应到客户端。不会考虑到其他情况。

    <?php
    $app = new \Slim\Slim();
    $app->get('/foo', function() use($app){
                    echo "You will see this...";
                    $app->stop();
                    echo "But not this";
                });
    $app->run();

**URL For**

Slim 应用的 urlFor() 函数可以让你为一个已命名路由动态的创建 URLs，当路由字段改变时，你的 URLs 也会在不破坏程序的情况下自动更新。下面的例子演示如何为已命名路由生成 URLs：

    <?php
    $app = new \Slim\Slim();
    // Create a named route
    $app->get('/hello/:name', function($name) use($app){
                    echo "Hello $name";
                })->name('hello');
    // Generate a URL for the named route
    $url = $app->urlFor('hello', array('name' => 'Josh'));

在这个例子中，$url 是“/hello/Josh”.当你使用 urlFor() 函数时，你必须首先为路由命名，然后再调用 urlFor() 函数。它的第一个参数是路由名称，第二个参数是用来替换路由 URL 参数实际值的关联数组；数组的 key 值必须和路由 URI 字段参数相匹配，数组的值才会被替换使用。

**路由 URL 重写**

我强烈推荐你使用支持 URL 重写的web服务器；这样你可以在 Slim 应用中使用简洁而友好的 URLs。要使用 URL 重写功能，你需要使用服务器提供的相关工具来转发所有的 HTTP 请求到 你实例化和运行 Slim 应用的 PHP 文件上。下面是 使用 mod_php 的Apache 和 nginx 最小配置示例。这些设置并不能满足生产环境需要，但是足够让你的应用尽快运行起来。你可以继续阅读[http://www.phptherightway.com](http://www.phptherightway.com)来获取更多服务器部署相关信息。

**Apache 和 mod_rewrite**

这是一个目录结构示例：

    /path/www.mysite.com/
        public_html <-- Document root!
            .htaccess
            index.php <-- I instantiate Slim here
        lib/
            slim/ <-- I store Slim lib files here!

目录中的 .htaccess 文件包含一下内容：

    RewriteEngine On
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^ index.php [QSA,L]

同时你也需要一个目录指令让 .htaccess文件 和 RewriteEngin 可以被使用。这个设置虽然可以在全局的 httpd.conf 文件中完成，但是通过将指令封装到虚拟主机设置模块的方式把指令限制在虚拟主机权限类是最好的办法。下面是设置的大致形式：

    <VirtualHost *:80>
        ServerAdmin me@mysite.com
        DocumentRoot "/path/www.mysite.com/public_html"
        ServerName mysite.com
        ServerAlias www.mysite.com

        #ErrorLog "logs/mysite.com-error.log"
        #CustomLog "logs/mysite.com-access.log" combined

        <Directory "/path/www.mysite.com/public_html">
            AllowOverride All
            Order allow,deny
            Allow from all
        </Directory>
    </VirtualHost>

现在 Apache 会将所有非文件请求转发到我 实例化和运行 Slim 应用的 index.php 脚本上。开启 URL 重写之后，以下面 index.php 中定义的 Slim 应用为例。你可以在应用路由中使用“/foo”来代替“/index.php/foo”。

    <?php
    $app = new \Slim\Slim();
    $app->get('/foo', function(){
                    echo "Foo";
                });
    $app->run();

**nginx**

我们使用和上面同样的目录结构，只是把配置文件换为 nginx 的配置文件 nginx.conf。

    /path/www.mysite.com/
    public_html/ <-- Document root!
        index.php <-- I instantiate Slim here!
    lib/
        Slim/ <-- I store Slim lib files here!

下面是 nginx.conf 文件中我们使用 try_files 指令的位置片段，它让服务器处理实际存在的静态文件（图片，css，js 等）请求而把其他的都转发到 index.php 文件上。

    server {
        listen       80;
        server_name  www.mysite.com mysite.com;
        root         /path/www.mysite.com/public_html;

        try_files $uri /index.php;

        # this will only pass index.php to the fastcgi process which is generally safer but
        # assumes the whole site is run via Slim.
        location /index.php {
            fastcgi_connect_timeout 3s;     # default of 60s is just too long
            fastcgi_read_timeout 10s;       # default of 60s is just too long
            include fastcgi_params;
            fastcgi_pass 127.0.0.1:9000;    # assumes you are running php-fpm locally on port 9000
        }
    }

大多数情况下安装后会有一个默认的 fastcgi_params 文件，因此你只需要把上面的配置包含进去即可。不过有些配置文件中没有包含 SCRIPT_FILENAME 参数。你必须确保包含了该参数，否则可能被终止运行并收到来自 fastcgi 程序的未指定输入文件错误。这个问题可以直接在 location block 中添加或者简单的把参数添加到 fastcgi_params 文件中。无论哪种方式，它应该看起来像这样：

    fastcgi_param SCRIPT_FILENAME $docment_root$fastcgi_script_name;

**不使用 URL 重写**

Slim 也可以在不使用 URL 重写的情况下使用。在这种脚本中，你必须在初始化和运行Slim 应用的资源 URI中包含 PHP 文件名。举个例子，假如下面的 Slim 应用定义在你虚拟主机根目录的 index.php 文件中：

    <?php
    $app = new \Slim\Slim();
    $app->get('/foo', function(){
                    echo "Foo!";
                });
    $app->run();

你可以使用“index.php/foo”来访问定义的路由。如果同样的应用被定义在 blog/ 子目录的 index.php 文件中，你可以通过 “/blog/index.php/foo” 来访问定义的路由。

-- EOF --

