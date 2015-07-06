title: 'compy - simple way of building webapps'
date: 2013-08-08 03:26:05
---

**Compy** is a simple, 'zero' configuration web app builder/compiller integrated with client package manager [component](https://github.com/component/component).
Although there is almost no configuration it gives you all flexibility to code the way you like.


###Start
Install compy with `npm` :
```sh
$ npm install compy -g
```

To start an app all you need is to tell **compy** where is the beginning. To do that you need `package.json` file with `compy.main` property pointing to main js file of your app.
```json
{
  "name" : "app",
  "compy" : {
    "main" : "appstart.js"
  }
}
```

`appstart.js` file will be executed right after the page load.

To compile app, just run `$compy compile`

**Compy** will generate `./dist` folder with _app.js_, _app.css_ and [_index.html_](https://github.com/edjafarov/compy/blob/master/index.html). All css in your directory will be concatenated/minified into _app.css_ file.

Compy have static server so you can check the result with
```sh
$ compy server [watch]
```

adding _watch_ option will recompile the app and livereload the changes in a browser.
### Components

Most powerful part of compy local require and integration with [component](https://github.com/component/component).

To install jquery:
```sh
$ compy install jquerycomp/jquery
```
to use jquery in code:
```javascript
var $ = require('jquery');
$(document.body).html("Hallo world");
```
Local require will work the same as in node.js
```javascript
//filename: add.js
module.exports = function(a, b){
  return a + b;
}
```
```javascript
//filename: appstart.js
var add = require('./add');
add(2,2); //4
```
### Plugins

**compy** support [component](https://github.com/component/component)'s plugins.

- [rschmukler/component-stylus-plugin](https://github.com/rschmukler/component-stylus-plugin) — precompile stylus
- [segmentio/component-jade](https://github.com/segmentio/component-jade) — precompile jade templates
- [anthonyshort/component-coffee](https://github.com/anthonyshort/component-coffee) - require CoffeeScript files as scripts
- [anthonyshort/component-sass](https://github.com/anthonyshort/component-sass) - compile Sass files using node-sass
- [kewah/component-builder-handlebars](https://github.com/kewah/component-builder-handlebars) - precompile Handlebars templates
- [ericgj/component-hogan](https://github.com/ericgj/component-hogan) - Mustache transpiler for component (using Hogan)
- [segmentio/component-sass](https://github.com/segmentio/component-sass) — Sass transpiler for component
- [segmentio/component-json](https://github.com/segmentio/component-json) — Require JSON files as Javascript.
- [queckezz/component-roole](https://github.com/queckezz/component-roole) — Compile [Roole](http://roole.org) files
- [Wizcorp/component-less](https://github.com/Wizcorp/component-less) - [LESS](https://github.com/less/less.js) transpiler for component-builder
- [segmentio/component-markdown](https://github.com/segmentio/component-markdown) - Compile Markdown templates and make them available as Javascript strings.

Given that you can use those to work with language/template you want.
For example to use coffeescript you will need to install plugin in your root folder.
```sh
$ npm install component-coffee
```
Now after recompilation all your coffee files will be used as javascript. That also means you can use both js and coffee files in same repo.

```coffeescript
#filename: add.coffee
module.exports = (a, b) =>
  a + b

```
```javascript
//filename: appstart.js
var add = require('./add');
add(2,2); //4
```
### And there is more

**Compy** is built ontop of grunt. Basically it is just [grunt](https://github.com/edjafarov/compy/blob/master/Gruntfile.js) setup. So no magic here. Though lots of stuff available:

* components support
* local `require`
* supporting coffeescript, sass, jade and other [plugins](#plugins)
* static server + livereload
* karma runner
* grunt extendable

May the [force](https://github.com/edjafarov/compy/) be with you!