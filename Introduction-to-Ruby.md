#Introduction to Ruby (a front-end developers perspective)

##Introduction

I'm not a Ruby developer. I'm a front-end developer who focuses on JavaScript, Regular Expressions, HTML and CSS (and that means I like to keep up to speed with each of the latest iterations of the languages, currently: ES5/6, HTML5, CSS3/4 etc). 

I've experience with PHP. Writing object-oriented code, creating databases and bespoke Content Managed Systems etc, but I've not touched that sort of stuff for a few years now as the front-end stack is where I've concentrated my attention (it's what I enjoy most).

So with that admission out in the open, this introduction to the Ruby programming language is likely to be full of holes you could drive a truck through. So why do it? Because with the arrival of JavaScript pre-processors such as CoffeeScript and the new additions to both ES5 (and much more so in ES6), these new additions are pushing the JavaScript language to be more 'functional' and thus similar in syntax to the Ruby programming language. 

Looking at CoffeeScript I wasn't convinced. I like the JavaScript syntax, I think it's actually quite a beautiful language (when written correctly). But I decided that the future is just around the corner so why wait to see what all the fuss is about. In my mind, the best way to get involved with the new JavaScript/CoffeeScript style syntax is to investigate some of its inspirations which (seem to) stem from Ruby.

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
* Functions/Methods
* Classes
* Loops
* Conditionals
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

```
5.times {
    print "This gets written five times"
    sleep 2
}
```

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

```
=begin
    Lots and lots of
    code that I want
    to comment out all at once
=end
```

With multi-line comments there must be no whitespace before the `=begin` and `=end` statements otherwise your script will throw an error.

---

###Variables

Variables do not have to be declared. So you can literally write `a = 123`.

You can assign multiple variables at once like so…

```
x, y = 1, 2     # which is effectively the same as x = 1, y = 2
a, b = b, a     # which switches the values of each variable
x,y,z = [1,2,3] # assign each array item value to a variable, so x = 1, y = 2, z = 3
```

