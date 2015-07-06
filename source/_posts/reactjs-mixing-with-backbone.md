title: Reactjs mixing with Backbone
date: 2013-11-24 07:06:04
tags:
---
Reactjs is a javascript librarby for building user interfaces opensourced by facebook just recently.

Not long ago I felt that as a developer I have more or less two best options to build an app. Whether do it in #angular or #backbone. Now I feel that #react is taking best of angular, do it better and allows to use best parts of backbone.

I hate Backbone Views and I hate $scope of angular, especially when it comes to directive scope and all &-@-= stuff. Transclusion and scope is a double hell and I am not talking about digesting and performance yet.

React has really small API and it does one thing, but does it really well. It abstracts DOM for you and optimizes the rendering part. So each time you need react to reflect state changes in the DOM it renders lightweight DOM in javascript and applies only diff to the real DOM. In that way rendering becomes really cheap unlike in angular. And that allows us to build apps with diffenrent patterns in mind.

And here are some tips I got from several weeks of playing around #Reactjs

### React is just V
React needs other stuff like routes and models. I am taking them from Backbone.

### Models are state
By default React have single state `this.state`. Which is not usually best solution. It appears that cleaner way is to have multiple states. Where `this.state` is not persisting state and backbone models are.

In the React's example you can find [BackboneMixin](https://github.com/facebook/react/blob/master/examples/todomvc-backbone/js/app.js#L148) but it has some flaws. Following one is better since it does proper cleanup.

```javascript
var  ModelMixin = {
  componentDidMount: function() {
    // Whenever there may be a change in the Backbone data, trigger a reconcile.
    this.getBackboneModels().forEach(this.injectModel, this);
  },
  componentWillUnmount: function() {
    // Ensure that we clean up any dangling references when the component is
    // destroyed.
    this.__syncedModels.forEach(function(model) {
      model.off(null, model.__updater, this);
    }, this);
  },
  injectModel: function(model){
    if(!this.__syncedModels) this.__syncedModels = [];
    if(!~this.__syncedModels.indexOf(model)){
      var updater = this.forceUpdate.bind(this, null);
      model.__updater = updater;
      model.on('add change remove', updater, this);
      this.__syncedModels.push(model);
    }
  }
}
```
In that way you can use same models in several nested components.

```
  <rootComponent user="new UserModel({id: id})">
    <contactComponent user = {this.props.user}/>
    <userpicComponent user = {this.props.user}/>
  </rootComponent>
```

### 2 way binding
It's kinda logical to have 2 way binding with those Backbone models. [LinkedState](https://github.com/facebook/react/tree/master/src/addons/link) plugin is working only for state thus here is BindMixin wich does basically the same as LinkedState but for Backbone models.

```javascript
var  BindMixin = {
  bindTo: function(model, key){
    return {
      value: model.get(key),
      requestChange: function(value){
          model.set(key, value);
      }.bind(this)
    }
  }
}
```

This mixin adds bindTo method that binds control with model property as simple as `this.bindTo(user, 'name')`:


```javascript
var Hello = React.createClass({
  mixins:[ModelMixin, BindMixin],
  getBackboneModels: function(){
    return [this.props.instance]
  },
  render: function() {
    var model = this.props.instance;
    return <div>
        <div>Hello {model.get('initial')}</div>
        <input type="text" valueLink={this.bindTo(model, 'initial')}/>
      </div>
  }
});
```

Here is working example: http://jsfiddle.net/djkojb/qZf48/24/