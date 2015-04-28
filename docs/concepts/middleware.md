# 什么是中间件？

Slim 中间件是一个需要接受三个参数的 _回调对象_（ _callable_ ），可以是 `Closure` 或者实现了 `__invoke` 方法的对象，其参数分别为：

1. `\Psr\Http\Message\RequestInterface` - 请求对象
2. `\Psr\Http\Message\ResponseInterface` - 响应对象
3. `callable` - 下一层中间件的回调对象

中间件可以根据需求对这些对象做出相应的修改或者替换，在调用之后 **必须** 返回一个实现了 `\Psr\Http\Message\ResponseInterface` 接口的对象。中间件在执行过程中 **可以** 将新的请求和响应对象作为参数，调用下一层中间件。

# 中间件是如何工作的？

不同的框架使用中间件的方式不同。在 Slim 框架中，中间件层以同心圆的方式包裹着核心应用。由于新增的中间件层总会包裹所有已经存在的中间件层，所以同心圆结构会不断的向外扩展。

当 Slim 应用运行时，请求对象和响应对象首先会进入最外层的中间件，然后不断向内深入，直到最后穿过整个中间件结构到达核心应用。在 Slim 应用分派了对应的路由之后，返回的响应对象又会以由内向外的方式穿过整个中间件结构。在离开最外层中间件之后，最终的响应对象会被序列化为一个原始的 HTTP 响应消息，然后返回给客户端。下面的图表很清晰的说明了中间件的执行流程：

![Middleware flow](/images/middleware.png 'Middleware')

# 如何编写中间件？

Middleware is a callable that accepts three arguments: a Request object, a Response object, and the next middleware. Each middleware **MUST** return an instance of `\Psr\Http\Message\ResponseInterface`. This example middleware is a Closure.

    <?php
    function ($request, $response, $next) {
        $response->write('BEFORE');
        $response = $next($request, $response);
        $response->write('AFTER');

        return $response;
    };

This example middleware is an invokable class that implements the magic `__invoke()` method.

    <?php
    class ExampleMiddleware
    {
        public function __invoke($request, $response, $next)
        {
            $response->write('BEFORE');
            $response = $next($request, $response);
            $response->write('AFTER');

            return $response;
        }
    }

# 如何添加中间件？

You may add middleware to a Slim application or to an individual Slim application route. Both scenarios accept the same middleware and implement the same middleware interface.

## 应用中间件

Application middleware is invoked for every HTTP request. Add application middleware with the Slim application instance's `add()` method. This example adds the Closure middleware example above:

    <?php
    $app = new \Slim\App();

    $app->add(function ($request, $response, $next) {
        $response->write('BEFORE');
        $response = $next($request, $response);
        $response->write('AFTER');

        return $response;
    });

    $app->get('/', function ($req, $res, $args) {
        echo ' Hello ';
    });

    $app->run();

This would output this HTTP response body:

    BEFORE Hello AFTER

## 路由中间件

Route middleware is invoked _only if_ its route matches the current HTTP request method and URI. Route middleware is specified immediately after you invoke any of the Slim application's routing methods (e.g., `get()` or `post()`). Each routing method returns an instance of `\Slim\Route`, and this class provides the same middleware interface as the Slim application instance. Add middleware to a Route with the Route instance's `add()` method. This example adds the Closure middleware example above:

    <?php
    $app = new \Slim\App();

    $mw = function ($request, $response, $next) {
        $response->write('BEFORE');
        $response = $next($request, $response);
        $response->write('AFTER');

        return $response;
    });

    $app->get('/', function ($req, $res, $args) {
        echo ' Hello ';
    })->add($mw);

    $app->run();

This would output this HTTP response body:

    BEFORE Hello AFTER
