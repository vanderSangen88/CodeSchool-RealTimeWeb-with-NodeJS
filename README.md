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

## 1.2 Hello You - Challenge
*in app.js*
```js
var http = require('http');

http.createServer(function(request, response){
  response.writeHead(200); // 1/3 Tell the response which status it should have
  response.write("Hello, this is <your name here>"); // 2/3 Write a message to the response body
  response.end(); // 3/3 Tell the response to end so the client on the other side knows it has received all the data.
}).listen(8000);
```
## 1.3 Convert Blocking - Challenge
*in file_read.js*
```js
var fs = require('fs');

// var contents = fs.readFileSync('index.html');
fs.readFile('index.html', function(error, contents){ // 3/3 Remove the contents var declaration and move console.log() inside the callback.
  // 2/3 Add a callback method to the readFile call with the parameters error and contents.
  console.log(contents);
}); // 1/3 Start by changing the call from readFileSync to readFile
```

## 1.4 Running Your Code - Challenge
```
$ node file_read.js // Run the file
```

## 1.5 Read File in Server - Challenge
*in app.js*
```js
var http = require('http');
var fs = require('fs');

http.createServer(function(request, response){
  response.writeHead(200);
  fs.readFile('index.html', function(error, contents){ // 1/3 Add call to fs.readFile() that reads 'index.html' asynchronously. Pass in a callback that accepts an error and contents parameter.
    response.write(contents); // 2/3 Now that you have the contents, write it to the response.
    response.end(); // 3/3 End the response after the file contents have been written.
  });
}).listen(8080);
```
## 1.6 Issuing a request - Challenge
```
& curl http://localhost:8080 // Issue a request and see the server respond with the contents of index.html
```

## 1.7 Writing Response Headers - Challenge
*in app.js*
```js
var http = require('http');
var fs = require('fs');

http.createServer(function(request, response){
  response.writeHead(200, {
    "Content-Type" : "text/html" // Add a Content-Type of text/html to the response
  });

  fs.readFile('index.html', function(err, contents){
    response.write(contents);
    response.end();
  });
}).listen(8080);
```

## 1.8 Response End - Challenge
*in app.js*
```js
var http = require('http');

http.createServer(function(request, response){
  response.writeHead(200);
  // response.write("Hello, this is dog"); 2/2 Remove call to response.write()
  response.end("Hello, this is dog"); // 1/2 Pass the content of response.write to response.end
}).listen(8080);
```
---

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

## 2.2 Chat Emitter - Challenge
*in chat.js*
```js
var events = require('events');
var EventEmitter = events.EventEmitter;

var chat = new EventEmitter(); // 1/3 Create a new EventEmitter object and assign it to a variable called 'chat'.
chat.on('message', function(message){ // 2/3 Listen to the 'message' event and add a callback that accepts the message parameter.
  console.log(message); // 3/3 Log the message to the console.
});
```

## 2.3 Emitting Events - Challenge
*in app.js*
```js
var events = require('events');
var EventEmitter = events.EventEmitter;

var chat = new EventEmitter();
var users = [], chatlog = [];

chat.on('message', function(message){
  chatlog.push(message);
});

chat.on('join', function(nickname){
  users.push(nickname);
});

// Emit events here
chat.emit('join', "My awesome message"); // 1/2 Emit the 'join' event on the chat object and pass in a custom message as a string.
chat.emit('message', "Whatever"); // 2/2 Emit the 'message' event on the chat object and pass in a custom message as a string.
```

## 2.4 Request Events - Challenge
*in app.js*
```js
var http = require('http');

var server = http.createServer();
server.on('request', function(request, response){ // Add an event listener on the server variable that listens to the request event. The event listener should take a callback with two arguments, request and response.
  response.writeHead(200);
  response.write("Hello, this is dog");
  response.end();
});

server.listen(8080);
```

## 2.5 Listening Twice - Challenge
*in app.js*
```js
var http = require('http');

var server = http.createServer();
server.on('request', function(request, response){
  response.writeHead(200);
  response.write("Hello, this is dog");
  response.end();
});
server.on('request', function(request, response){ // 1/2 Add a second 'request' handler to the HTTP server.
  console.log("New request coming in..."); // 2/2 Inside the new handler, log the message "New request coming in...".
});
server.listen(8080);
```