Variables in JavaScript typically come in two flavours: Global and Local. In JavaScript if you declare a variable with the `var` syntax then the variable is added as a property to the global object (which depending on the environment JavaScript is running in) most of the time will be the `window` object. If you declare a variable inside of a function in JavaScript using the `var` syntax then that variable is only available within that function (unless it's accessible via some priviledged object).

In Ruby, if you declare a variable inside a function with no prefix then it is only available within that function. If you prefix it with a dollar sign (e.g. `$my_var = 123`) then that declares it as a global variable and is accessible from any where in the program.

When using Classes, if you declare a variable within the class using an `@` prefix (e.g. `@my_var = 123`) then that variable is available to the object created by that class only (also known as an 'instance variable'). Where as a double `@@` (e.g. `@@my_var = 123`) is known as a 'class variable' which means it is available to all objects created by that particular class and any changes to this class variable is reflected in all objects created from that class. 

So for example, if I have a class called "MyClass" and inside of it I set `@@my_var` to equal 456 instead of 123 then every object created from "MyClass" will have `my_var` set to 456. Whereas if "MyClass" had `@my_var` set to 123 and I create a new object from that class and set `@my_var` to 456 - an 'instance variable' - then only that object would see the value of `@my_var` as 456, all other objects created from the class would still see the value as 123. It sounds tricky but it's not that bad really. Here is an example of what the above was trying to explain… 

```
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
```

---

###Magic Variables

There are 'magic' variables (like predefined variables in PHP).

For example you have `__FILE__` which refers to the current file being executed and there is also `$0` which refers to the file used to start the program. I mention these two specifically because these are used in the getting started examples on the [Ruby website](http://www.ruby-lang.org/).
```

---

###Symbols

Symbols are like variables but are prefixed with a colon like so `:my_symbol`. They are lightweight Strings and are normally used in situations where you require a string but you won't be displaying it or doing too much with it. It is considered to  be easier on the server (similar in Regular Expressions where a 'non-capturing group' is more efficient than a capturing group when all you need to do is group a sub expression, rather than actually remember it's value).

---

###Functions/Methods

To define a function the syntax is:

```
def function_name (arguments)
	// function code
end
```

In JavaScript, if a function doesn't specify a return value then it returns `undefined`. In Ruby a function will return the last expression evaluated in its body, and if there isn't one then it will return `nil` (`nil` is equivalent to `null` in PHP).

As was noted in the above 'Variables' section about variables being able to assigned multiple values, this can come in handy with functions returning multiple values as well (which is quite interesting as I'm only used to functions in JavaScript returning a single value)… 

```
def myFunction 
    [1, 2] # this being the last expression, this is what's returned
end

a, b = myFunction; # => a = 1, b = 2
```

You can call a function without parenthesis, e.g. `myFunction` apposed to `myFunction()`. The choice is yours whether you use parenthesis or not - personally there are times where I can see myself not needing them and other times using them so it's crystal clear what what I'm doing is calling a function (time will tell).

Function arguments (and variables) can be inserted into a string (must be a double quoted string, not single quotes) using what's called "interpolation": `#{argument_name}`. 

For example… 

```
def welcome (name)
	puts "Hello #{name}!"
end

welcome("Mark")
welcome ("Mark")
welcome"Mark"
welcome "Mark"
```

Multiple arguments work the same way…

```
def welcome (name, age)
	puts "Hello #{name}!, I see you're #{age} years old."
end
```

But note that with multiple arguments the caller must not have a space between the parenthesis and the function name.

So for example, these work…

```
welcome("Mark", 30)
welcome"Mark", 30
welcome "Mark", 30
```

…but this doesn't `welcome ("Mark", 30)`.

And calling a method which takes no arguments will also cause an error when called using a space between the method name and the parenthesis.

e.g. `Person.speak ()` => error but `Person.speak()` or `Person.speak` is fine.

You can specify default values for arguments…

```
def welcome (name = "World", age = 1)
	puts "Hello #{name}!, I see you're #{age} years old."
end
```

…which can be used as follows…

`welcome` => Hello World! Looks like you're 1 today  
`welcome "Mark"` => Hello Mark! Looks like you're 1 today  
`welcome "Mark", 30` => Hello Mark! Looks like you're 30 today

Lastly, where we mentioned interpolation as a way to insert variables and arguments into a string, you could do the same with simple string concatenation… 

```
puts "Hello" + name + "!, I see you're " + age + " years old."
```

…but as you can see it's not as nice to look at or easy to read.

You can also add new methods to an existing object just by prefixing the name of the method with the relevant object… 

```
def Math.someNewThing (x) 
    puts "#{x} was here"
end

Math.respond_to?("someNewThing") # => true

Math.someNewThing(123) # => 123 was here
Math.someNewThing("abc") # => abc was here
```

…in Ruby these types of method declarations are referred to as 'class methods' (or 'singleton methods')

Note: you might see methods that are called using a ? at the end, these indicate that the method will return a Boolean value (e.g. `respond_to?` which you'll see below in the 'Classes' section)… 

```
x = []
x.empty? # => true (…or x.empty?() if you prefer the use of parenthesis)

x = [1, 2, 3]
x.empty? # => false
```

---

###Classes

The Classes syntax is as follows…

```
class ClassName
	def initialize ()
		// code
	end
	// code
end
```

Instance variables for classes are defined using `@variable_name` and are available to all methods of the class.

For example…

```
class Person
	def initialize (name = "Bob")
		@name = name
	end

	def speak
		puts "Hello, my name is #{@name}"
	end
end

employee = Person.new("Mark") 
```
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

```
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
```

`tester.speak` => hello Mark  
`tester.user_name` => "Mark"  
`tester.user_name= "Bob"` => "Bob"  
`tester.user_name` => "Bob"

---

###Loops

Loops in Ruby are straight forward…

```
list = ["a", "b", "c"]
    
list.each do |item|
    puts item
end
```

The `each` method executes a block of code for each item in the Array. The `do…end` section is such a block. The pipes `||` denotes the parameter `|name|` and that parameter is bound to each list item.

One thing to be aware of (and you'll see this later under the 'Numbers' section), you can swap out `do…end` for normal curly brackets… 

```
list = ["a", "b", "c"]
    
list.each { |item|
    puts item
}
```

…but for an `each` loop it doesn't look as nice as `do…end` so I keep with that style instead. But as you'll see I still like using curly brackets for other types of loops that don't have parameters.

There is a standard `while` loop as well within Ruby… 

```
count = 0
while count < 10
    puts "count = #{count}"
    count += 1 # Ruby has neither ++ or -- to increment/decrement a value
end
```

Also with a `while` loop (if you're using a 'switch..case' style statement - like you would see in JavaScript) you can explicitly return a value… 

```
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
```

There are other types of iterator methods such as `map`… 

```
myArray = [1, 2, 3, 4]
newArray = myArray.map do |x|
    x*x
end
```

…which returns `[1, 4, 9, 16]`

---

###Conditionals

Ruby is slightly different to other programming languages in that even its syntax is very expression-oriented. So where a control structure like `if` would be called a 'statement' in other languages, in Ruby it is actually an expression which means it can be assigned to a variable like so…

```
x = 5
y = 10
result = if x < y then 
    x
else 
    y
end

# depending on how complicated your condition is you could put it all on one line like so… 
result = if x < y then x else y end
```

…and so, as we've mentioned before with `functions`, these types of blocks return the last expression evalutated inside the block, so in the above example the last expression is returned and stored in the `result` variable.

---

###Hashes

Hashes are like 'objects' in JavaScript and 'associative arrays' in other languages. Like Arrays they have an iterator method called `each` which works the same way, the only difference being is that is doesn't just pass the value through but the 'key' as well.

So for example to create a hash you would use… 

```
h = { 
    "a" => 1, 
    "b" => 2
}
```

…then you can access the relevant key/values (and add new key/values) like so… 

```
h["a"]     # => 1
h["b"]     # => 2
h["c"] = 3 # create a new key/value
h["c"]     # => 3
```

…and finally you can loop through the hash…

```
h.each do |key, value|
    puts "The value: #{value} belongs to the key #{key}"
end
```

You don't have to use a String as a hash `key`. You can use any object.

---

### Numbers (and how 'everything is an object' - similar to JavaScript)

In Ruby, all values are objects. This includes even simple things like numeric literals. So for example you can use a number to help carry out a certain action 'x' amount of times…

```
3.times {
    puts "This gets written three times"
}
```

And as explained earlier you can swap curly brackets for `do…end`…

```
3.times do
    puts "This gets written three times"
end
```

But you can also do…

```
1.upto(9) { 
    |x| puts x 
}
```

…which can be interchanged with… 

```
1.upto(9) do |x| 
    puts x 
end
```

I normally find anything that takes parameters `|x|` looks better with `do…end` style syntax.

---

###Conclusion

Well, this is just an 'introduction' so as you can see although we've covered a lot of ground already we're still literally just scratching the surface. As I start learning more about Ruby I'll create new blog posts to follow on from here.