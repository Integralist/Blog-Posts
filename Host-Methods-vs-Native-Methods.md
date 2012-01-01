Host Methods vs Native Methods
==============================

This was intended as a short (I failed) and overly simplified (I likely succeeded) post about Host methods and Native methods:

* What they are?
* How to detect them?
* When is it OK to modify them?

What they are?
--------------

Native methods are built-in functions provided by the ECMAScript core specification. So things like Object methods (e.g. `Object.create`), Array methods (e.g. `Array#forEach`) etc.

Host methods are functions provided by the host environment (most of the time when working in web development the host environment will be the user’s web browser). So things like the DOM API and the Events object are host objects/methods (e.g. `attachEvent` is a host method and `addEventListener` is a host method)

How to detect them?
-------------------

Detecting Native methods is relatively straight forward. The real problem comes when you need to determine whether the object/method you’re detecting actually works the way the specification dictates it should work. So just checking it is available to use isn’t good enough.

Detecting host methods is similar but a lot more problematic, because the ECMAScript specification states that the host environment can implement certain methods however they like and so there is no guarantee that your checks for certain host methods (which may work today) will work in future.

We’ll give an example of each so you can get an idea of what I mean…

To detect a Native method such as Array#forEach you should be able to do the following:

```js
if (!Array.prototype.forEach) { 
	/* polyfill for missing forEach method */ 
}
```

Note: polyfill is a term that Remy Sharp coined which means ‘a shim that mimics a future API’ (see: [http://remysharp.com/2010/10/08/what-is-a-polyfill/](http://remysharp.com/2010/10/08/what-is-a-polyfill/))

But the issue you could encounter in this example is if you’re inheriting a project from another developer and they have already extended the Native Array object with a forEach method and their polyfill version of the missing forEach function doesn’t work how the specification has dictated it should then you could find your code errors at hard to debug stages because of the difference in implementation where you’re passing parameters into a polyfill’ed method and that method hasn’t been implemented properly so the extra parameters either throw an error or (potentially worse) silently fail.

This is where you either ‘suck it and see’ (which is a bad idea, but not always unavoidable), or you attempt genuine ‘feature detection’ which means (in this example) you create a test Array and test the forEach method works how you expect it to.

The downsides to this approach (although it is the most robust and future-proof way of writing your code) is that all these checks are a performance penalty. If you’re sure the method you’re checking for is going to work how you expect it to then should you waste time/effort writing additional checks/tests to ensure the method works how the specification dictates? What happens if you do the full feature detection and discover the method doesn’t work how you expect it? You’ll still then need to implement some kind of fallback or lose the functionality that relies on that method.

These are important decisions that need to be made and ones that are outside the realms of this post I’m afraid (simply because there are no easy answers).

Now, detecting Host methods is actually worse because they can be implemented in any fashion the host environment chooses.

So far it has been *noted* that checking the typeof result for a Host method will normally result in either function, object, unknown or (recently I found…) string, so if you get one of these back as a result then it’s a good chance the host object you’re checking for is available to use, but as you should be able to tell by now, this is a flawed process… fun heh!

Again, this isn’t a reliable assumption to make, because in a future/new host environment they might have a typeof result that is none of the above. Literally you could check the typeof for a method and its result could be *spacecraft* - there are no rules as far as the Host environment is concerned!

But for testing a host method exists, the following function has become the de-facto standard:

```js
/*
 * Feature Testing a Host Method
 * Because a callable host object can legitimately have any typeof result then it can't be relied upon.
 *
 * @notes:
 * The reason for the && !!object[property] is because in ECMAScript version 3, 
 * a null object has typeof result 'object' (which is considered a bug).
 * In future versions (ECMAScript 5+) the typeof result will be 'null' (as it should be).
 * 
 * @reference: http://michaux.ca/articles/feature-detection-state-of-the-art-browser-scripting
 */

function isHostMethod(object, property) {
	var type = typeof object[property];

	return type == 'function' || // For Safari 3 typeof result being 'function' instead of 'object'
		   (type == 'object' && !!object[property]) || // Protect against ES3 'null' typeof result being 'object'
		   type == 'unknown' || // For IE < 9 when Microsoft used ActiveX objects for Native Functions
		   type == 'string'; // typeof for 'document.body[outerHTML]' results in 'string'
}
```

So lets take a quick re-cap of what’s going on here:

* `function`:
	For most browsers the `typeof` operator will result with ‘function’ when passed a callable host object

* `object’ && !!object[property`:
	In older versions of IE (less than 9) it implements some of its host objects not as Native functions but as ActiveX objects (admittedly this is deep browser implementation talk and normally you don’t need to know this stuff, but in this instance it’s important to understand what the heck is going on with IE + this will be very important when it comes to the following bullet-point and the ‘unknown’ result). 

	Because some objects are actually ActiveX objects it means that those callable host objects have a typeof result of ‘unknown’ and those which are non-ActiveX objects have a typeof result of ‘object’ (so that’s why we’re checking for ‘object’). ON TOP OF THAT… remember that in the ECMAScript 3 specification it allows a Host environment typeof call to result in anything the host wants, so you’ll see in some browsers that the ‘null’ value has a typeof result of ‘object’ (which means a false positive for our conditional checks)! So that’s why in the second half of the check we’re converting the object[property] into a boolean (using the double exclamation mark operators !!) as this will convert a potential null value into false and so the check will fail, unless of course the value is ‘object’ which if so it will be converted to true and pass.

* `unknown`:
	So, here comes that IE ActiveX object nonsense again. Any callable objects that are ActiveX objects will have a typeof result of ‘unknown’ - hence why we have this check here

* `string`:
	Lastly, this is something I added myself as I discovered that checking for the outerHTML method that it actually was returning ‘string’ instead of ‘object’ or ‘function’ - which just goes to show how flawed host object detection is
	
When is it OK to modify them?
-----------------------------

Modifying built-in Native objects isn’t as dangerous as host objects (as already noted by Kangax [http://perfectionkills.com/extending-built-in-native-objects-evil-or-not/](http://perfectionkills.com/extending-built-in-native-objects-evil-or-not/)) but care needs to be taken to ensure the augmented object works as the spec dictates (something that isn’t possible all the time, for example like with `Object.create`).

As far as host objects are concerned, never ever ever ever modify or augment them, just too dangerous.