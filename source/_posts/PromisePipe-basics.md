title: 'PromisePipe: basics'
date: 2015-08-13 20:39:17
tags:
---
I really like building business logic as a chain. And I like the idea of building logic out of reusable functions that does simple transformations. All logic can be described as a chain of data transformations. Even the [express](http://expressjs.com/) backend and all middleware chain is a chained logic that transforms the request into response.

[PromisePipe](https://github.com/edjafarov/PromisePipe) is a constructor of reusable Promise chains. Additionally it allows to [customize](https://github.com/edjafarov/PromisePipe#promisepipeusename-handler) it's API, gives [better debugging](http://localhost:4000/2015/07/PromisePipe-debugging/) and allows to make [cross process logic chains](http://localhost:4000/2015/06/PromisePipe-cross-process-homogenous-Promise-chains/).

You can install PromisePipe from npm:

```
$ npm install promise-pipe
```

In you javascript file you would start

```javascript
var PromisePipe = require("promise-pipe")();
var action = PromisePipe().then(addOne);

action(10).then(actionDone); //11
action(20).then(actionDone); //21

function actionDone(result){
  console.log(result)
}
function addOne(data){
  return data + 1;
}
```

the `action` is a constructor for the logic and is a function that returns a Promise. You can add chains modifying it's logic.

Let's create an action that would reverse the Array. Reverse chain would look same as if we are using native Promises:

```javascript
function reverse(data){
  if(!Array.isArray(data)) return Promise.reject(new Error('Data is not an Array'));
  return data.reverse();
}
//or if async
function reverse(data){
  return new Promise(function(resolve, reject){
    if(!Array.isArray(data)) return reject(new Error('Data is not an Array'));
    resolve(data.reverse());
  })
}
```
The `action` will be modified like:

```javascript

var action = PromisePipe().then(reverse).catch(handleError);

action([1,2,3]).then(actionDone);
//under the hood same as Promise.resolve([1,2,3]).then(reverse).catch(handleError).then(actionDone)

function handleError(err){
  console.error("Error: " + err);
  return Promise.reject(err)
}
```
Where `handleError` will catch the error if argument would not be an Array.

PromisePipe have small API but you can extend it with your custom methods.

For example we can extend PromisePipe API with reverse function like this.

```javascript
PromisePipe.use('reverse', function reverse(data){
  if(!Array.isArray(data)) return Promise.reject(new Error('Data is not an Array'));
  return data.reverse();
});
```
The action will look like

```javascript
var action = PromisePipe().reverse().catch(handleError);
```
PromisePipe extension function have first two arguments `(data, context)` as any chain function. All other arguments can be used in API method.
For example similar to reactive streams `.map` method that will accept `mapFunction` as argument:

```javascript
PromisePipe.use('map', function map(data, context, mapFunction){
  if(!Array.isArray(data)) data = [data];
  return data.map(mapFunction)
})
```
Our action
```javascript
var action = PromisePipe()
  .map(item => item + 1)
  .reverse()
  .catch(handleError);
```

And the example will look like:

```javascript
var PromisePipe = require("promise-pipe")();

PromisePipe.use('reverse', function reverse(data){
  if(!Array.isArray(data)) return Promise.reject(new Error('Data is not an Array'));
  return data.reverse();
});

PromisePipe.use('map', function map(data, context, mapFunction){
  if(!Array.isArray(data)) data = [data];
  return data.map(mapFunction)
})

var action = PromisePipe()
  .map(item => item + 1)
  .reverse()
  .catch(handleError);

action([1,2,3]).then(actionDone);

action([3,4,5]).then(actionDone);

function actionDone(result){
  console.log(result)
}
function handleError(err){
  console.error("Error: " + err);
  return Promise.reject(err)
}
```
And you can play around with whis PromisePipe example [online](http://requirebin.com/?gist=51886d9c0cc4f41a6a9c);
