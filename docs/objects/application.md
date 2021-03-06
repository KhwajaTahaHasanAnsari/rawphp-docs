---
title: Application
---

The RawPHP application has different component parts. The application is started from the `public/index.php` file. Then ther `routes/routes.php` file handles the rest of the requests


// Route callbacks
In `routes/routes.php`
```
$app->get('/', function ($request, $response, $args) {
    return $response->withStatus(200)->write('Hello World!');
});
```


## Application Configuration

The Application accepts just one argument. This can be either a [Container](/docs/concepts/di.html) instance or
an array to configure the default container that is created automatically.

There are also a number of settings that are used by RawPHP. These are stored in the `settings`
configuration key. You can also add your application-specific settings.

For example, we can set the RawPHP setting `displayErrorDetails` to true and also configure
Monolog like this:

In `config/DatabaseConfig.php`

```
$config = [
    'settings' => [
        'displayErrorDetails' => true,

        'logger' => [
            'name' => 'rawphp-app',
            'level' => Monolog\Logger::DEBUG,
            'path' => __DIR__ . '/../logs/app.log',
        ],
    ],
];
```


### Retrieving Settings

As the settings are stored in the DI container so you can access them via the `settings` key in container factories. For example:

```
$settings = $container->get('settings')['logger'];
```

You can also access them in route callables via `$this`:

```
$app->get('/', function ($request, $response, $args) {
    $loggerSettings = $this->get('settings')['logger'];
    // ...
});
```

### Updating Settings

If you need to add or update settings stored in the DI container *after* the container is initialized,
you can use the `replace` method on the settings container. For example:

```
$settings = $container->get('settings');
$settings->replace([
        'displayErrorDetails' => true,
        'determineRouteBeforeAppMiddleware' => true,
        'debug' => true
    ]);
]);
```

## RawPHP Default Settings

RawPHP has the following default settings that you can override:

<dl>
<dt><code>httpVersion</code></dt>
    <dd>The protocol version used by the <a href="/docs/objects/response.html">Response</a>
        object.
        <br>(Default: <code>'1.1'</code>)</dd>
<dt><code>responseChunkSize</code></dt>
    <dd>Size of each chunk read from the Response body when sending to the
        browser.
        <br>(Default: <code>4096</code>)</dd>
<dt><code>outputBuffering</code></dt>
    <dd>If <code>false</code>, then no output buffering is enabled. If <code>'append'</code>
        or <code>'prepend'</code>, then any <code>echo</code> or <code>print</code>
        statements are captured and are either appended or prepended to the Response
        returned from the route callable.
        <br>(Default: <code>'append'</code>)</dd>
<dt><code>determineRouteBeforeAppMiddleware</code></dt>
    <dd>When true, the route is calculated before any middleware is executed. This
    means that you can inspect route parameters in middleware if you need to.
    <br>(Default: <code>false</code>)</dd>
<dt><code>displayErrorDetails</code></dt>
    <dd>When true, additional information about exceptions are displayed by the
    <a href="/docs/handlers/error.html">default error handler</a>.
    <br>(Default: <code>false</code>)</dd>
<dt><code>addContentLengthHeader</code></dt>
    <dd>When true, RawPHP will add a <code>Content-Length</code> header to the response.
    If you are using a runtime analytics tool, such as New Relic, then this should be disabled.
    <br>(Default: <code>true</code>)</dd>
<dt><code>routerCacheFile</code></dt>
    <dd>Filename for caching the FastRoute routes. Must be set to to a valid filename within
    a writeable directory. If the file does not exist, then it is created with the correct cache
    information on first run.<br>
    Set to <code>false</code> to disable the FastRoute cache system.
    <br>(Default: <code>false</code>)</dd>
</dl>
