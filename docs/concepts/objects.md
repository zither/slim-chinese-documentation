# The Environment object

This object encapsulates the current PHP global environment, including the HTTP request method, URI, headers, and body. It also contains server variables found in the `$_SERVER` superglobal array. The Environment object effectively decouples a Slim application from the global PHP environment.

[Learn more](environment)

# The Request object

This object encapsulates the current HTTP request using the information provided by the Environment object. The Request object provides the HTTP request method, headers, parameters, and body.

[Learn more](request)

# The Response object

This object encapsulates an HTTP response to be returned to the HTTP client. It manages the HTTP response status, headers, and body.

[Learn more](response)

# The Router object

This object manages application routes. A _route_ has three parts: a method, a URI path, and a callback. The Router object's routes are iterated when you invoke the Slim application's `run()` method. The Router object finds and invokes the first route that matches the current HTTP request method and URI. The Router can be accessed directly, but it is typically used via proxy methods on the application instance.

[Learn more](router)
