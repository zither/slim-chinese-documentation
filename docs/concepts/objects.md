# 环境对象

环境对象是对当前请求 PHP 全局环境的封装，不仅包括了 HTTP 请求的方法、URI、头部和主体，还包括了超全局数组 `$_SERVER` 中的服务器变量。环境对象实现了 Slim 应用和 PHP 全局环境之间的有效解耦。

[Learn more](/objects/environment)

# 请求对象

This object encapsulates the current HTTP request using the information provided by the Environment object. The Request object provides the HTTP request method, headers, parameters, and body.

[Learn more](/objects/request)

# 响应对象

This object encapsulates an HTTP response to be returned to the HTTP client. It manages the HTTP response status, headers, and body.

[Learn more](/objects/response)

# 路由器对象

This object manages application routes. A _route_ has three parts: a method, a URI path, and a callback. The Router object's routes are iterated when you invoke the Slim application's `run()` method. The Router object finds and invokes the first route that matches the current HTTP request method and URI. The Router can be accessed directly, but it is typically used via proxy methods on the application instance.

[Learn more](/objects/router)
