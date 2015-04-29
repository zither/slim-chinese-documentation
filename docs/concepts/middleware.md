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

中间件是一个可以接收三个参数的回调对象（callable），其参数分别为：请求对象、响应对象以及下一层中间件。中间件 **必须** 返回实现了 `\Psr\Http\Message\ResponseInterface` 接口的实例。以下是一个通过闭包实现的中间件示例：

    <?php
    function ($request, $response, $next) {
        $response->write('BEFORE');
        $response = $next($request, $response);
        $response->write('AFTER');

        return $response;
    };

这是一个通过是实现了 `__invoke()` 魔术方法的可调用类实现的中间件示例：

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

我们既可以将一个中间件添加到应用中，也可以将其添加到单独的路由中。这是因为 Slim 应用和路由实现了同样的中间件接口，可以接受同样的中间件。

## 应用中间件

应用中间件可以通过 Slim 应用实例的 `add()` 方法添加，会被每一个 HTTP 请求调用。下面的代码演示了如何添加应用中间件：

    <?php
    $app = new \Slim\App();

    $app->add(function ($request, $response, $next) {
        $response->write('BEFORE');
        $response = $next($request, $response);
        $response->write('AFTER');

        return $response;
    });

    $app->get('/', function ($req, $res, $args) {
        return $res->write(' Hello ');
    });

    $app->run();

输出的 HTTP 响应主体为：

    BEFORE Hello AFTER

## 路由中间件

与应用中间件不同，路由中间件 _只有_ 在当前 HTTP 请求的方法和 URI 都与中间件所在路由相匹配时才会被调用。由于 Slim 应用提供了一系列添加路由的辅助方法（例如：`get()` 和 `post()`），它们在调用后都会返回 `\Slim\Route` 实例，因此我们只要在调用路由方法之后立即调用 `add()` 方法就可以将中间件添加到对应的路由中。下面的代码演示了如何采用链式调用的方式添加路由中间件：

    <?php
    $app = new \Slim\App();

    $mw = function ($request, $response, $next) {
        $response->write('BEFORE');
        $response = $next($request, $response);
        $response->write('AFTER');

        return $response;
    });

    $app->get('/', function ($req, $res, $args) {
        return $res->write(' Hello ');
    })->add($mw);

    $app->run();

输出的 HTTP 响应主体为：

    BEFORE Hello AFTER