## 2.6 Listening for Close - Challenge
*in app.js*
```js
var http = require('http');
var server = http.createServer();

server.on('request', function(request, response) {
  response.writeHead(200);
  response.write("Hello, this is dog");
  response.end();
});

server.on('request', function(request, response) {
  console.log("New request coming in...");
});

server.on('close', function(){ // 1/2 Listen for the 'close' event on the server. The event should take a callback that accepts no parameters.
  console.log("Closing down the server..."); // 2/2 Inside the callback, log the message "Closing down the server...".
});

server.listen(8080);
```
---
## 3.1 Streams
### What are Streams
Access the data piece by piece / chunk by chunk.
They are like channels, where data can simply flow through.

Two most common types of streams:
1. Readable
2. Writeable

### Streaming Response
The request object is a readable stream, the response a writeable.

### How To Read From The Request
The request object inherits from EventEmitter. The request object can communicate with other objects through firing events. The events fired are readable; which is fired when data is ready to be consumed. And end; which is fired when the client is done sending all the data.

To print what is receivend from the request:
```js
http.createServer(function(req, res){
  response.writeHead(200);
  request.on('readable', function(){
    var chunk = null;
    while (null !== (chunk = request.read())) {
      //console.log(chunk.toString()); // because chunks are buffered
      response.write(chunk);
    }
  });
  request.on('end', function(){
    response.end();
  });
}).listen(8080);

/* or when all it needs to do is write to a writeable from a readable stream, node offers a helper function to pipe these operations together: .pipe() */
http.createServer(function(req, res){
  response.writeHead(200);
  request.pipe(response);
}).listen(8080);
```

### Reading and Writing a file
```js
var fs = require('fs'); // require filesystem module

var file = fs.createReadStream("readme.md");
var newFile = fs.createWriteStream("readme_copy.md");

file.pipe(newFile);
```

### Upload a file
```
var fs = require('fs');
var http = require('http');

http.createServer(function(req, res){
  var newFile = fs.createWriteStream("readme_copy.md");  
  request.pipe(newFile);
  request.on('end', function(){
      response.end('uploaded!');
  });
}).listen(8080);
```
To call:
```
$ curl --upload-file readme.md http://localhost:8080
```

### File Uploading Progress
```js
var fs = require('fs');
var http = require('http');

http.createServer(function(req, res){
  var newFile = fs.createWriteStream("readme_copy.md");  
  var fileBytes = request.headers['content-length'];
  var uploadBytes = 0;
  request.on('readable', function(){
    var chunk = null;
    while(null !== (chunk = request.read())){
      uploadedBytes += chunk.length;
      var progress = (uploadedBytes / fileBytes) * 100;
      response.write("progress: " + parseInt(progress, 10) + "%\n");
    }
  });

  request.pipe(newFile);
  request.on('end', function(){
      response.end('uploaded!');
  });
}).listen(8080);
```

## 3.2 File Read Stream - Challenge
*in app.js*
```js
var fs = require('fs');

var file = fs.createReadStream("fruits.txt"); // 1/3 Use the fs module to create a Readable stream for fruits.txt. Store the new stream in a variable called file.
file.on('readable', function(){ // 2/3 Listen to the 'readable' event on the newly created stream and give it a callback.
  var chunk = null; // 3/3 Inside the callback, read the data chunks from the stream and print them to the console using a while loop.
  while(null !== (chunk = file.read())){
    console.log(chunk.toString());
  }
});
```

### 3.3 File Piping - Challenge
*in app.js*
```js
var fs = require('fs');

var file = fs.createReadStream('fruits.txt');

/* file.on('readable', function(){
  var chunk;
  while(null !== (chunk = file.read())){
    console.log(chunk.toString());
  }
}); 1/2 Remove the code for the readable handler */

file.pipe(process.stdout); // 2/2 Call file.pipe passing it to process.stdout.
```

### 3.4 Fixing Pipe - Challenge
*in app.js*
```js
var fs = require('fs');

var file = fs.createReadStream('origin.txt');
var destFile = fs.createWriteStream('destination.txt');

file.pipe(destFile, { end: false }); // Consult the pipe documentation to keep the write stream open and dispatches the end event.

file.on('end', function(){
  destFile.end('Finished!');
});
```

### 3.5 Download Server - Challenge
*in app.js*
```js
var fs = require('fs');
var http = require('http');

http.createServer(function(request, response) {
  response.writeHead(200, {'Content-Type': 'text/html'});

  var file = fs.createReadStream('index.html');
  file.pipe(response); // Use pipe() to send index.html to the response
}).listen(8080);
```

