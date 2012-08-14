#JavaScript 101

* Introduction
* What is JavaScript
* Terminology
* Global Object
* Variables
* Types
* Objects
* Arrays
* Conditional Statements
* Coercion
* Functions
* Code Reuse (inheritance)
* Conclusion

##Introduction

I've written this *very* brief guide to JavaScript just as an aid for people new to the language and who need a basic starting point to see what the syntax looks like and to get a feeling for some of its features.

This article's main purpose is to give readers new to the language a glimpse of the JavaScript environment and to hopefully spur them onto further reading/learning. 

##What is JavaScript

JavaScript is a *scripting language* - this means it is not *compiled* (like traditional software languages like C or C++) but is executed 'line by line' via its host environment at run time (the host environment can be: Web Browser, Server, Command Line, Desktop).

##Terminology

Term                | Example
------------------- | -------------
expression          | An expression is a command that the JavaScript engine can *evaluate* to produce a value
statement           | A statement is a command that can be executed (statements are terminated with a semicolon)
identifier          | A name (e.g. variable name, function name, labels for loops).
function declaration| `function myFunction(){ /* code */ }`
function expression | `var myFunction = function(){ /* code */ };`
primitive           | `undefined`, `null`, `boolean`, `string` and `number`
operator            | `+`, `-`, `!`, `++`, `===`, `&&`, `typeof`

###Expressions

An example of an 'expression' would be `1+1`. 

Looks straight forward enough, and it should be. As mentioned above, an expression is simply *something* that can be interpreted as a value. So `1+1` (when evaluated by the JavaScript engine) results in an number with a value of two. 

Now the expression `1+1` is pretty useless in a JavaScript program because we're not doing anything with it. For us to more effectively use this expression we ideally want to store it somewhere so we can reference it later and that's where 'variables' come in (see later on this article).

###Statements

Examples of JavaScript 'statements': 

* `if (condition) { /* code */ }`
* `while (condition) { /* code */ }`
* `return`, `break`, `throw` are single command statements

##Global Object

When the JavaScript interpreter starts up it creates a Global object and any properties/methods added to the Global object are available to the entire JavaScript program.

The global object is regular object (as per the Object section seen later in this article).

The global object is different depending on the context. In a web browser environment the global object is the `window` object. So if you open a browser console (e.g. Firefox's Firebug JavaScript Console, or Safari/Google Chrome's Console Tool) and enter the command `window` you'll see all the properties/methods available on that object - things like `window.location` will be one option that is listed (this references the location bar API of the web browser).

One quote you'll hear a lot in JavaScript is:

> Don't pollute the global environment

…and what this means is try to avoid creating global properties and methods. The less 'globals' you create then the less likely your code will conflict with another piece of code written by someone else that may be included in the same page.

To avoid declaring global properties/methods there are certain 'patterns' that have been designed to work around this issue, such as the IIFE pattern…

```js
/*
 * this pattern is referred to as an IIFE (immediately invoked function expression)
 * the function is immediately executed and all code within it is scoped to the function
 * when executed we pass through 'this' which refers to the global object
 * we accept 'this' into the function as an argument called 'global'
 */
var app = (function (global) {

    // private data (private as in: it can't be accessed from other code outside this function)
    // note: don't EVER name your functions like this!
    function will_be_made_public_but_cant_be_accessed_directly(){
        console.log('Private');
    }
    
    return {
        // public API (private code we've chosen to make public)
        do_something: will_be_made_public_but_cant_be_accessed_directly
    };

}(this));

app.will_be_made_public_but_cant_be_accessed_directly() // TypeError: Object #<Object> has no method 'will_be_made_public_but_cant_be_accessed_directly'

app.do_something(); // => 'Private'
```

