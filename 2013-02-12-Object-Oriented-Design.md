#Object-Oriented Design (OOD)

All of the following information has been distilled from Sandi Metz' [Practical Object-Oriented Design in Ruby](http://www.poodr.info/), and although the code in this post is based on the Ruby language, don't worry - the concepts are applicable for any object-oriented language.

I would highly recommend you read [Practical Object-Oriented Design in Ruby](http://www.poodr.info/) as the author goes into far more code detail and background information (as well as covering other subjects such as test-driven development and the process of writing efficient unit tests) which will help you understand the concepts better than I could in this single post. But hopefully the following distilled version should be a sufficient starting point for your journey into writing more flexible and maintainable code.

###What we'll cover:

- Objects
- Class Analysis
- Dependencies
- Flexible Interfaces
- Duck Typing
- Inheritance
- Inheritance vs Composition
- Further good rules of development from Sandi Metz
- Summary

##Summary

Here is a short summary for those of you who prefer to see a quick bullet-point list of items covered... 

- Decouple your code (*we discuss this in more detail below*)
- Describe your class to see if it does too much  
e.g. for each class write down a single line description and try to avoid the words 'and', 'or' from occuring
- Review each method thoroughly (you may find some methods don't belong in your class and deserve their own interface)
- Manage your dependencies:
    - Check the method arguments you're passing around
    - Use dependency injection (don't hard code class names)
    - Avoid direct references to complex data structures (transform your data into a more appropriate form)
    - Abide by the Single Responsibility Principle (SRP)
    - Review comments to ensure their purpose and usefulness
        - Your commented code could be better handled by moving into a separate method with a descriptive name
- Write more flexible interfaces:
    - Object-Oriented code is more about the 'messages' sent between objects than the objects themselves
    - Think about the messages you want to send and create objects/interfaces to handle them
    - Ask for what you *want* and don't include *how* to do what you want
    - Ensure messages you send (e.g. method calls you make) don't rely on knowledge of the object that implements the method
    - Reduce your object's context (i.e. how much it knows about other objects). Dependency Injection can help here
- Trust your objects (e.g. Duck Typing design principles)
- If using the inheritance pattern:
    - Abstract your shared functionality into the base class
    - Make sure sub classes inherit only what they need
    - Avoid calling `super` as it's a code smell


##Objects

The best description I have ever read regarding good Object-Oriented design goes like this... 

> Object-Oriented Design is about the messages that get sent between objects and not the objects themselves.

This single line quote perfectly captures the intention behind good Object-Oriented design. 

It seems our focus on objects has been wrong. We should be thinking primarily about the messages we want to send. This way we build up classes based on good clean interfaces and so our subsequent objects are clearer and more direct in their message handling.

## Class Analysis

We want our classes to be as decoupled as possible. The benefit of this is to allow changes to occur over time with little to no side-effects. If your classes have too many dependencies, which are likely too tightly coupled to the class, then any design/code changes in the future could potentially have a negative knock-on effect on the rest of your code.

To ensure a class only contains the behaviour it needs try describing your class in one sentence. If you find you have to use the word "and" within your description then the class appears to have more than one responsibility (this is a bad thing - your classes should be small and focused on a single responsibility). If you find you have used the word "or" to describe your class then you not only have more than one responsibility but the responsibilities aren't even related. That again would be an indication of a code smell.

Another way to analyse your classes is to ask each method within the class a question, and to see if any of the answers sound out of place.

e.g. "Please Mr. `ClassName` what is your `method_name`?"

This sounds strange and maybe a bit childish, but it's surprising what methods suddenly appear to no longer fit within the responsibilities of the class being interrogated.

For example, look at the following class which is based on a part of a bicycle (specifically gears)... 

    class Gear
        attr_reader :chainring, :cog, :rim, :tire
        
        def initialize (chainring, cog, rim, tire)
          @chainring = chainring
          @cog       = cog
          @rim       = rim
          @tire      = tire
        end

        def ratio
          chainring / cog.to_f
        end

        def gear_inches
            # tire goes around rim twice for diameter
            ratio * (rim + (tire * 2))
        end
    end

...now start to ask each of its methods a question (remember that `attr_reader` generates a getter method and so those need to be queried as well)... 

- "Please Mr. `Gear` what is your `ratio`?" **- seems fine**
- "Please Mr. `Gear` what is your `gear_inches`?" **- seems fine also**
- "Please Mr. `Gear` what is your `tire`?" **- hmm? notice this doesn't sound like it quite fits the purpose of a 'Gears' class**

You can tell that the `tire` method doesn't fit in with a class which handles bicycle gears information and would be better suited to be placed in its own class. A simple querying of the methods has pointed us in the direction of a potential code smell.

One other potential code smell worth avoiding is the direct referencing of class attributes/properties. You should only access them via a getter method to ensure good separation of data access. For example...

    class Gear
        attr_reader :chainring, :cog
        
        def initialize (chainring, cog)
          @chainring = chainring
          @cog       = cog
        end

        def ratio
          @chainring / @cog.to_f # bad
          chainring / cog.to_f   # good
        end
    end

##Dependencies

Dependencies can be many things, for example: external class references or arguments passed to methods.

Below are some rules to help you spot a dependency and how to better manage them... 

- ###Direct References

    Avoid 'direct references'. These are things like drilling down into a complex array structure to grab some data to work with. You may know the data structure now, but that's not to say it won't change in the future. But also, linking to a complicated data structure is confusing to other users because it obscures what the data really is and what it is meant to represent. 

    So in the following example we are directly accessing `item[0]` and `item[1]` from a multi-dimensional array... 
    
        #BAD
        class MyClass
            attr_reader :data
            
            def initialize(data)
                @data = data
            end

            def do_something
                data.each do |item| 
                    puts item[0]
                    puts item[1]
                    puts '---'
                end
            end
        end

        obj = MyClass.new([[10, 25],[3, 9],[41, 7]])
        obj.do_something
    
    ...but the order of the items may not always be what you think they are and the direct access is not very descriptive of what the data is that you're accessing. 
    
    Instead you should 'transform' your data structure into a simpler and easier to understand structure (a good way to do this in Ruby is by using `Struct` which is perfect for creating basic data holding classes - which is what we want to do here)...

        #GOOD
        class MyClass
            attr_reader :new_data
            
            def initialize(data)
                @new_data = transform(data)
            end

            def do_something
                new_data.each do |item| 
                    # now we are able to reference easily understandable 
                    # property names (rather than item[0], item[1])
                    puts item.coord_x
                    puts item.coord_y
                    puts '---'
                end
            end

            Transform = Struct.new(:coord_x, :coord_y)
            
            def transform(data)
                data.collect { |item| Transform.new(item[0], item[1]) }
            end
        end

        obj = MyClass.new([[10, 25],[3, 9],[41, 7]])
        obj.do_something

- ###Single Responsibility Principle
    
    You should refactor your methods so they do one thing (also known as the 'Single Responsibility Principle'). One reason to do this is so that your methods become easier to test, and also their new found simplicity can provide a greater clarity that can highlight whether other methods within the class should even be there.

    So for example, you may have a complex algorithm contained within a single method of your class and because of its complexity you may miss the fact that some of the algorithm should actually have been handled by a separate class altogether.
    
    Following the Single Responsibility Principle will result in smaller (and greater number of) small sized methods. This result will encourage greater code reuse from yourself (as well as other users of your code) and will also make your methods easier to test and to move around into different classes.

- ###Remove comments

    If a piece of code needs a comment then chances are you need to extract that code into a separate method. The name of the method should serve the same purpose as the comment once did. This isn't always the case, but as part of your analysis you should reconsider any comments to ensure they are helpful or just noise.

- ###Do not tightly couple your code

    The best way to decouple your code is to manage your dependencies. 

    For example, if you look at a class that utilises another class for some additional functionality, that secondary class has become the dependency. Also, if you use that dependency in multiple places and the class was to change in some way then consider how many places your class potentially could break or need to be updated? 
    
    Now, a class referencing the name of another class isn't necessarily a major issue in itself (as the change of a class name can easily be rectified using a modern IDE find & replace feature), the bigger problem is from the lack of code reuse. Your method is tightly coupled to a specific class. 
    
    Some other things to look out for are:  
    - arguments passed to a method on the dependency class  
        - if the class name itself is hard coded then technically that is an area of concern as well because if the dependency class was renamed then your code which references the old name would need to be updated. 
    - even down to things like the order of the arguments could be considered a dependency.  

    Every dependency results in more brittle, tightly coupled code.

- ###Facades

    Do not let external dependencies permeate your code. 
    
    One way to prevent this is to wrap any dependencies in a method so you can implement a facade over the original interface allowing it to match your own API.
    
    For example, if your dependency had a method which required arguments to be sent in a specific order then you could wrap the call to the method in a facade which allowed the user of your class to pass in the arguments in another format. Your facade could then normalise the data before passing it over to the dependency's method.

- ###Dependency Directions

    Make sure you spend time considering the direction of your dependencies.

    When considering the direction of your dependencies (e.g. does class A rely more on class B, or vice versa) remember to think about the following 3 points...

    1. Some classes are more likely to change than others

    2. Concrete classes are more likely to change than abstract classes.  
    Imagine you have a class with a hard coded reference to another class. We could make the primary class more abstract by injecting the  dependency of the secondary class rather than having to reference it directly. This way the primary class is able to accept an object successfully as long as it implements the required method.

    3. A class with many dependants could result in widespread consequences.

###Summary of dependencies...

- Dependency management is core to creating future-proof applications. 

- Injecting dependencies creates loosely coupled objects that can be reused. 

- Isolating dependencies allows objects to adapt to unexpected changes. 

- Depending on abstractions decreases the likelihood of facing changes. 

- The key to managing dependencies is to control their direction. 

And to quote another... 

> "Depend on things that change less often than you do"

##Flexible Interfaces

Object-Oriented applications are made up of objects(classes) but are defined by the messages that pass between these objects. 

Our code must handle...

- What objects *know* (i.e. their responsibility)
- *Who* they know (i.e. their dependencies)
- *How* they talk to one another

...and this is done via our object's interfaces. 

Creating a flexible interface is essential to good Object-Oriented design. 

Each object should reveal as little about itself, and know as little about other objects as possible. 

There are two parts to our interfaces: a Public Interface and a Private Interface... 

###Public interface:

- Should reveal the primary responsibility 
- Is expected to be invoked by others
- Will be unlikely to change (so safe for other objects to depend on)
- Testable

###Private interface

- Should handle implementation details
- Not be accessible by other objects
- Can be changed at anytime without causing side effects for other objects
- Aren't even accessible by unit tests

A good way to start designing your interfaces is to draw sequence diagrams (one way to do this is to use UML). Just remember to focus on the messages needing to be sent between objects rather than focusing on the objects themselves.

When designing your interfaces you should ask yourself the question: "I need to send this message, who should respond to it?". 

It's important to understand that you don't send messages because you have objects. You have objects because you send messages. If you keep that in mind then you can ensure your objects only handle responsibilities relevant to them.

Avoid asking the question "What should this class do?" and make sure your interfaces are designed in such a way that they ask for what they want and don't try to tell another object what to do. 

For example, If you have a Mechanic class and you want the class to prepare a bike for you then don't call the Mechanic's individual methods: "clean_bike", "pump_tyres", "check_brakes" directly. Instead you know the message you want to send (in this case you want to have a bike prepared for you) so create an interface that supports that message requirement. Do this by creating a method on the Mechanic class called "prepare_bike" and send your message to that method. This way if the Mechanic class changes its implementation then the object that calls the "prepare_bike" method doesn't have to change as well. 

###Reducing Context

The things an object knows about other objects make up its 'context'. 

An object may have a single responsibility but it expects a context. For example the object expects an object to respond to a specific method call. 

For an object to become more reusable and more easily testable it must reduce its contexts. If there is a context then to reuse the object you need to bring along the context every time (this makes code reuse and testing harder). 

Dependency injection reduces the context. For example if you had an object which called a generic method and passed 'self' as an argument then the receiving object could handle the request (and because the first object was injected as a dependency) then the second object could call the first object when it's finished with the request. 

###Summary of Interfaces

Your interface defines your application and determines its future. 

Object-Oriented applications are defined by messages that pass between objects. This is handled via public interfaces. Ask for what you want and don't include any 'hows' as part of the request which would be telling the receiving object how to behave rather than it handling the how itself. 

##Duck Typing

Duck Typing is the process of making code more flexible and less tightly coupled by taking into consideration that if an object "sounds like a duck, and quacks like a duck" then it stands to reason the object must indeed be (or act like) a duck.

The principle idea behind Duck Typing is to trust your objects (e.g. do not worry about the class of an object, only that the object implement the expected interface - and trust that it does). Sounds scary/dangerous but it will help you to avoid writing code that tightly couples to your dependencies. 

Cleaning up a long and ugly switch/case statement which checks the class of an object to determine what action to take is one area where Duck Typing can help. The problem with this example is that the switch statement technique is fragile and likely to break when new class types need to be added. 

Instead, for each object create a generically named method (same name for each object) which handles the appropriate internal/specific details relevant for that object. This way you can just call the method (and even pass the calling object as a reference `self` in case the receiving object needs further information to carry out its work) and thus make your code more reusable by not tightly coupling a specific method to multiple object calls. 

Also, any where you see the use of `is_a?` or `responds_to?` then that is an indication of a potential code smell because the principle issue identical to the switch statement. 

##Inheritance

###Abstract your base class

When inheriting from another class it is essential that the parent class is as abstract as possible. For example, it only holds enough code that is relevant for all its sub classes. A sub class should never inherit redundant data or methods. If it does then your parent class isn't abstract enough. 

###Default values

Be careful when using default values. If your base class has a common property which is different for each sub class, but is required within each sub class (hence sticking it in the base class) then you won't want to give it a value inside the base class. Your base class should take in the value via the constructor and if the value isn't provided you should set the default using a method like so...

`xxx = args[:xxx] || default_xxx_value`

In this example `default_xxx_value` should be a method the sub class implements which provides the specific value. The reason we have written it like this is so that the sub class has better control over setting the default value. 

So far so good. But if a new user doesn't read the documentation (which states they must implement `default_xxx_value` within their sub class) then they will get an error thrown. In the above example it may be best to raise your own descriptive error by implementing the `default_xxx_value` method as an abstract method within the base class like so...

    def default_xxx_value
        raise NotImplementedError, "#{self.class} cannot respond to: "
    end

Note: the above custom error message raised will display automatically the name of the method (`default_xxx_value`) at the end of the message when it is displayed to the user (hence we don't need to manually include it). 

###Super

Beware calls to `super` via a sub class, as this is a code smell. 

Why? Because it declares that the sub class knows the implementation of the base class. It says "I know what you do, I know what to send to you and I know what returned value to expect". The sub class has more context than it should do and we've created a dependency. 

In this example the sub class knows too much about the base class and so it is tightly coupled to it. If a new developer joins the project and creates a sub class, but doesn't call `super` at the appropriate time, then they would likely have a silent failure (or at least one that could be difficult to debug).

To resolve the concern of using `super` we can use a 'hook message' which effectively allows the sub class to stay decoupled from the base class. The sub class needs to be able to trust that the base class will do the right thing (which in this case is to call a method). 

The way the hook method works is you remove the constructor from the sub class and move the sub class specific constructor code into a separate method of the sub class called `post_initialise` (or whatever you want to call it). The base class' own constructor will be run when a new instance of the sub class is created but now the base class constructor will be updated so it calls the `post_initialise` method at the end of its constructor (this means the base class needs to implement an empty `post_initialise` method which the sub class then overwrites). 

Now the sub class doesn't know about the base class other than it inherits from it and the interface contract between them states the base class will at some point call `post_initialise` whenever it's ready to do so and the sub class takes over from there. It's now clear that the sub class is just a specialised version of the base class.

This works with other methods not just the constructor. The base class could have...

    def spares
        { tyre_size:tyre_size }.merge(local_spares)
    end

...the sub class can then implement its own `local_spares` method which returns a hash. So when a user creates a new instance of the sub class and calls the `spares` method, the base class handles the functionality. The sub class can insert its own specialised data without knowing how the base class works (other than the interface design dictates the sub class should implement a method called `local_spares`). 

###Modules

Not all objects are specialised versions of another object and so they shouldn't always inherit functionality via the inheritance pattern. 

Objects have a tendency to play a role, and some objects play a similar role to other objects.

Instead of the inheritance pattern we can use modules as 'mixins' which will let these objects (those with similar roles) share behaviour. 

Modules are placed inside the same lookup path as methods acquired through inheritance, so the principles for developing modules should follow those of writing classes: use the Template Method Pattern (e.g. have stub methods which the sub classes overwrite). 

For example, any object which includes the module needs to provide their own specialisation of a hook method implemented in the module. This means an object which includes the module doesn't have to create a dependency by calling `super`, thus avoiding needing to know anything about the included module (other than its implied interface contract) and ultimately reducing its context (i.e. what it knows - a dumb object is a reusable object). 

###Composition

The composition pattern is effectively the same as using modules (where you copy in functionality rather than inheriting it - thus creating a '*has-a*' relationship rather than a '*is-a*' relationship). 

But composition from a design perspective is more about the resulting 'whole', than the subsequent parts that make up the whole. 

##Inheritance vs Composition

Inheritance is the more appropriate solution if your design dictates that the objects have a well defined concrete class of functionality and that most of that base functionality is the same for all other objects. With inheritance you would write only small amounts of new code to extend the base functionality so the extending objects become more specialised.

> Inheritance is specialisation  
*Bertrand Meyer, Touch of Class*

If on the other hand your objects are all different and the design of the objects dictates there could be multiple reusable 'parts', then composition would be the better solution.

> Use composition when the behaviour is more than the sum of it's parts  
*Grady Booch, Object-Oriented Analysis and Design*

##Further good rules of development from Sandi Metz

1. Your class can be no longer than a hundred lines of code.
2. Your methods can be no longer than five lines of code
3. You can pass no more than four parameters (do not make it one big hash either).
4. In your controller, you can only instantiate one object, to do whatever it is that needs to be done.
5. Your view can only know about one instance variable.
6. Rules are meant to be broken if by breaking them you produce better code. [ ...where "better code" is validated by explaining why you want to break the rule to someone else. ]

##Summary

So just to quickly recap on some of the important points covered... 

- Decouple your code
- Describe your class to see if it does too much  
e.g. for each class write down a single line description and try to avoid the words 'and', 'or' from occuring
- Review each method thoroughly (you may find some methods don't belong in your class and deserve their own interface)
- Manage your dependencies:
    - Check the method arguments you're passing around
    - Use dependency injection (don't hard code class names)
    - Avoid direct references to complex data structures (transform your data into a more appropriate form)
    - Abide by the Single Responsibility Principle (SRP)
    - Review comments to ensure their purpose and usefulness
        - Your commented coded could be better handled by moving into a separate method with a descriptive name
- Write more flexible interfaces:
    - Object-Oriented code is more about the 'messages' sent between objects than the objects themselves
    - Think about the messages you want to send and create objects/interfaces to handle them
    - Ask for what you *want* and don't include *how* to do what you want
    - Ensure messages you send (e.g. method calls you make) don't rely on knowledge of the object that implements the method
    - Reduce your object's context (i.e. how much it knows about other objects). Dependency Injection can help here
- Trust your objects (e.g. Duck Typing design principles)
- If using the inheritance pattern:
    - Abstract your shared functionality into the base class
    - Make sure sub classes inherit only what they need
    - Avoid calling `super` as it's a code smell