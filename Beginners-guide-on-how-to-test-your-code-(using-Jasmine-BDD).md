Beginners guide on how to test your code (using Jasmine BDD)
============================================================

* Introduction
* Start how you mean to go on
* The ‘write test first’ process
* Other aspects of TDD/BDD
* Using Jasmine
* An example
* Review of example
* Conclusion

Introduction
------------

Any programmer worth a damn will tell you that you need to test your code. The way this is done is by using ‘unit-tests’.

The principle is that you write a test for a piece of your applications code and see if it passes.

Within a test you write a number of ‘assertions’ (which means you’re expecting certain values to be returned at that point in a certain format or type) and if the code fails to produce the relevant value the assertion will fail and thus the test itself will fail.

There are different methodologies for testing your code, the two most famous are Test-Driven Development (TDD) and Behaviour-Driven Development (BDD). Both are very similar and differ in perspective/terminology more than anything but I won’t get into that here because you’ll just end up getting confused.

Start how you mean to go on
---------------------------

Something else you normally hear from the TDD crowd is that you *should* write your tests first! That’s probably going to sound a bit alien to you because how can you write tests for code that doesn’t yet exist? Why would anyone do that?

Well, the reasoning behind it is quite logical when you think about it. Normally you’ll just start writing some code with no idea of how it should work, you just start hacking away and as you progress you go back and refactor sections and make it cleaner/more efficient and hopefully at the end you don’t need to change your code in any meaningful way to appease your users who may be using your code (e.g. if you’re building the next big JavaScript framework then your API is what your users will be relying on and if you discover there is a bug in your code and it means you have to go back and change the API because of it then that causes big concerns for your users).

So the idea of writing tests first is that you’re thinking about your API from the beginning. You’ll think about what the perfect API is for your code to produce and you’ll ensure you write code that fits that API (you won’t realise halfway through coding “ah crap, this doesn’t work as well as I had hoped it would, I’m going to have to change this”).

Although we’re not worrying about “writing tests first” in this article, I mention it so you are at least aware of the process.

The ‘write test first’ process
------------------------------

For those interested it goes a little like this:

* Write a unit-test
* Run the test (it will obviously fail because there is no code for it to run against!)
* Write the smallest amount of code for the unit-test to pass (we’re talking quick and dirty code here)
* Run the test again and watch it pass
* Once the test passes, go back and refactor your code so it’s how it should be (i.e. clean/efficient)
* Run the test again and watch it pass

Other aspects of TDD/BDD
------------------------

Here are a few other aspects of unit-testing worth mentioning before we get stuck into some examples: methods such as ‘setUp’ and ‘tearDown’ (which run before and after each test) are useful (for example…) because it means you can prepare each test to run from a fresh set-up.  This probably doesn’t make a lot of sense at the moment so I’ll demonstrate this later in our example code below, but trust me, when you’re testing your code it’s useful before each test (or after each test) to reset your environment.

There are also more complicated aspects such as mocks, stubs and spies which are useful when you start getting deep into unit-testing application code where ‘state’ becomes relevant (e.g. using some of these features makes testing code in the deepest parts of your application a lot easier).

