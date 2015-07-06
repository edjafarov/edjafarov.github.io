title: 'proper node-webkit desktop notifications'
date: 2014-09-16 18:39:41
tags:
---
Desktop notifications plays an important role in success of desktop apps since is allowing to bring attention of a user when something is happenning.

So far in node-webkit we have several solutions for desktop notifications.

1) [HTML5 notifications](https://github.com/rogerwang/node-webkit/issues/27)

* **pros:** is a html5 standart, same as in web.
* **cons:** looks differently in differen OSes, low interaction capabilities, have bugs for node-webkit:)

2) [NW Desktop Notifications](https://github.com/robrighter/nw-desktop-notifications)

* **pros:** customizable, same in different OSes.
* **cons:** no interaction, animations are ugly, API is not a standart.

3) [node-notifier](https://github.com/mikaelbr/node-notifier)

* **pros:** you can do it from node, you can use standart OSes notification systems (win8 is coming soon!).
* **cons:** looks differently in differen OSes, low interaction.

All those solutions were not working for me. Thus I had to create another one. Based on same idea as NW Desktop Notifications but implemented a little bit better.

[node-webkit-desktop-notifications](https://github.com/edjafarov/node-webkit-desktop-notification) should become drop-in replacement for HTML5 notifications. So ideally you just use it instead of html5 Notification with some more API around. And if the code is executed in node-webkit context it does all kinds of rich notifications otherwise it degrades to html5.

Additionally to simple notifications you will get ability to build any complex interations inside your notification. Like change layout, buttons, textfields or gestures.

###how to build interactive notifications
The notification itself is a window. Thus it has it's own context, html and css/javascript. Rich interactions are built on events. You can emit events on the window object inside notification window and catch them on DesktopNotification instance inside your application. So you can build any kind of presentation and keep interaction inside your appcode.

### use it
To use the lib in your app you need to take 2 files:

```
src/desktopNotification.js
src/desktopNotification.html
```

You need to place them in same folder of your app. Load `desktopNotification.js` to your index.html to use the `DesktopNotification`

```javascript
var notif = new DesktopNotification('Hello', {body: 'World'});
notif.show();
```
check other ways to use `DesktopNotification` in [example](https://github.com/edjafarov/node-webkit-desktop-notification/blob/master/src/index.html).

### try live

* Fetch the [repo](https://github.com/edjafarov/node-webkit-desktop-notification)
* npm install
* npm start
* find an app for your OS in build/node-webkit-desktop-notification
* [play](http://screencast.com/t/bUxB6vNvW8BN)
