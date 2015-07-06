title: semantic styling
date: 2014-07-22 08:27:08
tags:
---

Driving from [#OdessaJs](http://odessajs.org.ua/) with [@listochkin](https://twitter.com/listochkin) we had discussed the future of web development and our perceptions of what makes sense. It was priceless talk. And lot of things were formulated and validated for me. One of concepts popped out is the "semantic styling".

Not long ago there was a hype around semantic layout. Which is a great concept around giving more sense to layout. But it looks like nobody noticed the next big revolution in styles. The twitter did it. Twitter [bootstrap](http://getbootstrap.com/) has formulated **smallest footprint of layout + styles** for common design patterns.

So right now everybody agrees that button can be `<button>` as well as `<a>` and it should look the same and dropdown will look like:

```html
<div class="dropdown">
  <button class="btn btn-default dropdown-toggle">Dropdown</button>
  <ul class="dropdown-menu">
    <li><a href="#1">Action</a></li>
    <li><a href="#2">Another action</a></li>
  </ul>
</div>
```

and nobody tries to invent something else. Bootstrap made a standart. What we can call "semantic styles".

What is also great about twitter bootstrap that it is built with less thus you can use parts of it. The variables in bootstrap are mainly colors and you can override them. Thus your css (less) code consists of two parts: the **logic** (the relations between html elements/classes, that describes the footprint) and the **presentation** (that makes the design individualistic - variables that change your colors and paddings)

With TWBS we have a vocabulary of semantic styles that represent commonly used design patterns and we can adjust them to branded design style overriding less variables.

Unlike bootstrap does though I think this vocabulary should be managed in a different way.

Bootstrap has just a lot of files in twitter bootstrap repo. Package managers on other hand  provide better development experience, more abilities to handle dependencies and structure the code. I prefer [components](https://github.com/component/component) style over [bower](http://bower.io/) thus I would like and I believe it is possible to handle vocabulary of semantic styles in the same manner. But instead of CommonJs `require("modulename")` being able to (**CommonLess** - why not?) `@import "buttons"` without putting complete path to the component.

So from a development perspective we will just need to install some "less terms" as a components (example: `$ component install twbs/forms twbs/buttons twbs/grid`). And then inside my main.less file:

```less
//importing vocabulary
@import "forms"
@import "buttons"
@import "grid"

//overriding default variables to adjust to branded design.
$brand-success: #AAAAAA;
$brand-default: #BBBBBB;
```

Almost everything is ready. **CommonLess** thing is not there yet. So far we just need to put complete relative path for less/sass components we use.