---
## 4.1 Modules
### Create our own module
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

### Making HTTP Requests
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

### Require Search
```
var make_request = require('./make_request'); // looks in same directory
var make_request = require('../make_request'); // looks in parent directory
var make_request = require('/Users/eric/nodes/make_request');

var make_request = require('make_request'); // searches for closest node_modules directory
```

### Installing a npm module
*in /Home/my_app*
```
$ npm install request // installs into local node_modules directory (/Home/my_app/node_modules/request)
```

### Local vs Global
Global npm modules can't be required inside an application.

## 4.2 Missing Exports - Challenge
*in high_five.js*
```js
var highfive = function() {
  console.log("smack!!");
};
module.exports = highfive; // Add the proper exports line to have a successful high five!
```
## 4.3 Export a function - Challenge
*in app.js*
```js
var myRequest = require('my_request'); // 3/3 Require the my_request.js module in app.js.

myRequest('Hello, this is dog.');
```
*in my_request.js*
```js
// 1/3 Move the myRequest function and the http require into my_request.js
var http = require('http');

var myRequest = function(message) {
  var request = http.request('http://codeschool.com', function(response) {
    response.pipe(process.stdout, { end: false });
  });

  request.write(message);
  request.end();
};
module.exports = myRequest; // 2/3 Export the myRequest function.
```
## 4.4 Exporting An Object - Challenge
*in logger.js*
```js
module.exports.warn = function(message) { // 2/3 Export the warn function so we can use it in app.js by assigning it to the exports object.
  console.log("Warning: " + message);
};

module.exports.info = function(message) { // 1/3 Export the info function so we can use it in app.js by assigning it to the exports object.
  console.log("Info: " + message);
};

exports.error = function(message) { // 3/3 Export the error function so we can use it in app.js by assigning it to the exports object.
  console.log("Error: " + message);
};
```
*in app.js*
```js
var logger = require('./logger');

logger.info('This is some information');
logger.warn('something bad is happening');
```
## 4.5 Installing Local Modules - Challenge
```
$ npm install underscore // Install the npm module underscore
```
## 4.6 Installing Global Modules - Challenge
```
$ npm install coffee-script -g // Install the npm module coffee-script globally
```
## 4.7 Dependency - Challenge
*in package.json*
```json
{
  "name": "My Awesome Node App",
  "version": "1",
  "dependencies": {
        "connect": "2.1.1",
        "//": "1/2 Add the connect dependency",
        "underscore": "1.3.3",
        "//": "2/2 Add the underscore dependency"
  }
}
```
## 4.8 Semantic Versioning - Challenge
*in package.json*
```json
{
  "name": "My Awesome Node App",
  "version": "1",
  "dependencies": {
    "connect": "~2.2.1",
    "//": "1/2 Update the connect version to fetch the latest patch-level changes",
    "underscore": "~1.3.3",
    "//": "1/2 Update the underscore version to fetch the latest patch-level changes"
  }
}
```
---
## 5.1 Express
A "Sinatra inspired web development framework for Node.js -- insanely fast, flexible and simple".
- Easy route URLs to callbacks
- Middleware (from Connect)
- Environment based configuration
- Redirection helpers
- File Uploads

### Introducing Express
```node
$ npm install --save express
```
```js
var express = require('express');
var app = express();

app.get('/', function(req, res){
  response.sendFile(__dirname + "index.html"); // __dirname = current directory
});
app.listen(8080);
```
```node
$ curl http://localhost:8080/
```

### Express Routes
Create an endpoint where a twitterusername can been send in, get the latest 10 tweets and display them.
*in app.js*
```js
var request = require('request');
var url = require('url');

app.get('/tweets/:username', function(req, res){
  var username = req.params.username;

  // Get last 10 tweets for screen_name
  options = {
    protocol: "http:",
    host: 'api.twitter.com',
    pathname: '/1/statuses/user_timeline.json',
    query: { screen_name: username, count: 10 }
  }

  var twitterUrl = url.format(options);
  // Pipe request to response
  request(twitterUrl).pipe(res);
});
```
```node
// Start nodeserver
$ node app.js
// Get Data
$ curl -s http://localhost:8080/tweets/eallam
// To beautify json:
$ npm install prettyjson -g
// Get Data Again
$ curl -s http://localhost:8080/tweets/eallam | prettyjson
```
Instead of printing it to the response, print it to the browsers through templatess:
*in my_app/package.json*
```json
"dependencies": {
  "express": "4.9.6",
  "ejs": "1.0.0"
}
```
```node
$ npm install --save ejs // Embedded JavaScript
```
The default template directory is /Home/eric/my_app/views

