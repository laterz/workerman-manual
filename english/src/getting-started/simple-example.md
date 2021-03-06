# A simple tutorial

## Example 1 : A HTTP Service
**Create http_test.php**
```php
<?php
use Workerman\Worker;
require_once './Workerman/Autoloader.php';

// Create a Worker and listens 2345 port，use HTTP Protocol
$http_worker = new Worker("http://0.0.0.0:2345");

// 4 processes
$http_worker->count = 4;

// Emitted when data is received
$http_worker->onMessage = function($connection, $data)
{
    var_dump($_GET, $_POST, $_COOKIE, $_SESSION, $_SERVER, $_FILE);
    // Send hello world to client
    $connection->send('hello world');
};

// Run all workers
Worker::runAll();
```

**Run with**
```shell
php http_test.php start

```

**test**


Visit url http://127.0.0.1:2345


## Example 2 : A Websocket Service
**create ws_test.php**
```php
<?php
use Workerman\Worker;
require_once './Workerman/Autoloader.php';

// Create A Worker and Listens 2346 port, use Websocket protocol
$ws_worker = new Worker("websocket://0.0.0.0:2346");

// 4 processes
$ws_worker->count = 4;

// Emitted when new connection come
$ws_worker->onConnect = function($connection)
{
    // Emitted when websocket handshake done
    $connection->onWebSocketConnect = function($connection)
    {
        echo "New connection\n";
    };
};

// Emitted when data is received
$ws_worker->onMessage = function($connection, $data)
{
    // Send hello $data
    $connection->send('hello ' . $data);
};

// Emitted when connection closed
$ws_worker->onClose = function($connection)
{
    echo "Connection closed";
};

// Run worker
Worker::runAll();
```

**Run with**
```shell
php ws_test.php start

```

**Test**

Javascript

```javascript
ws = new WebSocket("ws://127.0.0.1:2346");
ws.onopen = function() {
    alert("connection success");
    ws.send('tom');
};
ws.onmessage = function(e) {
    alert("recv message from server：" + e.data);
};
```

## Example 3 ： A TCP Server
**create tcp_test.php**

```php
use Workerman\Worker;
require_once './Workerman/Autoloader.php';

// Creae A Worker and listen 2347 port，not specified protocol
$tcp_worker = new Worker("tcp://0.0.0.0:2347");

// 4 processes
$tcp_worker->count = 4;

// Emitted when new connection come
$tcp_worker->onConnect = function($connection)
{
    echo "New connection\n";
};

// Emitted when data is received
$tcp_worker->onMessage = function($connection, $data)
{
    // Send hello $data
    $connection->send('hello ' . $data);
};

// Emitted when connection closed
$tcp_worker->onClose = function($connection)
{
    echo "Connection closed\n";
};

// Run worker
Worker::runAll();
```

**Run with**

```shell
php tcp_test.php start

```

**Test**
```shell
telnet 127.0.0.1 2347
Trying 127.0.0.1...
Connected to 127.0.0.1.
Escape character is '^]'.
tom
hello tom
```
