JavaScript Inheritance
======================

[Class based inheritance](http://en.wikipedia.org/wiki/Class_(object-oriented_programming\)) doesn’t exist in JavaScript (*note: ECMAScript had plans to implement in ES6 basic Class syntax, but as of April 2012 this seems to be in doubt again*), but you can replicate its syntax by using functions as Constructors.

The basic premise to have code that appears more `Class` like is this: JavaScript functions created using the `new` keyword work in a similar way to what Class based languages refer to as ‘Constructors’. When a function uses the `new` keyword the Function object is given a `prototype` property which points (initially) to an empty object. This empty object can then have methods and properties added to it which will be available to all other objects that point their own prototype link to it.

A basic example is as follows…

```js
var Person = function (settings) {
   if (!settings) { settings = {}; } // code defensively

   // Instance properties (any new instances of the Person class will have these properties)
   this.name = settings.name || 'no name given';
   this.age = settings.age || 'no age given';

   // Instance method (any new instances of the Person class will have this method)
   this.getName = function() {
      alert(this.name);
   };
};

// Create a new instance of the Person Class
var integralist = new Person({ name:'Mark', age:7 });

// Add a method to this instance of the Person Class only (no other instances created will have this method)
integralist.getAge = function() {
   alert(this.age);
}

// Test the integralist instance has access to both methods
integralist.getName();
integralist.getAge();

// Create another instance of the Person Class
var user = new Person();

// Notice the user has access to a 'getName' method but not a 'getAge' method
user.getName();

// I know this will error so I'm wrapping it in a try statement
try {
   user.getAge();
} catch(err) {
   alert(err); // Uncaught TypeError: Object [object Object] has no method 'getAge'
}

// Add a method to the Person Class' prototype chain (all instances of the Person Class will now get this method - even those already defined)
Person.prototype.getNameAndAge = function() {
   alert('Hi, my name is ' + this.name + ', and I\'m ' + this.age + ' years old.');
}

// Test this new method is accessible to all instances of the Person Class
integralist.getNameAndAge();
user.getNameAndAge();
```

Personally I would suggest you learn how JavaScript implements its own form of inheritance (prototypal inheritance) which is more efficient and nicer to work with than the use of functions as Constructors.

The Mozilla Developer Network has written up a short article on the differences between Class-based and Prototype-based languages which you can find here: [https://developer.mozilla.org/en/Core_JavaScript_1.5_Guide/Details_of_the_Object_Model#Class-Based_vs._Prototype-Based_Languages](https://developer.mozilla.org/en/Core_JavaScript_1.5_Guide/Details_of_the_Object_Model#Class-Based_vs._Prototype-Based_Languages)