*back in app.js*
```js
app.get('/tweets/:username', function(req, res){
  ...
  // Pipe request to response
  // request(twitterUrl).pipe(res);
  request(url, function(err, res, body){
    var tweets = JSON.parse(body);
    response.locals = { tweets: tweets, name: username };
    response.render('tweets.ejs');
  });
});
```
*in tweets.ejs*
```html
<h1>Tweets for @<%= name %></h1>
<ul>
  <% tweets.forEach(function(tweet){ %>
    <li><%= tweet.text %></li>
  <% }); %>
</ul>
```

## 5.2 Express Routes - Challenge
*in app.js*
```js
var express = require('express');
var app = express();

app.get('/tweets', function(req, res){ // 1/3 Create a GET route for '/tweets' and give it the proper callback which accepts two parameters: the request and response.
  res.sendFile(__dirname + "/tweets.html"); // 2/3 Send back the file tweets.html which lives under the project's root path. Remember to use __dirname to locate tweets.html.
});
app.listen(8080); // 3/3 Have the express app listen on port 8080
```

## 5.3 Route Params - Challenge
*in app.js*
```js
var express = require('express');
var app = express();

var quotes = {
  'einstein': 'Life is like riding a bicycle. To keep your balance you must keep moving',
  'berners-lee': 'The Web does not just connect machines, it connects people',
  'crockford': 'The good thing about reinventing the wheel is that you can get a round one',
  'hofstadter': 'Which statement seems more true: (1) I have a brain. (2) I am a brain.'
};

app.get('/quotes/:name', function(req, res){ // 1/2 Create a route for '/quotes' that takes a name parameter as part of the URL path.

  // 2/2 Use the name parameter from the URL to retrieve a quote from the quotes object and write it out to the response.
  var name = req.params.name;
  res.end(quotes[name]);
});
app.listen(8080);
```
## 5.4 Rendering - Challenge
*in app.js*
```js
var express = require('express');
var app = express();

var quotes = {
  'einstein': 'Life is like riding a bicycle. To keep your balance you must keep moving',
  'berners-lee': 'The Web does not just connect machines, it connects people',
  'crockford': 'The good thing about reinventing the wheel is that you can get a round one',
  'hofstadter': 'Which statement seems more true: (1) I have a brain. (2) I am a brain.'
};

app.get('/quotes/:name', function(req, res) {
  var quote = quotes[req.params.name];
  res.render('quote.ejs', { // 1/3 Render the quote.ejs template to the response
    name: req.params.name,
    quote: quote
  }); // 2/3 Make the name and the quote data available to the template
});
app.listen(8080);
```
*in views/quote.ejs*
```html
<!-- 3/3 Add the data to the template -->
<h2>Quote by <%= name %></h2>

<blockquote>
  <%= quote %>
</blockquote>
```

## 5.5 URL Building - Challenge
*in app.js*
```js
var url = require('url');

options = {
  // add URL options here
  protocol: "http:",
  host: "search.twitter.com",
  pathname: "/search.json",
  query: { q: "codeschool" }
};

var searchURL = url.format(options);
console.log(searchURL);
```
## 5.6 Doing the Request
*in app.js*
```js
var url = require('url');

var options = {
  protocol: "http:",
  host: "search.twitter.com",
  pathname: '/search.json',
  query: { q: "codeschool"}
};

var searchURL = url.format(options);
var request = require('request'); // 1/3 Require the request module and assign the return function to a variable.
request(searchURL, function(err, res, body){ // 2/3 Issue a request to searchURL. the callback function takes three parameters: error, response and body.
  console.log(body); // 3/3 Log the response body to the console.
});
```
## 5.7 Express Server
*in app.js*
```js
var express = require('express'); // 1/5 Require the express module
var app = express(); // 2/5 Create the Express server and name it app.

app.get('/', function(req, res){ // 3/5 Create a route for GET requests to /
  request(searchURL).pipe(res); // 4/5 Issue a request to searchURL and pipe the results into the response.
});
app.listen(8080); // 5/5 Tell app to listen on port 8080
```
