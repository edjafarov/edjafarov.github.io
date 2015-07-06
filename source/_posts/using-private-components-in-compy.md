title: using private components in compy
date: 2013-10-21 17:09:02
tags:
---
There are core limitations in component that makes hard to use private git repositories directly, unless you use github. Component FAQ [proposes](https://github.com/component/component/wiki/F.A.Q#how-do-i-use-private-github-repositories) to use `remotes` property and any web server that uses the same urls as Github.

_package.json_
```json
{
  ...
  "compy":{
    ...
    "remotes":["https://user:pass@raw.github.com"]
  }
}
```

But there is a better way to manage private components with any git server you like.
### using git submodules to manage private components
Component supports `local` dependencies. That means it can serve components from any local folder you put in as local param in config.

_package.json_
```json
{
  ...
  "compy":{
    ...
    "paths":["local"],
    "local":["component1","component2"]
  }
}
```
So if you want to use local dependencies, you should put git [submodules](http://git-scm.com/book/en/Git-Tools-Submodules) with those private components in the folder.

[Compy](https://github.com/edjafarov/compy) will serve them as usual components and you will manage them with git-cli.

### adding component to folder

You can add component to `local` folder like this:
```
cd local;
submodule add git://github.com/chneukirchen/rack.git
```
