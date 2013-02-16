#Introduction to Ruby (a front-end developers perspective)

##Introduction

I'm not a Ruby developer. I'm a front-end developer who focuses on JavaScript, Regular Expressions, HTML and CSS (and that means I like to keep up to speed with each of the latest iterations of the languages, currently: ES5/6, HTML5, CSS3/4 etc). 

I've experience with PHP. Writing object-oriented code, creating databases and bespoke Content Managed Systems etc, but I've not touched that sort of stuff for a few years now as the front-end stack is where I've concentrated my attention (it's what I enjoy most).

So with that admission out in the open, this introduction to the Ruby programming language is likely to be full of holes you could drive a truck through (any Ruby enthusiasts reading this are welcome to comment on anything I have incorrect). So why do it? Because with the arrival of JavaScript pre-processors such as CoffeeScript and the new additions to both ES5 (and much more so in ES6), these new additions are pushing the JavaScript language to be more 'functional' and thus similar in syntax to the Ruby programming language. 

Looking at CoffeeScript I wasn't convinced. I like the JavaScript syntax, I think it's actually quite a beautiful language (when written correctly). But I decided that the future is just around the corner so why wait to see what all the fuss is about. In my mind, the best way to get involved with the new JavaScript/CoffeeScript style syntax is to investigate some of its inspirations which (seem to) stem from Ruby.

What I found though was that Ruby is itself an amazingly flexible and beautiful language. Very expressive and a joy to write and use (and to look at). 

