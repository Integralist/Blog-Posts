#Message passing in Object-Oriented Code

##What we'll cover

- Introduction
- Quick example
- The Proxy Design Pattern
- How Ruby handles method calls
- Implementing `method_missing`
- Conclusion

##Introduction

In my [previous post](http://integralist.co.uk/Object-Oriented-Design.html) I quoted the following description of object-oriented design…

> Object-Oriented Design is about the messages that get sent between objects and not the objects themselves.

The reason I felt this quote was important for good code design was because it helped focus our attention on improving our object's interfaces.

Since then I've been reading through [Design Patterns in Ruby](http://designpatternsinruby.com) by Russ Olsen, and in the chapter on the Proxy design pattern he reiterates thinking about objects more from the perspective of 'messages' and how that can help improve the Proxy pattern implementation.

His comments really nailed home for me the design benefits of thinking more about 'messages' being passed to objects, and it's that point which I want to elaborate on below.

##Quick example

Imagine the following code example: `account.deposit(50)`

When thinking about a statically typed language, object methods are generally considered to be more 'baked' into the objects, in the sense that running the above code example suggests you are 'calling' the `deposit` method found on the `account` object. 

But in a dynamically typed language (such as Ruby) this doesn't make a lot of sense because the `account` object might not actually contain a method called `deposit` (statically typed languages are compiled and so we can be assured that if we call a method on an object, it will be there - otherwise the program would fail to compile) so talking about 'calling' a method on an object is not as accurate as describing it like so: 

> "we're sending a deposit message to an account object"

##The Proxy Design Pattern

The Proxy design pattern is where we place an object between the user and the actual object the user wishes to interact with.

There are a few different types of proxy object:

- Protection proxies
- Remote proxies
- Virtual proxies

The reason 'message passing' came up in the Proxy design pattern (specifically when developing a 'virtual proxy' - which is where we create a proxy object to prevent an expensive object instantiation operation from happening until the user 'actually' interacts with one of the methods on the real object) was because the author wanted to avoid the situation where we would need to implement a stub method for each method found on the real object. 

This isn't necessarily an issue for all types of objects. But if you look at built-in objects such as the `Array` object, that has approximately 118 (maybe more) methods! So for us to implement a proxy for that object we'd theorectically need to implement 118 stub methods, each of which would simply forward on the request to the corresponding method on the real object to handle. That would not only be tedious but an inefficient way to implement our proxy object.

##How Ruby handles method calls

In Ruby if you pass a message (e.g. call a method) to an object and that method doesn't exist, then Ruby will try to find another method on that object: `method_missing`. 

If `method_missing` doesn't exist then Ruby will try to lookup the method on the parent object, and will keep moving up the inheritance chain until it reaches the core `Object` object (which does implement `method_missing`) and which simply raises a `NoMethodError` exeception.

##Implementing `method_missing`

If you implement `method_missing` on your proxy object then you can pass on the message to the real object more efficiently than stubbing the method.

So instead of this…

	class AccountProxy
		def initialize(real_object)
			@real_object = real_object
		end
		
		def deposit(amount)
			@real_object.deposit(amount)
		end
		
		… ad infinitum … 
	end

	account = Account.new
	proxy = AccountProxy.new(account)
	proxy.deposit(50)

…we should really take advantage of the dynamic nature of the Ruby language to avoid having to manually write out these methods by hand, like so… 

	class AccountProxy
		def initialize(real_account)
			@subject = real_account
		end
		def method_missing(name, *args)
			@subject.send(name, *args)
		end
	end

You can see from the above example that we're using the [send](http://ruby-doc.org/core-2.0/Object.html#method-i-send) method to pass the message (i.e. the method invoked by the user on the proxy object) directly to the real object.

##Conclusion

As you can see, focusing on passing messages not only helps inform us of better interfaces when designing our application but also makes us more efficient by utilising features unique to dynamically typed languages.