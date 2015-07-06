title: why building another app compiler?
date: 2013-09-28 15:10:19
tags:
---
When you are frontend developer and start doing node npm completely spoils you. Because unlike we used to it provides single and predictable way of adding/using 3rd party libs and snippets to your code.

Frontend is more complex in many ways. It is more fragmented since there are html and css additionally to javascript and our code is running in different combinations of vm's and platforms.

Commonly used way of adding 3rd libs is a `/vendor` folder that holds bunch of unminified (if you're lucky enough) files that were downloaded by someone ages ago. Maybe you will find comments inside that will give you an idea of what version of library is used, maybe not. Also what-load-first dependency management is completely your pain. You might have a master file with all the scripts loaded in 'right' order <img width="15" height="15" src="http://skypeblogs.files.wordpress.com/2011/07/0186-facepalm-30px.png?w=30&h=30">.

[**Bower**](https://github.com/bower/bower) is doing great job adding more metadata to packages fixing some problemts. But *Bower is just a package manager (c)* and it doesnt load scripts. So again, you need to do additional job defining what-load-when relations.

Even if you will use [**require.js**](https://github.com/jrburke/requirejs) you need to configure 3rd libraries. Besides **requirejs** adds it's own complexities into code. For example: do you know the difference between `require` and `define` functions? And frankly why do you need to know difference! You need something that just works.

So at the end of a day we need package manager that will deliver libs into our app, require functionality that will handle script dependencies and builder that will wire all the thing together and give back 3 files: index.html, app.js and app.css

### and [compy](https://github.com/edjafarov/compy) can do it
[**Componentjs**](https://github.com/component/component) does most of work already. **Compy** just wraps the concept in one solid solution.

#### **component package manager**
**Componentjs** was obvious choice. Unlike **npm** or **bower** component is really strict on what files considered a source. Which is not that important for server but really important for frontend.

#### **local require**
**Componentjs** gives local require out of the box. Component's require is synchronous. Your files are wrapped in scope and concatenated in one file. Your dependencies are already loaded then you require them. Thus you don't break javascript and require becomes plain simple and clear.

#### **builder**
Builder takes responsibility to compile out three files: app.js, app.css and index.html. app.js is built of js dependencies (components), precompiled templates and js source files. app.css is just concatenated css files and index.html generated automatically to eat js/css and run "main" file. Builder have bunch of plugins that allow to precompile sources. So you can use coffeescript, scss, jade, whatever. And just technically because we avoid on read/write cycle comparing to plain grunt it's faster than grunt. And you can use mix of technologies require coffeescript from js and vice versa.