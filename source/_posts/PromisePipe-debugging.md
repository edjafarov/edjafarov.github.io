title: "PromisePipe: debugging"
date: 2015-07-06 20:04:14
tags:
---
While Promises look nice there is one thing I always hated about them. They are awful for debugging and they fail silently if you have a typo in your code. You need to catch the error otherwise you will never know what happened. So in PromisePipes I decided to fix that.

So PromisePipe in debug mode shows unhandled exceptions in console:
```
Failed inside test
ReferenceError: ff is not defined
Object.test@/Users/edjafarov/work/PromisePipe/tests/PromisePipe.error.spec.js:28:16
newArgFunc@/Users/edjafarov/work/PromisePipe/src/PromisePipe.js:534:26
{anonymous}() ($)$$internal$$tryCatch@/Users/edjafarov/work/PromisePipe/node_modules/es6-promise/dist/es6-promise.js:304:16
{anonymous}() ($)$$internal$$invokeCallback@/Users/edjafarov/work/PromisePipe/node_modules/es6-promise/dist/es6-promise.js:316:17
{anonymous}()@/Users/edjafarov/work/PromisePipe/node_modules/es6-promise/dist/es6-promise.js:874:13
{anonymous}() ($)$asap$$flush@/Users/edjafarov/work/PromisePipe/node_modules/es6-promise/dist/es6-promise.js:111:9
process._tickCallback@node.js:442:13
```
It notifies you that things go wrong though it is not that useful for real life debugging.

What is really helping is that PromisePipe wraps each chain function into the wrapper that is recording data/context values that chain is called with. You can track how the data is transformed by each chain. And it works out of the box. Just setup debug mode for PromisePipe like this:

```javascript
PromisePipe.setMode("DEBUG")
```

In chrome it looks like this:

![](/images/promisepipe-debug.gif)

##Whats next
* We have the decomposition of all calls of the PromisePipe and transformations during the execution. That allows us to record the log of what happened while reproducing the bug by QA and replay the log later by Developer.

* As well we can record session to use it for autogeneration of integration tests.

* And we can watch a performance of each chain individually.

