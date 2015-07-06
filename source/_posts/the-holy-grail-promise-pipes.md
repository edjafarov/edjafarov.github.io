title: 'The Holy Grail: promise pipes'
date: 2015-01-27 19:19:35
tags:
---
The Holy Grail series of posts is about React based framework (inspired by Flux architecture) for building isomorph applications. This framework is like a puzzle consists of multiple segments that should play well together. Though each segment can be used separately.

[PromisePipes](https://github.com/edjafarov/PromisePipe) are reusable and cutomisable promise chains. They allow to build own business logic promise based DSL like:

```javascript
var doAction = PromisePipe().add(5).multiply(2).pow(10).doSomeBusinessLogic(withArgs).save('/api/result').emit('result:saved');

doAction(1); //((1+5)*2)^10 -> POST /api/result -> 'result:saved'
doAction(2); //((2+5)*2)^10 -> POST /api/result -> 'result:saved'
```

##SPA patterns

There are common patterns for frontend SPA patterns. Most of app work is to:

* get data from server.
* modify data.
* render data.
* save data to server.

Usually when user surfs app pages in your SPA app does ` GET -> render` flow. With PromisePipe that would look like:

```javascript
PromisePipe()
.get('/api/data')
.render('view_name')
```
Where the `.get()` is a promise that returns the body as a result to next promise in chain.

When user submits form app does `(data) -> Validation -> SAVE -> render`

```javascript
PromisePipe()
.validate(validationScheme) //validate incoming data, reject if failed
.save('/api/data') //save data if validation successfull
.catch(handleErrors) //catch and handle validation or save error
.render('view_name')
```

You can mix data from various sources with the PromisePipe:
```javascript
var renderAuthorByBookId = PromisePipe()
.get('/api/books/:bookId', {bookId:'id'})
.map(function(data){
	return data.author;
})
.get('/api/books/:authorId', {authorId:'id'})
.render('view_name')

renderAuthorByBookId({id: 10})
```
You can play around with PromisePipe in this [fiddle](http://jsfiddle.net/djkojb/dLehre33/).

### Form validation

PromisePipe was built to work in single direction dataflow architecture inspired by Flux. The PromisePipe role is to prepare (fetch/save) and manipulate data. These pipes are containers of pure business logic and stores/models are listening for events emitted in the pipes context and fill themselves with the data.

The validation of forms pattern is pretty neat. One of solutions for validation is mixing validation result with the data itself. I feel like this is wrong making the data dirty and flow ambigous. With PromisePipe I am separating the data of error object.

```javascript
var context = new Emitter();

var saveEventItem = PromisePipe()
.validate(validationScheme)
.post('/api/events')
.emit('events:add');
.catchAndEmit('events:add:reject')

saveEventItem(formData, context);
```
On submit the app will call the function passing the form data as a first argument and apps context as second.

The store can hook up the context events and look like:
```javascript

function eventsStore(context){
	var form = {
    	data: [],
        errors: {}
    }

    return new Emitter({
    	init: function(){
      		context.on('events:add', addEvent.bind(this));
			context.on('events:add:reject', updateErrors.bind(this));
        },
        get: function(){
        	return form;
        },
        updateErrors: function(errors){
        	form.errors = errors;
            this.emit('change')
        },
        addEvent: function(data){
        	form.data.push(data);
            this.emit('change')
        }
    })
}

var eventsForm = eventsStore(context);

saveEventItem(formData, context);
// validate -> post -> emit(events:add) -> eventsForm.addEvent -> eventsForm.emit('change')
```
### isomporph resources

The `.get`, `.post()`, `emit`, etc methods are not a part of PromisePipe API. But you can extend PromisePipe API. That opens you a possibility to build DSL with [superagent](https://github.com/visionmedia/superagent) thus making it isomorph.

```javascript
var resource = require('superagent');

PromisePipe.use('get', function get(data, context, url, query){
	return new Promise(function(resolve, reject){
		var req = resource.get(prepreUrl.call(context, url));
		if(context.request && context.request.headers){
			req.set(context.request.headers);
		}
		
		if(typeof(query) == 'function') {
			req.query(query.call(context, data));
		} else if(typeof(query) == 'object'){
			req.query(query);
		}
		req.on('error', function(err){
			reject(err);
		})
		req.end(function(res){
			if(res.error) return reject(res.error);
			resolve(res.body);
		});
	});
});
```

###PromisePipe

###install

npm install promise-stream

###extend

You can extend `PromisePipe` API with additional methods. Thus you are able to build your own customized DSL.
```javascript
var PromisePipe = require('promise-pipe');

PromisePipe.use('log', function(data, context, name){
	if(name) {
    	console.log(data[name]);
    } else {
    	console.log(data)
    }
    return data;
})

var action = PromisePipe().log().log('foo');

action({foo:"baz", bar:"xyz"})
// {foo:"baz", bar:"xyz"} <- log()
// baz <- log('foo')
```
###API
### PromisePipe

### PromisePipe.use(name, handler)
Allows to build your own customized DSL. `handler` is a function with arguments

```javascript
function handler(data, context, arg1, ..., argN){
	//you can return Promise
	return data;
}
PromisePipe.use('custom', handler);

PromisePipe().custom(arg1, ..., argN)
```

### Stream
Is a function that returns a promise. First argument is a data, second is a context. While `data` behaves the same way as in Promises `context` is passing thorough whole chain of promises.

```javascript
var stream = PromisePipe()
.then(function(data, context){
	console.log(data, context);
    context.foo = "bar";
	return data + 1;
}).then(function(data, context){
	console.log(data, context);
    context.xyz = "baz";
	return data + 1;
}).then(function(data, context){
	console.log(data, context);
})
stream(2, {});
//2 {}
//3 {foo:"bar"}
//4 {foo:"bar", xyz:"baz"}
```
### stream:then
As in Promises you can pass two functions inside for success and fail.

```javascript
var stream = PromisePipe()
.then(function(data, context){
	return //Promise.resolve/reject
}).then(success, fail)
```
### stream:catch
The catch is taking single argument and bahaves same as Promise catch.

### stream:join
You can join PromisePipes if you like.

```javascript
var stream = PromisePipe()
.then(function(data, context){
	return data + 1;
});
var stream2 = PromisePipe()
.then(function(data, context){
	return data + 2;
})
.join(stream)
.then(function(data){
	console.log(data);
});

stream2(1) //4
```


