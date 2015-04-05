# What are value objects?

Each Slim application's Request and Response objects are [_immutable value objects_](http://en.wikipedia.org/wiki/Value_object). They can be "changed" only by requesting a cloned version that has updated property values.

Value objects are a source of constant opinion. Some developers love them, and other developers hate them. I was skeptical when Matthew Weier O'Phinney first suggested I use them. After implementing value objects, however, I am 100% on board. Immutable value objects provide stability and predictability that are otherwise impossible with object references.

Each Slim application starts with an initial Request and Response object pair. These objects are passed into each application middleware layer, and each middleware layer is free to use these objects as it deems fit. If Slim used object references, each middleware layer could never be certain of its own Request and Response objects if further interior middleware can update the same object references. Instead, Slim uses immutable value objects. This means that each middleware's Request and Response objects always remain the same, and they cannot be changed by other middleware. If a middleware wants to modify the Request or Response object, it must create a _cloned_ version with updated properties.

Value objects have a small inherent overhead becaue they must be cloned when their properties are updated. This overhead is minimal and does not affect application performance in any meaningful way.

# How to change value objects

You cannot modify a value object. You can, however, request a copy of a value object that contains your desired changes. Slim's Request and Response objects implement the PSR-7 message interfaces. These interfaces provide methods that have the `with` prefix, and you can invoke these methods to _clone_ value objects and apply updated properties. For example, the Response object has a `withHeader($name, $value)` method that returns a cloned value object with a new HTTP header.

```php
$newResponse = $oldResponse->withHeader(
    'Content-Type',
    'application/json'
);
```
