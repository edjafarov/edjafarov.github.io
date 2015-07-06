title: 'Angularjs is evil: dependency injection'
date: 2014-02-17 20:11:02
tags:
---
A year ago I was excited of angular and I still think angular is a game changer. I see how community charmed with angular and how easy to start building webapps apps with it. However after a year of using angular I must say that I am not recommending investing time into this framework. I will write several posts about why.

* [Angularjs is evil: dependency injection](/2014/02/angularjs-is-evil-dependency--injection/)
* [Angularjs is evil: the scope horror](/2014/04/angularjs-is-evil-the-scope-horror/)
* [Angularjs is evil: overengineering hell](/2014/04/angular-is-evil-overengineering/)

One of things I hate most about angular is its module system. Sadly it is deeply injected in its DNA. Someone decided that DI in JavaScript should look like this:

```javascript
function someFactory(stuff){
	//use stuff
}
someFactory.$inject = ['stuff'];

app.factory(['stuff', function(stuff){
	//use stuff
}])
```
It looks cool. But for me as a JS developer it's too messy and complex (someFactory->someFactoryProvider o_O WTF?!) Here is a dependency injection in pure JS:
```javascript
function someFactory(stuff){
	return function(){ //we're using closure for DI here
    	//use stuff
    }
}
someFactory(stuff) // injecting stuff
```
I am using simple closure for dependency injection. It feels more natural.
But we still can't replace angular's module system right? Thus we need to use CommonJS (you can use [browserify](http://browserify.org/) or [brunch](http://brunch.io/)).
```javascript
var stuff = require('./stuff');
module.exports = function someFactory(){ //and again we use closure
	//use stuff
}
```
Why is that better? It is simple. It is better way for structuring your app - it gives you references and you can directly say where the dependency lives. It already works well for **nodejs**. It is compatible with existing package managers.

I know that how JS modules should look like is a debatable topic. I think that CommonJS pattern quite usable and much better fit for JS than AMD or ES6 modules. Though I definitely think that Angular got it wrong.