title: Under the hood of PromisePipe
date: 2015-09-06 13:40:12
tags:
---
There will be a lot of simplified pieces of code that shows how PromisePipe works.

It all started with this piece of code from awesome article about Promises on html5rocks: [JavaScript Promises. There and back again](http://www.html5rocks.com/en/tutorials/es6/promises/)
```javascript
// Loop through our chapter urls
story.chapterUrls.reduce(function(sequence, chapterUrl) {
  // Add these actions to the end of the sequence
  return sequence.then(function() {
    return getJSON(chapterUrl);
  }).then(function(chapter) {
    addHtmlToPage(chapter.html);
  });
}, Promise.resolve());
```
This piece of code is taking an array of chapterUrls and chains the fetching JSON for each URL and adding the html to the page sequentially.

What I like here is that you can take any array of functions and get a promise chain:
```javascript
var sequence = [fn1, fn2, fn3];
sequence.reduce(function(sequence, fn, i){
  return sequence.then(fn);
}, Promise.resolve())
```
and it equeals to
```javascript
Promise.resolve().then(fn1).then(fn2).then(fn3)
```
We can build a constructor for such reusable chains. And we will have chains that look like and work like Promise chains but can be called multiple times.

```javascript
function PromisePipe(){
  var sequence = [];
  var result = function(){
    return sequence.reduce(function(sequence, fn, i){
      return sequence.then(fn);
    }, Promise.resolve())
  }
  result.then = function(fn){
    sequence.push(fn)
  }
  return result;
}
var action = PromisePipe().then(fn1).then(fn2).then(fn3);
action(1); //call once
action(10); //call twice same as
//Promise.resolve(10).then(fn1).then(fn2).then(fn3)
```

That also allows us to have more control over the execution of Promise chain. For example promise chain functions always have a single argument and we can add the second argument as a context shared through all the chains, just like request/response in nodejs middlewares.

```javascript
var context = {};

var fn1 = {
  id: getUniqueId()
  func: function(data){
    return fn1.call(this, data, context);
  },
  env: env
}
```

I also added some meta information like environment where it should be executed and unique ID of a chain. And that is how PromisePipe can control the execution in a runtime.

```javascript
origSequence.reduce(function(sequence, fn, i){
  var previousFn = origSequence[i - 1];
  if(fn.env !== previousFn.env) {
    return PromisePipe.transition(previousFn.env, fn.env)
    //smth else send message previousFn.env to fn.env
    //and return a promise that will be resolved when transaction
    //will be back and resolved
  }
  return sequence.then(fn.func);
}, Promise.resolve());
```
When the execution comes to a chain that should be executed in different environment the PromisePipe is building a message with data, context, pipe id and chain id's that should be executed on other environment.

We need to deliver the message to other env and execute it in PromisePipe.

```javascript
PromisePipe.execTransitionMessage = function(message){
  var firstChainIndex = getIndexByChainId(message.firstChainId);
  var lastChainIndex = getIndexByChainId(message.lastChainId);
  var localChain = sequences[message.pipeId].slice(firstChainIndex, lastChainIndex);
  return localChain.reduce(function(sequence, fn, i){
    return sequence.then(fn.func);
  }, Promise.resolve());
}

//where message is something like
PromisePipe.createTransitionMessage = function createTransitionMessage(data, context, pipeId, chainId, envBackChainId, callId){
  return {
    data: data,
    context: context,
    pipe: pipeId,
    chains: [firstChainId, lastChainId]
  };
};
```
After the message will be executed and resolved the message with updated data can be passed back to previous environment to resolve execution of original pipe.


So basically we are running the same code on client and server but only pieces of code are actually running on each environment.
```
CLIENT
--0-------1-------2-------3-------4-------5---
client->client->      ->      ->      ->client

SERVER
--0-------1-------2-------3-------4-------5---
      ->      ->server->server->server->

RESULT
--0-------1-------2-------3-------4-------5---
client->client->server->server->server->client
```
This is a general idea of how PromisePipe works. The pieces of code you see here are mostly a pseudo code, real code little bit more complicated and handles multiple features including error handling, security, api extending, better debuggability, etc...

If you want to know more about PromisePipes check:

* [PromisePipe: cross process homogenous Promise chains](http://eldar.djafarov.com/2015/06/PromisePipe-cross-process-homogenous-Promise-chains/)
* [PromisePipe: basics](http://eldar.djafarov.com/2015/08/PromisePipe-basics/)
* [PromisePipe: debugging](http://eldar.djafarov.com/2015/07/PromisePipe-debugging/)