title: 'Angularjs is evil: overengineering hell'
date: 2014-04-28 10:56:09
tags:
---

This I hope is the last post about how Angular will bring you to a world of pain.


* [Angularjs is evil: dependency injection](/2014/02/angularjs-is-evil-dependency-injection/)
* [Angularjs is evil: the scope horror](/2014/04/angularjs-is-evil-the-scope-horror/)
* [Angularjs is evil: overengineering hell](/2014/04/angular-is-evil-overengineering/)


Recently I stumbled on this [Angularjs hate article](http://codeofrob.com/entries/you-have-ruined-javascript.html) that I am totally alligned with on emotional level. Angular is clearly overengineering. And unlike some may say It is not giving you exclusive scalability.

I would compare Angular to React. I know that those are not comparable. But I believe that React based architecture is something that beats Angular solution in terms of simplicity and scalability.

**React vs AngularJS by number of concepts to learn**

* React stack: 4 (everything is a component, some components have state, you may use model instead, Commonjs modules, router).
* AngularJS: 7 (modules, router, controllers, directives, scopes, templates, services, filters).

There are twice more concepts to learn for Angular than React. Not even saying that React's concepts are much more simple. For example you have controller and directive with templates that do more or less the same things. With React you have component only as a building block of your application. We all know that simplicity scales better, right?

**directive vs component**

```javascript
//Angular
App.directive('myDirective', function() {
  return {
    restrict: 'E',
    transclude: true,
    scope: {
   	  link: '@link'
    },
    template: '<a href="#/{{link}}" ng-transclude></a>',
    link: function (scope, element, attrs) {
	  //do stuff with scope
    }
  };
});
//usage
<myDirective link='somewhere'><span>GO</span></myDirective>
```

```javascript
//React
var myComponent = React.createClass({
  componentDidMount: function(){
    //do stuff with this.props
  },
  render: function() {
    return <div href={'#/' + this.props.link}>{this.props.children}</div>;
  }
});
//usage
<myComponent link='somewhere'><span>GO</span></myComponent>
//JSX transformed
myComponent({link:'somewhere'}, span(null,'GO'));
```
Substantial difference between React and Angular here is that React is Javascript friendly - you just put stuff as props and since components are functions these are passed as function arguments.

Component is a function!

**service vs function**
```javascript
//Angular
myApp.service('unicornLauncher', ["apiToken", UnicornLauncher]);

function UnicornLauncher(apiToken) {

  this.launchedCount = 0;
  this.launch = function() {
    // make a request to the remote api and include the apiToken
    ...
    this.launchedCount++;
  }
}
```
```javascript
//Javascript
var apiToken = require('../apiToken.js');

module.exports = function UnicornLauncher() {

  this.launchedCount = 0;
  this.launch = function() {
    // make a request to the remote api and include the apiToken
    ...
    this.launchedCount++;
  }

}
```
Service/provider in Angular is a solution for a made up problem. Just use CommonJs and you won't need service/provider thing. You will just use modules and functions that are natural for JS.

Service is a function!

**filter vs function**
```javascript
//Angular
App.filter('incr', function() {
    return function(input) {
      return input + 1;
    };
  })
<div>{ {value | incr} }</div>
```

```javascript
//React
function incr(input){
  return input + 1;
}

<div>incr(value)</div>
```
Well, directive is pretty useful if you use html templates as strings. Life is easier with React when you do not use strings for templates.

Filter is a function!

**template vs JSX**
```html
//Angular
<div>
  <ProfilePic username='username' />
  <ProfileLink username='username' />
</div>
```

```javascript
//Reactjs
/** @jsx React.DOM */
var Avatar = React.createClass({
  render: function() {
    return (
      <div>
        <ProfilePic username={this.props.username} />
        <ProfileLink username={this.props.username} />
      </div>
    );
  }
});
//transformed
var Avatar = React.createClass({
  render: function() {
    return (
      div(null,
        ProfilePic({username:this.props.username}),
        ProfileLink({username:this.props.username})
      )
    );
  }
});
```
Functions are better than strings. Functions could work with closures. Functions are faster. And in javascript functions are first class citizens. Functions are much more logical than strings.

Template is a function!

With react you can live in function world. And with Angular you live in enterprize patterns world.

My next story will be about why Angular might work for you.
