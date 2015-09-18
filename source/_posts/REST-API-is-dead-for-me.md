title: REST API is dead for me
date: 2015-09-18 19:23:52
tags:
---
On the client, we have some data structure that we need to save in database, or process somehow, or else. In any case, it is a JSON, and It probably isn't flat. What do we do?

1. convert the data into the combination of HTTP method, API URL and body.
2. make an HTTP request
3. take the request on server side
4. try to decode the combination of method, API URL and body into original data.
5. do a business logic with the data.

At step 1 we have an object and all steps up to step 4 all we are doing is moving this object to the server using this crazy long way.

The question is **WHY?**

On a server side we have a set of middlewares:

```javascript
app.get('/doStuffOnServer', middleware1, middleware2, middleware3)
```
Those middlewares are taking a data from Request object and transform this data into a response.

But we can think of server as an asychronous function for client:
```javascript
function doStuffOnServer(data){
  return middleware3(middleware2(middleware1(data)));
}
```
We actually shouldn't care about what transport it use. It can be HTTP or Sockets it doesn't matter. All your API calls should be just simple asynchronous functions and this functions should be generated automatically by your server framework, for **different** languages and technologies. Isn't it easier to write auto generator **once** or implement REST API each time.

I spent several years trying to understand the way good REST API should look like. Now REST API is dead for me. It just doesn't make sense anymore.