So with all this in mind, I would highly recommend you go and read a book titled ‘Test-Driven JavaScript Development’ by Christian Johansen ([@cjno](http://twitter.com/cjno)) which covers all these topics in great detail.

Using Jasmine
-------------

Now, we’re going to be using a BDD library called Jasmine to help run our tests. You can download it from here: [http://pivotal.github.com/jasmine/](http://pivotal.github.com/jasmine/)

The set-up is as follows:

* Create an HTML page
* In this page insert the provided css file ‘jasmine.css’ + the two provided js files ‘jasmine.js’, ‘jasmine-html.js’
* Then include your own JavaScript code ‘my-cool-library.js’
* Then include your own ‘my-tests.js’
* After that have an inline script which executes the Jasmine test runner…

```js
jasmine.getEnv().addReporter(new jasmine.TrivialReporter());
jasmine.getEnv().execute();
```

Within your own ‘my-tests.js’ file is where you’ll write your unit-tests.

Different unit-testing libraries have different API’s. Jasmine’s API is as follows…

```js
describe('test suite name', function(){
	// assertions for your code to try and pass
	// if any assertions fail then this entire suite fails
});
```

An example
----------

So now imagine your ‘my-cool-library.js’ consisted of an object whose API let the user add/remove or check for CSS classes on an element. Lets say the API was as follows…

```js
var header = document.getElementById('my-header');
css.add(header, 'newclass') // --> adds the class 'newclass' to the specified element 'header'
css.has(header, 'newclass') // --> returns a boolean value (true/false) depending on whether the class 'newclass' is found
css.remove(header, 'newclass') // --> removes the class 'newclass' from the specified element 'header'
css.classes(header) // --> returns an Array of classes found on this element
```

Your test suite for this code could look something like (don’t worry, we’ll discuss after)…

```js
// Test Suite
describe('CSS tests', function() {
	
	var header = document.getElementById('my-header');
	
	beforeEach(function() {
		// Reset the className before each spec is run
		header.className = 'myclassa myclassb';
	});
	
	// Spec
	it('should return an Array of class names', function() {
		expect(css.classes(header)).toEqual(['myclassa', 'myclassb']);
		expect(css.classes(header).length).toBe(2);
	});
	
	// Spec
	it('should add class to element', function() {
		css.add(header, 'newclass');
		expect(header.className).toBe('myclassa myclassb newclass');
	});
	
	// Spec
	it('should return a boolean for whether the class is on the given element', function() {
		expect(css.has(header, 'myclassa')).toBeTruthy();
		expect(css.has(header, 'newclass')).toBeFalsy(); // although in the previous spec we added 'newclass' to the element, in this spec this assertion should return false because the beforeEach method above has reset the class list back to 'myclassa myclassb'
	});
	
	// Spec
	it('should remove class from element', function() {
		css.remove(header, 'myclassb');
		expect(header.className).toBe('myclassa');
	});
	
});
```

…so a few things you’ll notice:

* We’ve grouped all our tests related to the CSS part of our code using Jasmine’s `describe('test suite name', function(){ /* tests */ });`
* We’re using a setUp method (which Jasmine calls ‘beforeEach’) to run some code to reset the class names before each test run (so we start from a clean slate for each test) - there is also a corresponding tearDown method which Jasmine calls ‘afterEach’ (see documentation)
* Each test is represented by `it('expectation of this test', function(){ /* assertions */ });`
* The assertions are handled by Jasmine’s `expect(expressions).matcher`

The assertions method ‘expect’ takes an expression (e.g. some code to execute) and then the result of that code is passed to the ‘matcher’. Our tests consisted of a few matchers such as:

* `toEqual()`
* `toBe()`
* `toBeTruthy()`
* `toBeFalsey()`

Jasmine has a few more matchers which you can read more about in the documentation: [https://github.com/pivotal/jasmine/wiki/Matchers](https://github.com/pivotal/jasmine/wiki/Matchers)

You can even create your own matchers…

```js
// Add our two new matchers. One to check if an object is an Array and the other to check if the result is a Number
// You create these within the beforeEach method which is executed before each test is run
beforeEach(function() {
	this.addMatchers({
		toBeArray: function(expected) {
			return Object.prototype.toString.call(this.actual); === '[object Array]' ? true : false;
		}
	});
	
	this.addMatchers({
		toBeNumber: function(expected) {
			return /\d+/.test(this.actual);
		}
	});
});
```

Review of example
-----------------

We’ll look at one of the example tests and explain what’s happening so you get an idea of how Jasmine works, then from there you should be at least good to go in getting up and running and playing around with it yourself.

I’ve set-up an example of the code found in this post on Github: [https://github.com/Integralist/Unit-testing-with-Jasmine-BDD](https://github.com/Integralist/Unit-testing-with-Jasmine-BDD) (which should also help you getting started)

So lets look at one of the tests…

```js
it('should add class to element', function() {
	css.add(header, 'newclass');
	expect(header.className).toBe('myclassa myclassb newclass');
});
```

…as you can see the test starts by describing what is expected of it. In this case it should add a class to the specified element.

Within the execution of the test itself we can see that we’re executing the css.add() method and telling it to add the class ‘newclass’ to the element ‘header’ (which as you can see in the full code above was an element with an id of ‘my-header’ stored in the variable ‘header’).

After that code has executed we’re expecting the header element’s className value to be ‘myclassa myclassb newclass’.

If we were to run the test-runner.html file we should see all tests pass (this is demonstrated by the green bar and the message of ‘4 specs, 0 failure’).

To see the test suite fail then just amend one of the tests slightly to cause it to fail. For example in the above example we looked at change it to: toBe(‘x myclassa myclassb newclass’) and this will cause the test to fail because obviously the list of class names on the header element is not going to include the class name ‘x’ at the start.

When you run the test-runner.html’ file now (remember that now one of the tests will fail) you’ll see that instead of a nice clean green bar to highlight success you see a red bar and a drill down into the issue. If you do as I suggested above to cause the test to fail you’ll notice now Jasmine highlights exactly what the problem is to you…

[http://cl.ly/1z3b1g3U1z0e2r2U2c2H](See screenshot image)

```
4 specs, 1 failure in 0.014s
> CSS tests (this is the name of the test suite that failed - as you could have multiple suites running this helps narrow it down)
>> should add class to element (this tells you the exact test that fails)
>>> Expected 'myclassa myclassb newclass' to be 'x myclassa myclassb newclass'. (this tells you what the result actually was followed by what the test was expecting - so you can see where the result went wrong)
>>>> (after this you get a stack trace of what was executed so you can see specifics of where the error occurred)
```

Conclusion
----------

Hopefully this has given you a taster for how easy it is to get started writing unit-tests for your code (even if you don’t take the “write tests first” approach).

There are many good unit-testing libraries available today and they each have their pros/cons as far as the API is concerned and the features they offer.

I personally prefer Jasmine because I love the simplicity of the terminology and API and the flexibility I have to include my own matchers.

There are also libraries that focus on the more on specific parts of the testing process such as ‘mocks’, ‘stubs’ and ‘spies’ (see Sinon.js [http://sinonjs.org/](http://sinonjs.org/)).