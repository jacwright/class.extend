# Class.extend

Simplifies extending classes and provides static inheritance. Classes that need to be extendable should extend Class
which will in turn give them the `extend` static function for their subclasses to use. In addition to a prototype,
mixins may be added as well.

One problem with many `Class.extend(prototype)` implementations is that you lose the name of the function, making it
harder during debug time to know what objects you're looking at in the logs since the function name for a class is used
in the display. By requiring the constructor to be defined outside of `extend` you get to keep the name and still
benefit from static inheritance and the increased code readability `extend` provides.

### Features

* Keeps the class name by requiring the constructor to be defined as a named function
* Makes prototype members non-enumerable so only instance data is enumerable, easier for debugging as well
* Static inheritance, methods defined statically on the super class are copied to the sub class
* Inline getters/setters supported in the prototype definitions
* Multiple inheritance (sort of, not the real deal), or mixins are supported

### Examples

Here are some examples of feature usage.

#### Simple extension

```
var Class = require('class.extend');

function SuperClass(name) {
  this.name = name || '';
}

Class.extend(SuperClass, {
  output: function() {
    console.log(this.name);
  }
});

function MyClass(name, type) {
  SuperClass.call(this, name);
  this.type = type || '';
}

SuperClass.extend(MyClass, {
  output: function() {
    console.log(this.name, this.type);
  }
});
```

#### Mixins, getters/setters

```
// ...

function MyClass() {
  SuperClass.call(this);
  EventEmitter.call(this);
  this._bar = 0;
}

SuperClass.extend(MyClass, EventEmitter, {
  foo: function() {
    this._bar++;
  },
  get bar() {
    return this._bar;
  }
});
```

Statics

```
// ...

Class.extend(SuperClass, {
  foo: function() {
    // do something
  }
});

// Singleton support
SuperClass.getInstance = function() {
  if (!this._instance || this._instance.constructor !== this) {
    this._instance = new this();
  }
  return this._instance;
};

SuperClass.extend(MyClass);

var myclass = MyClass.getInstance();
```

```
var Class = require('class.extend');

function SuperClass() {

}

Class.extend(SuperClass, {
  statics: {
    type: 'superclass'
  },

  get type() {
    return this.constructor.type;
  }
});

function MyClass() {

}

SuperClass.extend(MyClass, {
  statics: {
    type: 'myclass'
  }
});

console.log(MyClass.type); // myclass
```
