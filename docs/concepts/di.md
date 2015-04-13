# 容器

每个 Slim 应用都有很多 _依赖_，比如在前面对象概念中所提到的环境、请求、响应以及路由器等对象。

Slim 为每个依赖都提供了可以直接使用的自身实现。但由于 Slim 应用本身是 [Pimple](http://pimple.sensiolabs.org/) 依赖注入容器的实例，你也可以注入自定义的依赖实现。Slim 应用的依赖都是通过具有延迟初始化特性的 Pimple 服务来实现的，只有在被调用时才会初始化并返回相应的对象。

# 服务（Services）

Slim 应用默认的依赖服务都是可以被覆写的，你只需要在调用 `run()` 方法之前注入对应的自定义 Pimple 服务即可。这些默认服务包括：

settings
:   设置服务必须返回一个数组或者实现了 `\ArrayAccess` 接口的 _共享_ 实例。

environment
:   环境服务必须返回实现了 `\Slim\Interfaces\Http\EnvironmentInterface` 接口的 _共享_ 实例（译者注：官方文档此处为新实例，尚未修正）。

request
:   请求服务必须返回实现了 `\Psr\Http\Message\RequestInterface` 接口的新实例。

response
:   响应服务必须返回实现了 `\Psr\Http\Message\ResponseInterface` 接口的新实例.

router
:   路由器服务必须返回实现了 `\Slim\Interfaces\RouterInterface` 接口的 _共享_ 实例。

errorHandler
:   This service must return a callable to be invoked if there is an application error. The callable **MUST** return an instance of `\Psr\Http\Message\ResponseInterface`. The callable **SHOULD** accept three arguments:

1. `\Psr\Http\Message\RequestInterface`
2. `\Psr\Http\Message\ResponseInterface`
3. `\Exception`

notFoundHandler
:   This service must return a callable to be invoked if the current HTTP request URI does not match an application route. The callable **MUST** return an instance of `\Psr\Http\Message\ResponseInterface`. The callable **SHOULD** accept two arguments:

1. `\Psr\Http\Message\RequestInterface`
2. `\Psr\Http\Message\ResponseInterface`

notAllowedHandler
:   This service must return a callable to be invoked if an application route matches the current HTTP request path but not its method. The callable **MUST** return an instance of `\Psr\Http\Message\ResponseInterface`. The callable **SHOULD** accept three arguments:

1. `\Psr\Http\Message\RequestInterface`
2. `\Psr\Http\Message\ResponseInterface`
3. Array of allowed HTTP methods

You most likely **do not** need to replace these dependencies. However, you can if necessary. Slim's default service implementations live in the Slim application's `__construct()` function.

# 服务提供者（Service providers）

Each Slim application extends the `\Pimple\Container` class. This means each Slim application can accept and register [Pimple Service Providers](http://pimple.sensiolabs.org/#extending-a-container). You register Pimple Service Providers with the Slim application's `register()` method.

A simple example is the [slim/twig-view](features/templates/#the-slimtwig-view-component) component. After you install the `slim/twig-view` component with Composer, you can register the component with a Slim application like this:

```php
// Create Slim app
$app = new \Slim\App();

// Register Twig View service
$app->register(new \Slim\Views\Twig('path/to/templates', [
    'cache' => 'path/to/cache'
]));

// Use Twig View service in route
$app->get('/hello/{name}', function ($request, $response, $args) {
    $this['view']->render('profile.html', [
        'name' => $args['name']
    ]);
})->setName('profile');

// Run app
$app->run();
```

The `slim/twig-view` service provider adds a new `view` service to the Slim application container that you can use to render Twig templates.
