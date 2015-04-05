# The Request Object

The Request object encapsulates the HTTP request data from the current Environment object. Each Slim application has one Request object. You use the Request object to inspect the current HTTP request's method, headers, and body. The Request object is registered as a Pimple service on the application instance. You can fetch the Request object like this:

    <?php
    $request = $app['request'];

## Request Method

You can inspect the current HTTP request method with the Request object's `getMethod()` method. This returns a string value equal to `GET`, `POST`, `PUT`, `DELETE`, `HEAD`, `OPTIONS`, or `PATCH`.

    <?php
    $method = $app['request']->getMethod();

### GET Method

You can detect HTTP `GET` requests with the Request object's `isGet()` method.

    <?php
    if ($app['request']->isGet() === true) {
        // Do something
    }

### POST Method

You can detect HTTP `POST` requests with the Request object's `isPost()` method.

    <?php
    if ($app['request']->isPost() === true) {
        // Do something
    }

### PUT Method

You can detect HTTP `PUT` requests with the Request object's `isPut()` method.

    <?php
    if ($app['request']->isPut() === true) {
        // Do something
    }

### DELETE Method

You can detect HTTP `DELETE` requests with the Request object's `isDelete()` method.

    <?php
    if ($app['request']->isDelete() === true) {
        // Do something
    }

### HEAD Method

You can detect HTTP `HEAD` requests with the Request object's `isHead()` method.

    <?php
    if ($app['request']->isHead() === true) {
        // Do something
    }

### OPTIONS Method

You can detect HTTP `OPTIONS` requests with the Request object's `isOptions()` method.

    <?php
    if ($app['request']->isOptions() === true) {
        // Do something
    }

### PATCH Method

You can detect HTTP `PATCH` requests with the Request object's `isPatch()` method.

    <?php
    if ($app['request']->isPatch() === true) {
        // Do something
    }

### Method Override

There are two ways to override the HTTP request method. You can override the HTTP request method using the `_METHOD` parameter, or you can send a custom `X-HTTP-Method-Override` header. This is particularly useful if, for example, you need to mimic a `PUT` request using a traditional web browser that only supports `POST` requests. You can always fetch the _original_ (non-overridden) HTTP method with the Request object's `getOriginalMethod()` method.

#### With a body parameter

Include a `_METHOD` parameter in a `POST` request body. You must use the `application/x-www-form-urlencoded` content type.

#### With a header

You may also include the `X-HTTP-Method-Override` header in the HTTP request.

## Request URL

The Request object provides several methods to inspect the HTTP request URL.

### Scheme

You can fetch the HTTP request scheme (e.g., HTTP or HTTPS) with the Request object's `getScheme()` method.

    <?php
    $scheme = $app['request']->getScheme();

### Host

You can fetch the HTTP request's host (e.g., example.com) with the Request object's `getHost()` method.

    <?php
    $host = $app['request']->getHost();

### Port

You can fetch the HTTP request's port number (e.g., 443) with the Request object's `getPort()` method.

    <?php
    $port = $app['request']->getPort();

### URL (physical path)

You can fetch the HTTP request's physical directory path (relative to the document root) with the Request object's `getScriptName()` method. This will be an empty string unless the Slim application is installed in a physical subdirectory beneath your document root.

    <?php
    $realPath = $app['request']->getScriptName();

### URL (virtual path)

You can fetch the HTTP request's virtual directory path with the Request object's `getPathInfo()` method. This will return a string value that likely matches a Slim application route path.

    <?php
    $virtualPath = $app['request']->getPathInfo();

### URL (physical + virtual paths)

You can fetch the complete physical and virtual path with the Request object's `getPath()` method. This is useful if you must create an application link URL to an application resource to be rendered in an application template.

    <?php
    $fullPath = $app['request']->getPath();

### Query String

You can fetch the HTTP request's raw query string with the Request object's `getQueryString()` method. You probably want to use the Request object's `get()` method instead.

    <?php
    $queryString = $app['request']->getQueryString();

## Request Headers

There are several ways to inspect the Request object's headers.

### All Headers

You can fetch an associative array of all headers with the Request object's `getHeaders()` method.

    <?php
    $headers = $app['request']->getHeaders();

### Detect Header

You can test for the presence of a header with the Request object's `hasHeader($key)` method.

    <?php
    if ($app['request']->hasHeader('Accept') === true) {
        // Do something
    }

