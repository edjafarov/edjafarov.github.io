title: "PromisePipe: cross process homogenous Promise chains"
date: 2015-06-17 14:42:39
tags:
---
I used to be a backend developer and even earlier I was a frontend developer. I guess I was good backend dev for my frontend colleagues at that time. Since I was thinking about API’s as a consumer of that API.

I wasn’t ever that lucky as a frontend dev. Building API is hard. It usually takes a lot of time and wtf’s to get same vision on how communication with the server should work.  We start with REST API, then everyone has its own vision on what is REST. Each modification of API is a pain and it always take a lot of time and talks to make a change.

Today as frontend developer I usually describe resource calls with Promises. I use promises because I find their chaining API really nice to describe the potentially asynchronous business logic.

It might be that any business logic could be represented as a chain of data transformations. Even saving of data in DB is a transformation of data into item ID.

Lets check following code for a simple frontend business logic built with Promises:

```javascript
Promise.resolve(item)
  .then(validateItem)
  .then(postItem)
  .then(addItem)
  .catch(handleError)
```

`postItem` here will return a Promise that will be resolved when the server replies.

On server side, we would probably have some express route:

```javascript
app.post('/api/items',
  validateItemMiddleware,
  saveItemInDBMiddleware,
  returnItemMiddleware)
```
If Promises would appear earlier, I think nodejs [express](http://expressjs.com/) would use them instead of middlewares.
Built with Promise chains the server will probably look something like:

```javascript
app.post('/api/items')
  .then(validateItem)
  .then(saveItemInDB)
  .then(returnItem)
```

Since any Promise could be constructed out of composition of Promises lets imagine for a second that we do not have a frontend and backend - our code will look like:

```javascript
var postItem = function(data){
  return Promise.resolve(data)
    .then(validateItem)
    .then(saveItemInDB)
    .then(returnItem)
}

Promise.resolve(item)
  .then(validateItem)
  .then(postItem)
  .then(addItem)
  .catch(handleError)
```

Obviously in that case we would need no API at all and we won’t waste time deciding how to name the Url and what method to use right?

And we can go even deeper:

```javascript
Promise.resolve(item)
  .then(validateItem)
  .then(validateItemServer)
  .then(saveItemInDB)
  .then(addItem)
  .catch(handleError)
```

Of cause, you can’t do it with plain promises - but with [PromisePipe](https://github.com/edjafarov/PromisePipe) you can.

PromisePipe is a builder for reusable promise chains.
It has more control over the execution of chains and can control how to execute each of them.

PromisePipe is a singleton. You build chains of business logic and run the code both on server and client. Chains marked to be executed on the server will be executed on the server only and chains marked to be executed in the client will be executed in the client. You need to implement methods in PromisePipe to pass messages from the client to the server. And it is up to you what transport to use.

![PromisePipe](/images/PromisePipe.png)

So you need to write some boilerplate code that would pass messages around between server and client to try it ([simple example](https://github.com/edjafarov/PromisePipe/tree/master/example/simple)). So far I wrote examples with [socket.io](http://socket.io/) as a transport but there is no problem to use plain HTTP request or any protocol that can pass messages around.

![simple example](/images/Ck1tyZ5qA8.gif)

With PromisePipe our code example would look like:

```javascript
var doOnServer = PromisePipe.in('server')
var addItemAction = PromisePipe()
  .then(validateItem)
  .then(doOnServer(validateItemServer))
  .then(doOnServer(saveItemInDB))
  .then(addItem)
  .catch(handleError);
addItemAction(item) // will pass complete chain
```
When execution comes to `validateItemServer` chain PromisePipe is passing execution to server with execution message and proceeds there. `validateItemServer` and `saveItemInDB` are executing on the server side and the message passed back to a client to proceed execution starting with `addItem`.

PromisePipe allows to extend API with custom methods and you can build expressive DSL that will describe business logic:

```javascript
var doOnServer = PromisePipe.in('server')
var addItemAction = PromisePipe()
  .validate('item')
  .validateServer('item')
  .db.save.Item()
  .then(addItem)
  .catch(handleError);
addItemAction(item) // will pass complete chain
```

For example, here is a [mongodb API](https://github.com/edjafarov/mongo-pipe-api) for PromisePipe. And here is an example of [todo-app](https://github.com/edjafarov/PromisePipe/tree/master/example/mongotodo)([live](bit.ly/promisepipe-todo )) which uses this "mongo-pipe-api". `validateServer` and "mongo-pipe-api" should be marked as serverside methods. So, they would be executed on the server only.


PromisePipe allows to build business logic out of simple transformation chains which can run in different processes while the logic itself still simple and homogenous.

With PromisePipes you get:
* **simplicity**

	Build up your logic in a functional manner with simple transformations. Forget about the process to process communication and save time for building business logic.

* **testability**

	Each chain can be tested independently. It is also easy to assemble pieces of logic together and test parts in isolation.

* **isomorphism**

	PromisePipe was created to work in a cross process environment. You will get isomorphic business logic out of the box if you build chains with isomorphism in mind. So if you do not use any env specific API's in your logic you can run pipe in a single process or expect the chain to work in multiple environments like browser and server.

* **scalability**

    Each chain could be running in a separate process without much effort. That doesn't mean you have scalability out of the box. But you have a nice way to distribute your load over multiple processes.

* **frontend guys can build complete business logic**

    Chains are easy to compose together. The main idea behind is to allow frontend guys to use simple building blocks to build backend functionality encapsulating complexity inside meaningful business logic chains.

![homogenous code](/images/homogenous-code.png)

I believe PromisePipe will help us to push microservice architectures forward. The homogenous business logic allows decoupling of logic from process to process communication. Which makes no difference between code that is running in monolith and miscroservice architecture.