Another pattern is to use AMD (Asynchronous Module Definition) which is a module based pattern (see: [Beginners guide to AMD and RequireJs](https://github.com/Integralist/Blog-Posts/blob/master/Beginners-guide-to-AMD-and-RequireJs.md))


##Variables

Variables hold values/data. 

You declare a variable like so: `var my_var = 123;` - in this example we've declared a variable and assigned the value `123` to it.

You must always declare a variable. An undeclared variable looks like this: `my_var = 123;` - notice the lack of the `var` keyword. 

Undeclared variables are a bad practice because they cause confusion as to whether the variable should be 'global' or not.

Here is a break down of the different variable scenarios:

* Variable is declared within a function:  
variable becomes scoped to that function (i.e. it isn't accessible outside of that function). 
* Variable is declared at the top level of the script file (e.g. not inside of a function):  
variable becomes a global variable (i.e. is available any where within the JavaScript program).
* Variable is undeclared (e.g. doesn't matter where in the program the undeclared variable was created):  
variable becomes a global variable (i.e. is available any where within the JavaScript program).

As you can see, missing a `var` declaration will mean the JavaScript engine will make that undeclared variable a 'global' variable (e.g. it is assigned to the Global object).

To see why this is a problem first imagine you have a web page you're working on and in which you have included a 3rd party JavaScript file (i.e. a script written by another developer). Now imagine the content of that 3rd party script is as follows:

```js
// 3rd party script written by another developer...

var my_name = 'Mark';
```

…in this instance, the 3rd party script hasn't wrapped the line `var my_name = 'Mark';` inside of an IIFE (see above pattern) so the variable `my_name` will be created as global property (e.g. it is available any where within the JavaScript program as well as explicitly via `window.my_name`).

Now you don't want to overwrite the variable `my_name` because the 3rd party script that defined it is relying on it being a String with a value of `Mark` - if you change the value then it could cause the 3rd party code to break.

So now imagine you're going to add your own code to the page. You write the following…

```js
function do_something(){
    my_name = 'Bob';
}
```

…this may cause unintended side effects on the 3rd party code. Why? Because you coincidentally defined a variable by the same name of `my_name` and although you likely intended it to be scoped to the function `do_something` you've accidentally forgotten the `var` keyword and so your variable is undeclared, so your variable has been assigned to the Global object, thus overwriting the `my_name` variable that is already available on the Global object (set by the 3rd party code).

To fix this you would simply make sure you properly declared your variable like so…

```js
function do_something(){
    var my_name = 'Bob';
}
```

So now, even though the variable name is the same, the variable is 'scoped' to the `do_something` function.

In today's world you'll be very hard pushed to find a library that sets anything more than one global variable - but it's an issue to be aware of in case you inherit a code base from a less educated developer.

Global properties aren't completely avoidable. A library will generally set one global which will act as a 'namespace' for their entire app. For example, jQuery sets two globals `jQuery` and `$` and all their methods are available through those two globals so unless you're trying to use `jQuery` or `$` as your own namespace then you shouldn't notice any conflicts. 

Most companies name their global namespace after their company: `var mycompany_app = /* code */;`

##Types

JavaScript types are split into two groups: `primitive` types and `reference` types. 

`Numbers`, `Booleans`, `Strings`, `Null` and `Undefined` types are primitive. 

`Objects`, `Arrays`, and `Functions` are reference types. 

It's important to know the different types because at some point in your JavaScript code you'll want to pass around values and objects and you might notice some issues with doing so. For example, you'll notice that JavaScript passes `Objects`/`Arrays`/`Functions` by reference, and passes primitives such as `Booleans`/`Strings`/`Numbers`/`Null`/`Undefined` by value.

What this means is if you have an Array and want to copy it so you can make changes to the copy (e.g. you want to leave the original as it is) then you might think to do something like this…

```js
var my_arr = ['a', 'b', 'c'];
var new_arr = my_arr;

new_arr.push('d');
```

…you might think that `new_arr` will be `["a", "b", "c", "d"]` and `my_arr` would still be equal to `["a", "b", "c"]` but you'd be wrong because Arrays are passed by 'reference' and so your change: `new_arr.push('d');` is effectively doing this: `my_arr.push('d');`, hence `my_arr` now has the value of `["a", "b", "c", "d"]` and so does `new_arr` because it really is just referencing (i.e. pointing to) `my_arr`.

So be careful.

##Objects

To create a new `Object` use the syntax: `{}`

For example:

```js
var obj = {
	name: 'Mark',
	location: 'London, England'
};
```

A variable added to an Object is known as a 'property'.  
A function added to an Object is known as a 'method'  

…this naming convention originates from traditional Object-Oriented languages.

So for example:

```js
var obj = {
	/* Property */
		name: 'Mark',
	/* Property */
		location: 'London, England',
	/* Method */
		get_details: function(){
			return this.name + ' is from ' + this.location; // => "Mark is from London, England"
		}
};
```

###(Nearly)Everything in JavaScript is an Object

One thing to be clear on is that nearly everything in JavaScript is an object (e.g. Functions, Arrays, Numbers, Strings etc). They all inherit their properties/attributes from the top level Object, and this is made possible in JavaScript via what's called the 'prototype chain'. 

We won't go into the 'prototype' too deeply now but the following section will touch on how it works briefly.

###Prototype

Every Object you create in JavaScript has a hidden object tied to it. This hidden object is called the 'prototype' for that object, and what this means is that your object you've created will inherit properties from the prototype.

All 'objects' that you create (e.g. `var obj = {};`) have the same prototype object they point to/reference - which is the top level `Object` in JavaScript. We call this `Object.prototype`, and this top level `Object` itself has no prototype (it's the only object that has no prototype because nothing precedes it).

All built-in Constructors (e.g. `Array.prototype`, `Date.prototype`) inherit from the `Object.prototype` and this linked set of objects is known as the 'prototype chain' and is how 'inheritance' works (see below for a short mention of inheritance and other forms of code reuse).

###Accessing properties/methods

To access properties/methods you can use either the dot notation or the bracket notation. The difference is in whether the property/method name is known at the type of execution or not.

Using the previous example code, if your program was written by you then to access the 'name' property you would simply use: `obj.name` - but if your application accepted input from the user (where by you asked them what property/method they wanted to access) you obviously don't know before hand what the user is going to choose. In this instance you would use the bracket notation: 

```js
var user_input = document.getElementById('my_form_input');
console.log(obj[user_input]);
```

The other time you'll need to use bracket notation is if you want to access a property that has special characters:

```js
var obj = { 
	'my property': 123 // YOU SHOULD NEVER NEED TO CREATE A PROPERTY NAME LIKE THIS
};

console.log(obj['my property']);
```

So the majority of the time you'll just use the dot notation, but depending on the dynamic aspect of your program you may need to use bracket notation every once in a while.

###Class Attribute

Every object has a *class* attribute which provides information about the object's type. 

Sounds pretty useful, but unfortunately neither of the current specifications (ES3 or ES5) allow you to access this attribute!?

But there is a trick to accessing an object's *class* attribute and that is to call the `toString()` method on the top level `Object.prototype` but using it on the relevant object you want to get the class of. The following example demonstrates the most common use case of accessing the *class* attribute: trying to work out if an object is actually an `Array`…

```js
var arr = ['a','b','c'];

console.log(typeof arr); // => "object" - well that's not right, it should return the type as 'Array'! (note: this is a known JavaScript bug)

console.log(Object.prototype.toString.call(arr)); // => "[object Array]" - that's more like it
```

…what we've done here is used a method available to all `Function`'s called `.call()`. What this method does is let you call another function (i.e. borrow a function) but use the calling context as the `this` value.

That previous sentence probably didn't make a lot of sense because we've not covered `this` or anything to do with contexts/execution environments etc - but try and stick with it for a moment and understand that what we've done is called the `Object.prototype`'s `toString` method but we've called it as if it was our Array that had executed `toString`.

The way this trick works actually requires a deep level understanding of how JavaScript handles it's conversion of data types: something I definitely wont go into here).

##Arrays

Arrays are like a simplified Object.

An Object is effectively a mapping of names (identifiers) to values… 

```js
var obj = {
    name: value,
    name: value,
    name: value
};
```

…an Array is the same with the exception that the 'name' identifiers are automatically incremented numerical values. So an Array like this…

```js
var arr = ['a', 'b', 'c'];
```

…would effectively be similar to the following object…

```js
var obj = {
    0: 'a',
    1: 'b',
    2: 'c'
};
```

Note: the above object with numerical keys requires you to use the bracket notation to access the identifiers (e.g. `obj[0]`) as `obj.0` would result in a `SyntaxError: Unexpected number`.

###Methods

Arrays come with many methods (functions) that let you manipulate and filter the data contained within the Array.

Some methods change the data in the Array (*mutators*).

Some methods return a new Array with the data changed (*accessors* - not the best/most descriptive name really,  as it suggests these methods just 'access' data when that's not always the case).

Some methods loop through the data (*iterators*).

Below are some examples of each…

Mutators | Accessors   | Iterators
-------- | ----------- | ---------
[pop](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/pop)      | [concat](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/concat)      | [filter](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/filter)
[push](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/push)     | [join](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/join)        | [forEach](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/forEach)
[reverse](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/reverse)  | [slice](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/slice)       | [every](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/every)
[shift](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/shift)    | [indexOf](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/indexOf)    | [map](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/map)
[sort](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/sort)     | [lastIndexOf](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/lastIndexOf) | [some](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/some)
[splice](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/splice)   |             | [reduce](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/Reduce)
[unshift](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/unshift)  |             | [reduceRight](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/ReduceRight)

##Conditional Statements

Conditional statements are 'control logic'. This means that depending on the result of a specified condition the corresponding branch of logic will be executed.

There are a few different conditional statements such as:

* `if`
* `switch`
* `ternary`

The simplest way to understand them is to see the syntax.

###If Statement

```js
if (condition) {

    // if 'condition' evaluated to true then run this code
    
} else if (other_condition) {
    
    // if 'other_condition' evaluated to true then run this code
    
} else {
    
    // other wise we'll run this code as a fallback
    
}
```

So a basic example would be something like this…

```js
var can_drink = false;
var age = 18;

if (age >= 18) {
    can_drink = true;
}
```

###Switch Statement

If you have lots of checks against the same variable/condition then you're better off using a `switch` statement… 

```js
switch (condition) {
    case x:
        // Execute this code block
        break;
    case y:
        // Execute this code block
        break;
    case z:
        // Execute this code block
        break;
    default:
        // Execute this code block
        break;
}
```

An example of using this statement would be… 

```js
var car = 'Porsche';

switch (car) {
    case 'Ford':
        // Execute this code block if car is 'Ford'
        break;
    case 'Porsche':
        // Execute this code block if car is 'Porsche'
        break;
    case 'BMW':
        // Execute this code block if car is 'BMW'
        break;
    default:
        // Execute this code block if car is none of the above
        break;
}
```

###Ternary Statement

For very short `if` statements you can also use a shortened syntax (a conditional operator sometimes referred to as a 'ternary' operator because of its three operands). The syntax is like so…

```js
condition ? true : false
```

…and can be used like so…

```js
var age = 18;
var can_drink = (age >= 18) ? true : false; // can_drink will equal a Boolean value of true

var x = 'abc';
var y = (x === 'abc') ? 'def' : 'xyz'; // y will equal a String value of 'def'
```

##Coercion

One of the areas of most confusion in JavaScript is its ability to coerce data 'types'.

The best thing to do is to just try and take advantage of JavaScript's ability to coerce objects into different types and use it to your advantage to make your code more succinct.

For example, with an `if` conditional statement JavaScript will try to coerce the expression into either `true` or `false` like so… 

```js
var element = document.getElementById('js-element');

if (element) {
	// if the DOM element is available then this conditional
	// will coerce `element` into a Boolean value of true 
	// and hence the condition will pass.
	
	// If the DOM element doesn't exist then the resulting value will be null
	// and null is converted into a Boolean value of false 
	// and hence the condition will not pass
}
```

In the above example JavaScript automatically converts the condition into a Boolean, but you can manually coerce a value into a Boolean by using the double negation operator `!!` like so...

```js
var obj = { age: 0, year: 1980 };

!!obj.age // => false (because zero coerces to false)
!!obj.year // => true (any number greater than zero coerces to true)
```

You can also use a single negation operator `!` to return the opposite Boolean value of an object (this is useful for saying 'if NOT x')…

```js
var bool = false;

if (!bool) {
    // this statement is executed if 'bool' is NOT true (i.e. if it's false)
}
```

###Some quick notes:

All Objects/Arrays coerce to true (even an empty Array).

An empty String wil coerce to false.

There is a lot to learn about JavaScript's coercion process, so for full details please see the following article: [http://webreflection.blogspot.co.uk/2010/10/javascript-coercion-demystified.html](http://webreflection.blogspot.co.uk/2010/10/javascript-coercion-demystified.html)

##Functions

Functions make it easier to create re-useable code. They are simply blocks of JavaScript code that can be called/executed multiple times.

An example of the function syntax is as follows…

```js
function identifier (parameters) {
    // statements
}
```

…which could be used like so…

```js
function add (a, b) {
    return a + b;
}

console.log(add(1, 1)); // => 2
```

###Parameters

Functions accept any number of 'parameters' (also known as 'arguments').

Within a function you can access all arguments via a special `arguments` property…

```js
function test (a, b, c) { 
    console.log(arguments); 
}

test(1, 2, 3) // => [1, 2, 3]
```

…you'll notice in the above example it looks like the `arguments` property is an Array but it's not. It's an 'array-like' object. So you can access the keys values much like an Array but you don't have access to the Array methods.

###Return values

If a function doesn't explicitly return a value (if you look at our `add` function example above you'll see we explicitly returned `a + b`) then the function will have a return value of `undefined`. Hence when you run certain code snippets in a browsers web console you'll normally see `undefined` appear directly underneath the code you've just written.

###Borrowing methods

Objects have two core methods made available to them: `call` and `apply`.

These two methods are the same (i.e. they do the same thing - which is to call a function *indirectly* while specifying a value for `this`) but with one small difference: `call` requires you pass through any arguments for the function as a comma separated list while `apply` requires you pass through any arguments for the function as an Array of values.

The purpose of these methods is to allow you to *borrow* a function/method from another object. The following code demonstrates its usage and how powerful it can be… 

```js
var obj1 = {
    name: 'Bob'
};

var obj2 = { 
    name: 'Mark', 
    speak: function(){ 
        return 'My name is ' + this.name;
    } 
}

obj2.speak(); // => 'My name is Mark'

obj2.speak.call(obj1); // => 'My name is Bob'
```

…you can see how this can make functions/methods very re-usable!

##Code Reuse (inheritance)

The final subject I want to briefly cover is 'code reuse'. In most object-oriented programming languages the main principle of code reuse is done via *inheritance* (this is where you have a base object that all other objects inherit properties/methods from - much like how JavaScript already works! i.e. all objects inherit properties/methods from the top level `Object.prototype`).

The way you implement inheritance is by taking advantage of the `prototype` chain in JavaScript.

There are multiple ways to use the prototype chain, one populate way is to try and emulate the 'Classical Inheritance' style syntax (i.e. most programming languages have a `Class` keyword that makes creating objects based off a blueprint Class very easy but JavaScript doesn't have the concept of Class'es - not yet any way). 

An example of how to emulate Class style syntax in JavaScript (currently) is by using functions as 'Constructors' like so… 

```js
var Person = function (settings) {
   // Instance properties (any new instances of the Person class will have these properties)
   this.name = settings.name || 'no name given';
   this.age = settings.age || 'no age given';

   // Instance method (any new instances of the Person class will have this method)
   this.getName = function() {
      return this.name;
   };
};

// Create a new instance of the Person Class
var bob = new Person({ name:'Bob', age:7 });

// Add a method to this instance of the Person Class only (no other instances created will have this method)
bob.getAge = function() {
   return this.age;
}

// Test the bob instance has access to both methods
bob.getName();
bob.getAge();

// Create another instance of the Person Class
var user = new Person({ name:'Mark' });

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
   return 'Hi, my name is ' + this.name + ', and I\'m ' + this.age + ' years old.';
}

// Test this new method is accessible to all instances of the Person Class
bob.getNameAndAge();
user.getNameAndAge();
```

###Composition

There is another code reuse pattern you can use instead of 'inheritance' called 'composition'. The way it works is instead of inheriting methods/properties from a blueprint/base object you instead borrow the methods using `call` or `apply` (as we've seen previously). 

An example of this is as follows…

```js
var person = {
		names: ['James', 'Neil', 'Russ', 'Stuart']
	};

var people = {
	names: ['Ash', 'Brad', 'Mark', 'Mike'],
	speak: function(which) {
		return 'Hi, my name is ' + this.names[which];
	}
};

// Composition not Inheritance
people.speak.call(person, 1); // => 'Hi, my name is Neil'
```

###Mixins

Another code reuse pattern is called a 'mixin' - which instead of using 'inheritance' we simply copy over the functions/properties we want to use.

The following example demonstrates how this works… 

```js
function extend(destination, source, overwrite) {
	var overwrite = overwrite || false;
	for (var i in source) {
		if (source.hasOwnProperty(i)) {
			// If we're not allowed to overwrite an existing property… 
			if (!overwrite) {
				// …then we check to see if the property is undefined… 
				if (destination[i] === undefined) {
					// …if it is then we know we can copy the property to the destination object
					destination[i] = source[i];
				}
			} else {
				destination[i] = source[i];
			}
		}
	}
	return destination; 
}

var person = {
		names: ['James', 'Neil', 'Russ', 'Stuart']
	};

var people = {
	names: ['Ash', 'Brad', 'Mark', 'Mike'],
	speak: function(which) {
		return 'Hi, my name is ' + this.names[which];
	}
};

extend(person, people); // copy properties from `people` to `person`

// `person` now has a `speak` method it didn't have originally 
person.speak(1); // => 'Hi, my name is Neil'
```

##Conclusion

OK, this has been a super brief/quick run through of different JavaScript concepts and language features.

This isn't supposed to be even remotely an exhaustive discussion - have you seen the size of 'JavaScript: The Definitive Guide' !?

Hopefully this has at least been enough to get you started and interested in learning more. If there are any issues or errors then please get in contact and let me know.