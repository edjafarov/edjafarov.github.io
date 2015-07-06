title: 'socker: websocket CRUD over engine.io'
date: 2013-10-15 04:10:06
tags:
---
One of substantial difference of using sockets instead of plain http requests is that we usually broadcast messages without expecting any response. While building [jschat](http://github.com/openconf/jschat) I thought it would be ehough. Though even for a chat we need the response if we want reliability and better experience. Think of "Pending" state of a message when sending it in offline mode.

Raw libraries doesn't provide any 'response' like functionality thus I had to build my own implementation. As a base for jschat we are using [engine.io](https://github.com/LearnBoost/engine.io) because [socket.io](https://github.com/learnboost/socket.io) is not supported for a long time and engine.io is kind of it's successor and it's awesome.

## socker
[Socker](https://github.com/edjafarov/socker) is inspired by [express](https://github.com/visionmedia/express). Simple and lightweight implementation of middlewares, routing and error handling. Socker wrapping both engine.io and engine.io-client and providing additional methods that implement express like API.

### setting up socker
We can use engine.io and socker with or without express
```javascript
//backend
var engine = require('engine.io');
var socker = require('socker');

var app = require('express')();
var server = http.createServer(app);
server.listen(nconf.get('server:port'));
server = engine.attach(server);

socker(server); // wrapping server with additional methods
server.on('connection', function(socket){
  socker.attach(socket);// we are attaching socker to the socket
});
```

```
// frontend
var socket = require('engine.io')('ws://localhost');
var sockerClient = require('socker-client'); // we can use it as standalone though
sockerClient(socket);
```
### sending the message from client
On the client we have additional `serve` method on the socket
```
//socket.serve(<optional> route, <optional> message, <required> callback);

socket.serve({message:"Hello world!"}, function(err, data){
  // err contains error object if it was thrown
  // data is a response data
})
socket.serve('READ /api/item/343', function(err, data){
  // err contains error object if it was thrown
  // data is a response data
})
socket.serve('CREATE /api/items', {itemName : "foo"} function(err, data){
  // err contains error object if it was thrown
  // data is a response data
})
```
### handling the message on server
On the server we additionally have sock.use and sock.when methods. `sock.use` adds middleware handler. Middleware in our case instead of request and response gives socket and data objects.
```
server.sock.use(logger);
function logger(socket, data, next){
  // socket is a socket object
  // data - the data object sent with `request`
  console.log(data);
  
  // socket object have .json method to send a response
  if(weNeedTo) return socket.json({responseMessage: "bar"});
  
  // or we can throw an error
  if(weNeedToThrowError) return next("Error message");
  
  // if we need to pass to next handler
  next() 
}
```
`socket` is a message context object. And you can pollute it whenever you like. The "session" context object is `socket.__proto__` so If you want to save some data for connection lifetime use prototype object.

### handling routing
Inside routing middlewares the route is already parsed and we have also socket.params object with all params from the route.
```
server.sock.when('CREATE /api/items', checkItem, createItem);
server.sock.when('READ /api/item/:id', getItem);
function getItem(socket, data, next){
  // socket.params['id'] contains id from the route
  // data is a data sent
  socket.json({room:"name", id: 343});
}
```
Using the mask `METHOD uri` is not required for socker. In the same manner you can name your routes.
``` javascript
server.sock.when('Server, please, give me room with :id', callback);
//or
server.sock.when('Bloody server! I command you to stay on your knees and give all items you got.', callback);
```
### error handling
We can also customize error handling.
```
server.sock.use(function(err, socket, data, next){
  if(err){
    socket.json({type:"ERROR", err:err, code: 500})
  }
})
```
It is important to put `type : "ERROR"` because that is the way client will treat the message as error.

### try it
You got clean and simple API and you got some latency boost. You save roundtrip to your session storage and handshake time. And now with [socker](https://github.com/edjafarov/socker) moving from express REST API to socket based API is really simple.