Warning: this introduction assumes you have prior programming knowledge (doesn't matter really whether it's JavaScript or C# as long as you've had some programming experience). I'm not going to be explaining the basics of what are 'expressions', 'block statements', 'variables', 'arrays', 'functions/methods' etc.

So without further ado, here we go…

*Ps, [http://rubymonk.com/toc](http://rubymonk.com/) looks like a pretty good learning resource - I've only taken a quick peek at it so far but it looks interesting*

##Table of Contents

* Installing Ruby
* The Interactive Ruby Console
* How to execute Ruby scripts
* Comments
* Variables
* Magic Variables
* Constants
* Symbols
* Functions/Methods
* Blocks
* Lambdas/Procs
* Classes
* Loops
* Conditionals
* Strings
* Arrays
* Hashes
* Numbers (and how 'everything is an object' - similar to JavaScript)
* Conclusion

---

###Installing Ruby

OK, this was an absolute bitch to get set-up. Luckily for you, I've suffered so you don't have to (hopefully).

I'm running Mac OSX so your mileage may vary.

If you can get away with it, use the package manager homebrew to install: `brew install ruby`.

But homebrew didn't work for me. It said it installed, but I couldn't get it to switch from the default Ruby 1.8 the Mac has pre-installed. So the next step was to install what is called 'rvm' which lets you install and manage multiple versions of Ruby.

The process is as follows (open the Terminal and start typing):

1. `bash -s stable < <(curl -s https://raw.github.com/wayneeseguin/rvm/master/binscripts/rvm-installer)`

    After rvm is installed you'll see a message asking you to to run the following...


2. `source /Users/<username>/.rvm/scripts/rvm` 

    Remember to replace `<username>` with the name of your computer.

3. `rvm install 1.9.3 --with-gcc=clang`

    The `--with-gcc=clang` was suggested on Stack Overflow to fix issues Mac OSX Lion users were having trying to upgrade to the latest Ruby version 1.9.3
    
    This worked fine at home, but at work it failed so instead I executed…
    
    `rvm install ruby-1.9.3-p125`

4. `rvm use 1.9.3 --default`

    We're now telling rvm to use version 1.9.3 as the default version of Ruby
    
5. `ruby -v` - just check the version quickly to be sure

---

###The Interactive Ruby Console

Beginners are advised to use the interactive Ruby console to get to know the language. So to start it up, in your Terminal type: `irb` (and if you want to quit use: `Ctrl+D`).

From irb you can start typing out code and seeing what the results are.

For example to display something you would use `puts` (this is similar to `Reponse.Write` in ASP or `echo` in PHP, or even `document.write` - *never use that!* - in JavaScript) like so `puts "Hello World"`. There is also `print` but be warned that although it looks similar it's not the same as `puts`.

Take the following example…

    5.times {
        print "This gets written five times"
        sleep 2
    }

…which every two seconds displays the sentence "This gets written five times"…

`This gets written five timesThis gets written five timesThis gets written five timesThis gets written five timesThis gets written five times`

…and returns `=> 5`. Notice the sentence is printed on one line. If you were to change `print` to `puts` it would automatically generate a newline for you making it easier to read. 

As far as I understand it, the other more subtle difference is that `print` buffers the output, so rather than displaying the sentence every two seconds it waits until ten seconds (2 seconds * 5 times) before printing the sentence five times. Now, I tried this myself both via `irb` and via a standard Ruby script but `print` definitely was executing every two seconds, so this may only occur under certain environments - but it's still worth being aware of. See [this article](http://mattberther.com/2009/02/11/puts-vs-print-in-ruby) which is where I first heard about this subtle difference.

---

###How to execute Ruby scripts

Simply create a file with an extension of `.rb` and at the top of that file include the following line… 

`#!/usr/bin/env ruby`

…this tells the operating system how to handle the file (e.g. tell it to use the Ruby parser to execute the file).

Then within the Terminal application, `cd` to the directory where that file is located and execute the command `ruby name_of_file.rb`

---

###Comments

In Ruby you have single line comments `# this is a comment` and multi-line comments:

    =begin
        Lots and lots of
        code that I want
        to comment out all at once
    =end

With multi-line comments there must be no whitespace before the `=begin` and `=end` statements otherwise your script will throw an error.

---

###Variables

Variables do not have to be declared. So you can literally write `a = 123`.

You can assign multiple variables at once like so…

    x, y = 1, 2     # which is effectively the same as x = 1, y = 2
    a, b = b, a     # which switches the values of each variable
    x,y,z = [1,2,3] # assign each array item value to a variable, so x = 1, y = 2, z = 3

Variables in JavaScript typically come in two flavours: Global and Local. In JavaScript if you declare a variable with the `var` syntax then the variable is added as a property to the global object (which depending on the environment JavaScript is running in) most of the time will be the `window` object. If you declare a variable inside of a function in JavaScript using the `var` syntax then that variable is only available within that function (unless it's accessible via some priviledged object).

In Ruby, if you declare a variable inside a function with no prefix then it is only available within that function. If you prefix it with a dollar sign (e.g. `$my_var = 123`) then that declares it as a global variable and is accessible from any where in the program.

When using Classes, if you declare a variable within the class using an `@` prefix (e.g. `@my_var = 123`) then that variable is available to the object created by that class only (also known as an 'instance variable'). Where as a double `@@` (e.g. `@@my_var = 123`) is known as a 'class variable' which means it is available to all objects created by that particular class and any changes to this class variable is reflected in all objects created from that class. 

So for example, if I have a class called "MyClass" and inside of it I set `@@my_var` to equal 456 instead of 123 then every object created from "MyClass" will have `my_var` set to 456. Whereas if "MyClass" had `@my_var` set to 123 and I create a new object from that class and set `@my_var` to 456 - an 'instance variable' - then only that object would see the value of `@my_var` as 456, all other objects created from the class would still see the value as 123. It sounds tricky but it's not that bad really. Here is an example of what the above was trying to explain… 

    # Following classes 'MyClass' and 'MyClass2' demonstrate difference between 'class variable' and 'instance variable'
    class MyClass
        def initialize
            @@class_var = 123
        end
        def showValue
            puts @@class_var
        end
        def setValue
            @@class_var = 456;
        end
    end

    class MyClass2
        def initialize
            @class_var = 123
        end
        def showValue
            puts @class_var
        end
        def setValue
            @class_var = 456;
        end
    end

    #############################

    # This is the 'class variable'

    objA = MyClass.new()
    objB = MyClass.new()

    objA.showValue # => 123
    objB.showValue # => 123

    objA.setValue

    objA.showValue # => 456
    objB.showValue # => 456

    #############################

    # This is the 'instance variable'

    objC = MyClass2.new()
    objD = MyClass2.new()

    objC.showValue # => 123
    objD.showValue # => 123

    objC.setValue

    objC.showValue # => 456
    objD.showValue # => 123

---

###Magic Variables

There are 'magic' variables (like predefined variables in PHP).

For example you have `__FILE__` which refers to the current file being executed and there is also `$0` which refers to the file used to start the program. I mention these two specifically because these are used in the getting started examples on the [Ruby website](http://www.ruby-lang.org/).

---

###Constants

Constants are variables that cannot be changed once they are set. Any variable that is capitalised (e.g. `Myconstant`) is made into a constant. 

In other languages a constant is normally either prefixed with the keyword `constant Myconstant` or is all caps `MYCONSTANT`.

If you try to overwrite a constant you'll get the following message: `warning: already initialized constant` - although as far as I can tell by testing this in irb it seems to change the constant and only warns you rather than actually preventing you from changing the value? ***Maybe a Rubyist reading this can clarify if this is expected behaviour.*** 

---

###Symbols

Symbols are like static variables (or constants). They are used as identifiers, as a way to keep code cleaner.

If you had lots of hashes (which we'll come to later) and you have a key/property called "name" then you could write your hash like so…

    hash1 = { "name" => "Mark" }
    hash2 = { "name" => "Brad" }
    hash3 = { "name" => "Ash" }
    hash4 = { "name" => "Neil" }

…but `"name"` is being re-created in memory every time it's referenced. It's much more energy efficient to use a Symbol which looks like a variable but is prefixed with a colon `:name`…

    hash1 = { :name => "Mark" }
    hash2 = { :name => "Brad" }
    hash3 = { :name => "Ash" }
    hash4 = { :name => "Neil" }

…as you can see, Symbols don't have values like variables, they are literally just used as efficient identifiers.

---

###Functions/Methods

In Ruby everything is an Object (even Strings and Integers) so when you're defining a function you're really defining a method (methods are the same as functions but you normally call a function a method when it's attached to an object).

To define a method the syntax is:

    def method_name (arguments)
    	// function code
    end

In JavaScript, if a method doesn't explicitly specify a return value then it returns `undefined`. In Ruby a method will return the last expression evaluated in its body, and if there isn't one then it will return `nil` (`nil` is equivalent to `null` in PHP).

As was noted in the above 'Variables' section about variables being able to assigned multiple values, this can come in handy with method returning multiple values as well (which is quite interesting as I'm only used to functions in JavaScript returning a single value)… 

    def myMethod 
        [1, 2] # this being the last expression, this is what's returned
    end

    a, b = myMethod; # => a = 1, b = 2

You can call a method without parenthesis, e.g. `myMethod` apposed to `myMethod()`. The choice is yours whether you use parenthesis or not - personally there are times where I can see myself not needing them and other times using them so it's crystal clear that what I'm doing is calling a method (time will tell - but I understand a good practice is to use parenthesis whenever a method expects arguments to be passed otherwise there is no point in using them).

But note that with multiple arguments the caller must not have a space between the parenthesis and the function name.

So for example, these work…

    welcome("Mark", 30)
    welcome"Mark", 30
    welcome "Mark", 30

…but this doesn't `welcome ("Mark", 30)`.

And calling a method which takes no arguments will also cause an error when called using a space between the method name and the parenthesis.

e.g. `Person.speak ()` => error but `Person.speak()` or `Person.speak` is fine.

You can specify default values for arguments…

    def welcome (name = "World", age = 1)
    	puts "Hello #{name}!, I see you're #{age} years old."
    end

…which can be used as follows…

`welcome` => Hello World! Looks like you're 1 today  
`welcome "Mark"` => Hello Mark! Looks like you're 1 today  
`welcome "Mark", 30` => Hello Mark! Looks like you're 30 today

You can also add new methods to an existing object just by prefixing the name of the method with the relevant object… 

    def Math.someNewThing (x) 
        puts "#{x} was here"
    end

    Math.respond_to?("someNewThing") # => true

    Math.someNewThing(123) # => 123 was here
    Math.someNewThing("abc") # => abc was here

…in Ruby these types of method declarations are referred to as 'class methods' (or 'singleton methods')

In a similar example of extending already defined Classes:

    class Fixnum
        def seconds
            self
        end
        def minutes
            self * 60
        end
        def hours
            self * 60 * 60
        end
        def days
            self * 60 * 60 * 24
        end 
    end

    puts Time.now
    puts Time.now + 10.minutes
    puts Time.now + 16.hours
    puts Time.now - 7.days

Note: you might see methods that are called using a ? at the end, these indicate that the method will return a Boolean value (e.g. `respond_to?` which you'll see below in the 'Classes' section)… 

    x = []
    x.empty? # => true (…or x.empty?() if you prefer the use of parenthesis)

    x = [1, 2, 3]
    x.empty? # => false

If you want to know what methods are available to an object/class then look at the `class` of the object and then inspect the methods available… 

    my_hash = { :name => "Mark" }
    my_hash.class # => Hash
    Hash.instance_methods # => [:rehash, :to_hash, :to_a, :inspect, :to_s, :==, :[], :hash, :eql?, :fetch, :[]=, :store, :default, :default=, :default_proc, :default_proc=, :key, :index, :size, :length, :empty?, :each_value, :each_key, :each_pair, :each, :keys, :values, :values_at, :shift, :delete, :delete_if, :keep_if, :select, :select!, :reject, :reject!, :clear, :invert, :update, :replace, :merge!, :merge, :assoc, :rassoc, :flatten, :include?, :member?, :has_key?, :has_value?, :key?, :value?, :compare_by_identity, :compare_by_identity?, :entries, :sort, :sort_by, :grep, :count, :find, :detect, :find_index, :find_all, :collect, :map, :flat_map, :collect_concat, :inject, :reduce, :partition, :group_by, :first, :all?, :any?, :one?, :none?, :min, :max, :minmax, :min_by, :max_by, :minmax_by, :each_with_index, :reverse_each, :each_entry, :each_slice, :each_cons, :each_with_object, :zip, :take, :take_while, :drop, :drop_while, :cycle, :chunk, :slice_before, :nil?, :===, :=~, :!~, :<=>, :class, :singleton_class, :clone, :dup, :initialize_dup, :initialize_clone, :taint, :tainted?, :untaint, :untrust, :untrusted?, :trust, :freeze, :frozen?, :methods, :singleton_methods, :protected_methods, :private_methods, :public_methods, :instance_variables, :instance_variable_get, :instance_variable_set, :instance_variable_defined?, :instance_of?, :kind_of?, :is_a?, :tap, :send, :public_send, :respond_to?, :respond_to_missing?, :extend, :display, :method, :public_method, :define_singleton_method, :object_id, :to_enum, :enum_for, :equal?, :!, :!=, :instance_eval, :instance_exec, :__send__, :__id__]

---

###Blocks

In Ruby a `code block` is any piece of code within either `do..end` or curly brackets `{}`. 

When creating a method, if you want to pass a code block in as an argument, you need to prefix the argument name with an ampersand `&` like so…

    def myfn (&code_block)
        %w(a e I o u).each do |vowel|
            code_block.call(vowel)
        end
    end

    myfn { |x| puts x }

…what the above code does is create an Array and then iterates over it. Every item in the Array is passed to the code block (the code block which is passed in as an argument to the method).

But the above can be simplified...

    def myfn
        %w(a e I o u).each do |vowel|
            yield vowel
        end
    end

    myfn { |x| puts x }

...this isn't as obvious but is less verbose (the `yield` keyword automatically detects the code block and passes control to it rather than us having to pass through the code block and executing the `call` method on the code block).

---

###Lambdas/Procs

Ruby also has Lambdas and Proc objects. These are similar to Blocks but have some differences worth mentioning.

####Procs

Procs are the same as blocks but can be saved into a variable so they are easily reusable. A block on the other hand can't be reused. It can only be retyped for every method that you want to use it on. 

The following is an example of how to use a Proc object instead of Block...

    my_proc = Proc.new { |x| puts x }

    def myfn proc_obj
        %w(a e I o u).each do |vowel|
            proc_obj.call(vowel)
        end
    end

    myfn my_proc

Notice we use a `call` method on the Proc object rather than the `yield` keyword which we use for a Block.

So if you have a one time piece of code you want to pass to a method then a block would make sense, but if you have a piece of code that you want to reuse across multiple methods then best to make it into a Proc object.

####Lambdas

Lambdas are the same as Proc objects but with two slight differences.

1. If you pass in the wrong number of arguments then the lambda will throw `ArgumentError`
2. If they have a `return` statement then the whole method wont suddenly return from that point (Proc objects cause the rest of the method to halt)

The following is an example of how to use a Proc object instead of Block...

    my_lambda = lambda { |x| puts x }

    def myfn lambda_obj
        %w(a e I o u).each do |vowel|
            lambda_obj.call(vowel)
        end
    end

    myfn my_lambda

Lambdas are very popular in other languages hence it's inclusion in Ruby (it's just a nice way to pass around code blocks). 

The following example is modified from a test on RubyMonk but is a good example of using lambdas…

    def with_names(fn)
      result = []
      [ ["Christopher", "Alexander"],
        ["John", "McCarthy"],
        ["Joshua", "Norton"] ].each do |pair|
          result << fn.call(pair[0], pair[1])
      end
      result
    end

    l = lambda { |name1, name2| "#{name1} #{name2}" } 

    with_names(l)

---

###Classes

The Classes syntax is as follows…

    class ClassName
    	def initialize ()
    		// code
    	end
    	// code
    end

Instance variables for classes are defined using `@variable_name` and are available to all methods of the class.

For example…

    class Person
    	def initialize (name = "Bob")
    		@name = name
    	end

    	def speak
    		puts "Hello, my name is #{@name}"
    	end
    end

    employee = Person.new("Mark") 

…executing this via irb returns `#<Person:0x007feb988ce048 @name="Mark">`

Now executing `employee.speak` returns "Hi, my name is Mark".

To check what methods exist for an object/class we can use `instance_methods` (e.g. using above example Class: `Person.instance_methods`) which shows ALL methods, even those you've not defined yourself.

We can ignore ancestor methods by setting the `instance_methods` argument to false: `Person.instance_methods(false)` which then just shows us the one method we defined (you could use no parenthesis `Person.instance_methods false` but I don't think that is as clear in this instance).

A quick note about the use of parenthesis: I think there is no right or wrong choice but rather it depends on the tastes of the invidual user. I personally find no-parenthesis cleaner, but in some places it is just too confusing without them, so I like to mix and match wherever I feel it's appropriate.

We can do a check to see if our object/class has a certain method available by checking if it `responds` to it…

`employee.respond_to?("speak")` => true  
`employee.respond_to? "speak"` => true

To create privileged methods, within the class we need to specify `attr_accessor :variable_name` (where by `variable_name` is replaced with the appropriate value). This then defines two additional methods onto the class which provides access to the specified variable. The two methods added are `variable_name` (which 'gets' the value) and `variable_name=` (which 'sets' the value).

For example…

    class Test
    	attr_accessor :user_name
    	def initialize (name)
    		@user_name = name
    	end
    	def speak
    		puts "hello #{@user_name}"
    	end
    end

    tester = Test.new("Mark")

`tester.speak` => hello Mark  
`tester.user_name` => "Mark"  
`tester.user_name= "Bob"` => "Bob"  
`tester.user_name` => "Bob"

Note: as well as `:attr_accessor` which creates getter and setter methods, there is `:attr_reader` which only creates a getter method, and `:attr_writer` which only creates a setter method.

---

###Loops

Loops in Ruby are straight forward…

    list = ["a", "b", "c"]
        
    list.each do |item|
        puts item
    end

The `each` method executes a block of code for each item in the Array. The `do…end` section is such a block. The pipes `||` denotes the parameter `|name|` and that parameter is bound to each list item.

One thing to be aware of (and you'll see this later under the 'Numbers' section), you can swap out `do…end` for normal curly brackets… 

    list = ["a", "b", "c"]
        
    list.each { |item|
        puts item
    }

…but for an `each` loop it doesn't look as nice as `do…end` so I keep with that style instead. But as you'll see I still like using curly brackets for other types of loops that don't have parameters.

There is a standard `while` loop as well within Ruby… 

    count = 0
    while count < 10
        puts "count = #{count}"
        count += 1 # Ruby has neither ++ or -- to increment/decrement a value
    end

Also with a `while` loop (if you're using a 'switch..case' style statement - like you would see in JavaScript) you can explicitly return a value… 

    def myFunction
        xyz = "def"
        while true
            case xyz
                when "abc"
                    return true
                when "def"
                    return false
                else
                    return nil # in case xyz doesn't equal what we expect
            end
        end
    end

There are other types of iterator methods such as `map`… 

    myArray = [1, 2, 3, 4]
    newArray = myArray.map do |x|
        x*x
    end

…which returns `[1, 4, 9, 16]`

---

###Conditionals

Ruby is slightly different to other programming languages in that even its syntax is very expression-oriented. So where a control structure like `if` would be called a 'statement' in other languages, in Ruby it is actually an expression which means it can be assigned to a variable like so…

    x = 5
    y = 10
    result = if x < y then 
        x
    else 
        y
    end

    # depending on how complicated your condition is you could put it all on one line like so… 
    result = if x < y then x else y end

…and so, as we've mentioned before with `functions`, these types of blocks return the last expression evalutated inside the block, so in the above example the last expression is returned and stored in the `result` variable.

---

###Strings

Building up string values can be a bit of a nightmare in other languages. I know in PHP and JavaScript it's a real pain without including some kind of templating rendering (such as `Mustache`). But in Ruby they provide a technique called Interpolation which is where method arguments and variables can be inserted into a string (must be a double quoted string, not single quotes) using: `#{variable_name}`. 

For example… 

    def welcome (name)
    	puts "Hello #{name}!"
    end

    welcome("Mark")

Multiple arguments work the same way…

    def welcome (name, age)
    	puts "Hello #{name}!, I see you're #{age} years old."
    end

You could do the same with simple string concatenation… 

```
puts "Hello" + name + "!, I see you're " + age + " years old."
```

…but as you can see it's not as nice to look at or easy to read and definitely isn't as maintainable.

As mentioned earlier, as everything in Ruby is an object (even Strings) there are methods available to strings such as:

* `sub` (basic find/replace)
* `gsub` (basic 'global' find/replace)
* `scan` (regex based search which executes code block for each match)
* `match` (regex based search with Array of results returned)

For example…

    "foobarfoobar".sub('bar','foo')
    # => foofoofoobar

    "foobarfoobar".gsub('bar','foo')
    # => foofoofoofoo

    x = "This is a test"
    x.sub(/^[a-zA-Z]{4}/, 'Hello')
    # => "Hello is a test"

    x = "The car cost £2000 in 2012"
    x.scan(/\d+/) { |match| puts match }
    # => 2000
    # => 2012
    # scan() without the code block returns an array of matches

    x = "This is a test".match(/(\w+) (\w+)/)
    puts x[0] # => This is
    puts x[1] # => This
    puts x[2] # => is

---

###Arrays

We've seen Arrays used quite a bit already, but lets look at some additional methods and operators available… 

The bitwise operator `<<` is used to add a new item to an array:

    x = []
    x << "abc"

…which is equivalent to `x.push("abc")`

Note: Strings also use the `<<` operator to add content to them:

    testString = "this is my string"
    testString << " that has extra stuff added to it"
    testString # => "this is my string that has extra stuff added to it" 

There are methods for removing the last item in the Array `x.pop()` as well as joining an array items into a string using the specified character(s) as a separator `x.join(", ")`.

With Strings you can use the `split` method to convert a string into an Array: `"this is my string".split(" ")` which returns `=> ["this", "is", "my", "string"]`.

If you need to concatenate two Arrays you can use the `concat` method… 

    arr = ["a", "b", "c"]
    arr.concat(["d", "e"])
    arr # => ["a", "b", "c", "d", "e"] 

…there is also more basic concatenation using the `+` operator `arr + ["d", "e"]` which returns `["a", "b", "c", "d", "e"]` but one caveat is that you must remember to set the array to be overwritten. For example, the previous code will return an Array which is a combination of `arr` and `["d", "e"]` but it doesn't actually overwrite the original Array (`arr` will still return `["a", "b", "c"]`). If you were expecting `arr` to be changed to `["a", "b", "c", "d", "e"]` then you would need to explicitly overwrite `arr` using: `arr += ["d", "e"]` instead.

There are two other useful Array methods `arr.first` and `arr.last`. Can you guess what they do? That's right, they return the first and last items in the Array.

Some other interesting features of Ruby is the ability to write Arrays more quickly using the `%w()` method. So instead of writing `["a", "e", "i", "o", "u"]` you would write `%w(a e i o u)` which generates the same Array.

There are many Array methods for inserting new items into an Array, but you can also use Ranges (which normally work like `('A'..'Z')`):

    arr = [0, 1, 2, 3, 4, 5, 6]
    arr[1..3] = ["a", "b", "c"]
    arr # => [0, "a", "b", "c", 4, 5, 6]

---

###Hashes

Hashes are like 'objects' in JavaScript and 'associative arrays' in other languages. Like Arrays they have an iterator method called `each` which works the same way, the only difference being is that is doesn't just pass the value through but the 'key' as well.

So for example to create a hash you would use… 

    h = { 
        "a" => 1, 
        "b" => 2
    }

…then you can access the relevant key/values (and add new key/values) like so… 

    h["a"]     # => 1
    h["b"]     # => 2
    h["c"] = 3 # create a new key/value
    h["c"]     # => 3

…and finally you can loop through the hash…

    h.each do |key, value|
        puts "The value: #{value} belongs to the key #{key}"
    end

You don't have to use a String as a hash `key`. You can use any object or Symbol.

In Ruby 1.9 the keys of a hash are returned in the order they were added when looping properties (unlike JavaScript where the order are not guaranteed).

To see what keys are available in an object use the `keys` property:

    hash = { :name => "Mark", :age => 30 }
    hash.keys # => [:name, :age]

You can delete a key/value from the hash using `hash.delete(key)`

Hashes also have shortcut for deleting properties depending on their value: `hash.delete_if { |key, value| value <= 30 }`

---

### Numbers (and how 'everything is an object' - similar to JavaScript)

In Ruby, all values are objects. This includes even simple things like numeric literals. So for example you can use a number to help carry out a certain action 'x' amount of times…

    3.times {
        puts "This gets written three times"
    }

And as explained earlier you can swap curly brackets for `do…end`…

    3.times do
        puts "This gets written three times"
    end

But you can also do…

    1.upto(9) { 
        |x| puts x 
    }

…which can be interchanged with… 

    1.upto(9) do |x| 
        puts x 
    end

I normally find anything that takes parameters `|x|` looks better with `do…end` style syntax.

---

###Conclusion

Well, this is just an 'introduction' so as you can see although we've covered a lot of ground already we're still literally just scratching the surface. As I start learning more about Ruby I'll create new blog posts to follow on from here.