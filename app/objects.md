---
layout: page
title: Classes
---
Classes is a term used in JavaScript to describe a logical component which holds it's own state (instance properties) and has methods on it (prototype methods). Style should closely resemble the style described by Duglas Crockford's [JavaScript: The Good Parts](). The following is examples of best practise styles for classes and how to approach the Factory Pattern introduced in the Titanium API patterns.

All classes should be encapsulated usually through an IIFE or a class in CoffeeScript.

All classes should be camel cased and start with a capital letter. `ExampleClass`

All methods and properties should be camel cased and start with a lower cased letter. `exampleCamelCase`

All private variables should be snake cased. `example_snake_case`

Private (meant for internal use) instance properties and methods should start with an underscore. `_examplePrivatePropertyOrMethod`

    //= CoffeeScript
    class MyClass
      foo: ->

    //= JavaScript
    var MyClass = (function() {

      function MyClass() {}

      MyClass.prototype.foo = function() {};

      return MyClass;

    })();

## Configuration

If an class requires configuration it should be broken up by required and optional. Required parameters should be part of the initial constructor whereas optional ones should be passed in through a config object.

    //= CoffeeScript
    class Foo
      constructor: (@required, {@optional}) ->

    //= JavaScript
    var Foo = (function() {

      function Foo(required, options) {
        this.required = required;
        this.optional = options.optional;
      }

      return Foo;

    })();

Classes which allow optional configuration that can change *after* instantiation should provide chain-able methods which are prefixed with the word `with`.

    //= CoffeeScript
    class Foo
      constructor: ({@foobar}) ->
      withFoobar: (@foobar) -> this

    //= JavaScript
    var Foo = (function() {

      function Foo(options) {
        this.foobar = options.foobar;
      }

      Foo.prototype.withFoobar = function(foobar) {
        this.foobar = foobar;
        return this;
      };

      return Foo;

    })();

## Callbacks

When sending information back up the chain any object that needs to react beyond the usual setters and getters _could_ use callbacks. When registering callbacks

    //= CoffeeScript
    class Foo
      onEvent: (@on_event_cb) ->

## Events


## Views

A good example of an class is a view. It should be responsible for creating the components it would use. Passing information back up should be done through either callbacks or events.

All view classes should define a `root` instance property which is a Titanium object

All view classes should define a `create` **static** method.

If the root property is a window you should provide `open` and `close` methods

All view classes should set all references to Titanium objects to null when the root component is done (closed). You most likely want to attach to the "close" event.

If you have multiple events to deal with you should place them in a `_registerEvents` method.

    //= CoffeeScript
    class MyView
      constructor: ->
        # create model
        # create components like windows, views, buttons
        @root = Ti.UI.createWindow();
        @_registerEvents()
      open: ->
        # custom methods you define
        @root.open()
      close: -> @root.close()
      _registerEvents: ->
        @root.addEventListener "close", => @root = null
      @create: -> new MyView

    //= JavaScript
    var MyView = (function() {

      function MyView() {
        this.root = Ti.UI.createWindow();
        this._registerEvents();
      }

      MyView.prototype.open = function() {
        this.root.open();
      };

      MyView.prototype.close = function() {
        this.root.close();
      };

      MyView.prototype._registerEvents = function() {
        var _this = this;
        this.root.addEventListener("close", function() {
          _this.root = null;
        });
      };

      MyView.create = function() {
        return new MyView();
      };

      return MyView;

    })();

## Parasitic Views

Parasitic views are designed to define a Titanium UI component and nothing else. They are nothing more then a function which returns a Titanium object. If you need to attach your own methods or state then you should *not* use this pattern instead use the above View pattern.

The rule of thumb is if you return a titanium object you should use this factory pattern with the prefix `create`. Otherwise you should use the View pattern above.

These methods start with the work `create` to conform to the Titanium Factory pattern standard.

    //= CoffeeScript
    createMyButton = (title) ->
      Ti.UI.createButton {title}

    //= JavaScript
    function createMyButton(title) {
      return Ti.UI.createButton({
        title: title
      });
    }
