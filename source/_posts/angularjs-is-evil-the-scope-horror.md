title: 'Angularjs is evil: the scope horror'
date: 2014-04-21 19:04:27
tags:
---
This is the second post about how angular makes my life painful.


* [Angularjs is evil: dependency injection](/2014/02/angularjs-is-evil-dependency--injection/)
* [Angularjs is evil: the scope horror](/2014/04/angularjs-is-evil-the-scope-horror/)
* [Angularjs is evil: overengineering hell](/2014/04/angular-is-evil-overengineering/)

The scope is most one of most complex concepts in Angular. You never know what scope are you in and what is available inside. Things may change if you add anguments somewhere at the top node.

```javascript
var app = angular.module('myApp', []);

function foo($scope) {
    $scope.value = "foo";
}

function bar($scope) {
    $scope.value = "bar";

}
```
```html
<div ng-controller="foo">
    <div ng-controller="bar">
    {value}
    </div>
</div>
```
You can miss new ```ng-controller``` or someone could overwrite your context. It looks like ok, until you have really complex html.

Things become more infernal when you try to create a directive. Directive may have or may not have the scope. Obviously you are passing the stuff to the scope through arguments of the newly created "element". You can do it in three different ways with craziest API I ever seen - you got three magic symbols `& = @` and they do different things with stuff you are throwing inside your arguments. I can only tell that it looks reasonable untill you find [alternative](http://facebook.github.io/react/) that do same thing and make sense in the same time.

And then you try to do what every angular developer does at least once in his life. You take transclusion and try to put directive scope inside transcluded html. Which looks like a great idea at the beginning.

The other thing that I hate about `$scope` is that I never know when the template will be rendered. It is quite painful to see the magic without knowing what's happening. I know that I can learn it, my point though is that if I need to learn to embrace it is probably done in a wrong way.


