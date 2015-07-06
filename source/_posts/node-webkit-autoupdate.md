title: node-webkit autoupdate
date: 2014-06-10 18:42:59
tags:
---
[Node-webkit](https://github.com/rogerwang/node-webkit) allows you to build cross platform desktop application with [node](https://nodejs.org) and JavaScript. Though building desktop application unlike pure online webapp means you got no control over the code after the app was installed. Thus releases are painful and bugs after the code was released are frustrating.

Right now desktop apps usually update themselves. [Node-webkit do not have](https://github.com/rogerwang/node-webkit/issues/233) this functionality out of the box. Which is reasonable because such functionality would hardly rely on specific implementation.

So I created [webkit-updater](https://github.com/edjafarov/updater). It relies on suggestion that your app will be packaged with [grunt-node-webkit-builder](https://www.npmjs.org/package/grunt-node-webkit-builder), compressed with [grunt-contrib-compress](https://github.com/gruntjs/grunt-contrib-compress) and with windows package unzip.exe will be packaged. Basically the example of packaging app you can find [here](https://github.com/edjafarov/updater/blob/master/gruntfile.js).

[webkit-updater](https://github.com/edjafarov/updater) is working/tested under [mac](http://screencast.com/t/OXyC5xoA), [win](http://screencast.com/t/MSTKqVS3) and [linux](http://screencast.com/t/Je2ptbHhP)(32/64).

###how does it work

It gives you [api](https://github.com/edjafarov/updater/blob/master/README.md#api) to:

1. Check the manifest for version.
2. If the version is different from local one download new package to temp.
3. Unpack the package to temp.
4. Run new version from temp and exit the process.
5. The new version from temp will copy itself to original folder.
6. The new version will run itself from original folder and exit the process.

you should build this logic by yourself though. As a reference you can use [example](https://github.com/edjafarov/updater/blob/master/app/index.html).

###what are the plans

There should be bugs, probably. Need to stabilize it and test it extensively in real world app.

It would also be great to have different rates of updates:
* update assets only without page reload (*.*.x)
* update assets with page reload (*.x.*)
* update assets and node-webkit engine - full cycle (x.*.*)

There is a [bug](https://github.com/rogerwang/node-webkit/wiki/The-solution-of-lacking-libudev.so.0) for newer versions of linux. Updater should resolve things like that. Also there should be some preInstall and postInstall scripts handling.

You are welcome to [use and commit](https://github.com/edjafarov/updater).


