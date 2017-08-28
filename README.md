# CodeSchool-RealTimeWeb-with-NodeJS

## 1.1 Intro to Node.js

### What is node.js - Created by Ryan Dahl
Nodejs allows you to build scalable network applications using JavaScript on the server-side.

It's running the V8 JavaScript Runtime. This is the same runtime that is used in the Chrome browser.  
Node.js provides a wrapper around this engine for additional functionality for building network applications.

It's fast because its mostly C code.

### What is node.js not
- A web framework
- for beginners
- multi-threaded

### Node.JS Hello Dog

1. Require the http module.
2. Call the createServer-function, which takes in a callback-function as single parameter.
3. Write a header for the response.
4. Write out the response body.
5. End the response.
6. Make server listen to port 88.
7. Log to make sure the server is listening.

```js
var http = require('http'); // 1.

http.createServer(function(request, response){ // 2.
  response.writeHead(200); // 3.
  response.write("Hello, this is dog."); // 4.
  response.end(); // 5.
}).listen(8080); // 6.
console.log('Listening on port 8080...'); // 7.
```

To run the server on the command-line:
```
$ node hello.js
```

### The Event Loop #1
The first time it goes through this code and executes it, Node is going to register events. In this case it registers the request event. Once it gets done executing this script, Node will do something called an Event Loop:
It's checking for events continuously:  
"Did a request come in? Did a request come in? Did a request come in?"
As soon as the request comes in in triggers the event and runs the callback that we wrote and print the message "Hello, this is dog".

### Why JavaScript?
It makes it really easy to program in an eventive way; to do "evented" programming using that event loop and write code that is potentionnaly non-blocking.

### The Event Loop #2
Triggering = Emitting
to an Event Queue. If multiple events come in at the same time, it processes one event at a time.

### With Long Running Process
Let's simulate a Long Running Process.
1. Create the server
2. Write the statuscode for the response.
3. Write body.
4. Pause for about 5 sec.
5. Dog is done.
6. End the response.

```js
var http = require('http');
http.createServer(function(request, response){
  response.writeHead(200);
  response.write("Dog is running.");
  setTimeout(function(){ // Represent long running process
    response.write("Dog is done.");
    response.end();
  }, 5000);
}).listen(8080);
```

## 2.1 events

### Events in the DOM - Events in Node

Many objects in Node emit events from the EventEmitter constructor.

### Custom event emitters

1. Require class.
2. create new EventEmitter called logger, to sometimes log error-, warn- and info-events.

```js
var EventEmitter = require('events').EventEmitter;

logger.on('error', function(message){
  console.log('ERR: ' + message);
});

logger.emit('error', 'Spilled Milk'); // trigger the error-event
```

### HTTP Echo Server
```js
http.createServer(function(request,response){...});
```

The createServer function returns a new web server object:
```
http.createServer([requestListener])
```
The requestListener is a function which is automatically added to the 'request' event.

**Class: http.Server**  
*This is an EventEmitter with the following events:*

**Event: 'request'**  
  function (request, response) {}  
*Emitted each time there is a request.*

### Alternate Syntax
```js
http.createServer(function(request, response){...});
```
same as:
```js
var server = http.createServer();
server.on('request', function(request, response){...}); // This is houw we add event listeners
```

## 3.1 Streams

## 4.1 Modules
#### Create our own module
*in custom_hello.js*
```js
var hello = function() {
  console.log("hello!");
}
module.exports = hello; // export defines what require returns
```
*in app.js*
```js
var hello = require('./custom_hello'); // "./" look in same directory

hello(); // "hello!"
```
Another one:
*in custom_goodbye.js*
```js
exports.goodbye = function() {
  console.log("bye!");
}
```
*back in app.js*
```js
...
var gb = require('./custom_goodbye');

gb.goodbye(); // "bye!"
...
```

#### Making HTTP Requests
*in make_request.js*
```js
var http = require('http');

var make Request = function(message){
  var message = message;
  var options = {
    host: 'localhost',
    port: 8080,
    path: '/',
    method: 'POST'
  };

  var request = http.request(options, function(response){
    response.on('data', function(data){
      console.log(data); // logs response body
    });
  });
  request.write(message); // begins request
  request.end(); // finishes request
}

// makeRequest("Here's looking at you, kid."); // invoke
module.exports = makeRequest;
```
*in app.js*
```js
var makeRequest = require('./make_request');

makeRequest("Here's looking at you, kid");
makeRequest("Hello, this is dog");
```

#### Require Search
```
var make_request = require('./make_request'); // looks in same directory
var make_request = require('../make_request'); // looks in parent directory
var make_request = require('/Users/eric/nodes/make_request');

var make_request = require('make_request'); // searches for closest node_modules directory
```

#### Installing a npm module
*in /Home/my_app*
```
$ npm install request // installs into local node_modules directory (/Home/my_app/node_modules/request)
```

#### Local vs Global
Global npm modules can't be required inside an application.