### Fetch Single Header

You can fetch a single header value with the Request object's `getHeader($key)` method.

    <?php
    $headerValue = $app['request']->getHeader('Accept');

## Request Cookies

There are several ways to inspect the Request object's cookies.

### All Cookies

You can fetch an associative array of all cookies with the Request object's `getCookies()` method.

    <?php
    $cookies = $app['request']->getCookies();

### Detect Cookie

You can test for the presence of a cookie with the Request object's `hasCookie()` method.

    <?php
    if ($app['request']->hasCookie('logged_in') === true) {
        // Do something
    }

### Fetch Single Cookie

You can fetch a single cookie value with the Request object's `getCookie($key)` method.

    <?php
    $cookieValue = $app['request']->getCookie('user_name');

## Request Body

You can fetch the raw HTTP request body with the Request object's `getBody()` method. Bear in mind this returns a string value containing the HTTP request body _exactly_ as sent by the HTTP client.

    <?php
    $body = $app['request']->getBody();

This is useful if you need to fetch and decode a JSON request, for example. However, you're probably more familiar with traditional HTML form submissions. Slim provides the following helper methods to inspect HTTP request parameters submitted via HTML forms.

### GET data

You can inspect GET request parameters (e.g., query string data) with the Request object's `get()` method. If you invoke the method _without_ an argument, it returns an associative array for all GET request data. If you invoke the method _with_ an argument, it returns a single string value for the specified parameter name.

    <?php
    // All GET data
    $data = $app['request']->get();

    // Single GET parameter
    $value = $app['request']->get('id');

### POST & DELETE data

You can inspect POST request parameters with the Request object's `post()` method. If you invoke the method _without_ an argument, it returns an associative array for all POST request data. If you invoke the method _with_ an argument, it returns a single string value for the specified parameter name.

    <?php
    // All POST data
    $data = $app['request']->post();

    // Single POST parameter
    $value = $app['request']->post('id');

The Request object also provides `put()` and `delete()` methods. These are aliases of the `post()` method.

<div class="wy-alert wy-alert-info">
The Request object's <code>post()</code>, <code>put()</code>, and <code>delete()</code> methods work only if the HTTP request content type is <code>application/x-www-form-urlencoded</code>.
</div>

## Request Helpers

The Request object provides additional methods to inspect the HTTP request metadata (e.g., content type, charset, length, IP, and referrer).

### Detect AJAX / XHR requests

You can detect AJAX/XHR requests with the Request object's `isAjax()` and `isXhr()` methods. Both methods do the same thing, so choose only one. These methods detect the presence of the `X-Requested-With` HTTP request header and ensure its value is `XMLHttpRequest`. These methods also return `true` if the `isajax` parameter is provided in the HTTP request query string or body.

    <?php
    if ($app['request']->isAjax() === true) {
        // Do something
    }

### Content Type

You can fetch the HTTP request content type with the Request object's `getContentType()` method. This returns the `Content-Type` header's full value as provided by the HTTP client.

    <?php
    $contentType = $app['request']->getContentType();

### Media Type

You may not want the complete `Content-Type` header. What if, instead, you only want the media type? You can fetch the HTTP request media type with the Request object's `getMediaType()` method.

    <?php
    $mediaType = $app['request']->getMediaType();

You can fetch the appended media type parameters as an associative array with the Request object's `getMediaTypeParams()` method.

    <?php
    $mediaParams = $app['request']->getMediaTypeParams();

### Character Set

One of the most common media type parameters is the HTTP request character set. The Request object provides a dedicated method to retrieve this media type parameter.

    <?php
    $charset = $app['request']->getContentCharset();

### Content Length

You can fetch the HTTP request content length with the Request object's `getContentLength()` method.

    <?php
    $length = $app['request']->getContentLength();

### IP Address

You can fetch the HTTP request's source IP address with the Request object's `getClientIp()` method. This method respects values provided by the `X-Forwarded-For` and `X-Client-IP` headers if present.

    <?php
    $ip = $app['request']->getClientIp();

### Referrer

You can fetch the HTTP request referrer (if present) with the Request object's `getReferrer()` and `getReferer()` methods.

    <?php
    $ref = $app['request']->getReferer();

### User Agent

You can fetch the HTTP user agent string with the Request object's `getUserAgent()` method.

    <?php
    $agent = $app['request']->getUserAgent();
