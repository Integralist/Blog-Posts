#Build a site with Ruby and Sinatra

##Table of Contents

* Introduction
* GitHub Repository
* Sinatra
* Hosting
* Example
    * Requirements
    * Set-up
    * Performance
    * Loading pages
    * Loading templates
    * Static resources
    * Handling errors
    * Hosting our application
* Conclusions

##Introduction

In my opinion there's isn't enough good information on the web about how to get started building a [Ruby][1] based website (unless you want to use the ever popular [Ruby on Rails][2] framework). 

But even then, it's not as straight forward as you'd think (not for someone new to the language or server-side coding in general).

###DSL vs GPL

Before we go any further, it's worth clarifying the difference between a Domain Specific Language (DSL) and a General Purpose Language (GPL). Languages like JavaScript and PHP are DSL's because they were designed to be run within a specific 'domain' (and by domain we really mean 'environment') such as the web. But languages like Python and Ruby are GPL's because they can run within many different domains (e.g. Ruby can run on the web, desktop, command line etc).

If we look at a DSL language such as [PHP][3]**: it is very easy to build a site using PHP because you can just input some procedural code, upload the file onto a server that supports PHP (and what server doesn't nowadays) and that's all there is to it (!now I'm obviously not suggesting this is good practice - I'm just illustrating the point that if you wanted to __just get started__ with PHP then that is all there is to it).

With a GPL such as Ruby it isn't so straight forward (it's like the old adage "the questions easy when you know the answer": Ruby can be easy to use and get set-up when you know what you're doing but the fundaments - in my opinion - are difficult to grasp without a helping hand).

***PHP (and JavaScript) were designed to be DSL's but have since evolved more into GPL's as they can be run on the desktop as well as the web (and many other environments they weren't originally designed for).*

My issue with Ruby is that there is no "just get started" guide for those who want to play around with Ruby OR who want to build a site with it but not have to deal with design patterns such as MVC (or not straight away at least). I wouldn't be surprised if developers new to Ruby felt obliged to use Ruby on Rails because it seems to be the only Ruby related framework out there that gets any real spotlight - and I understand why, because it truly is an all encompassing framework - but I wanted a Ruby site without the bloat of a framework like [Ruby on Rails][2]. I didn't want all the confusing MVC structure and features that I'd never use. Hence why I'm writing this now.

##GitHub Repository

You can find an example repository with all code discussed in this article here: [https://github.com/Integralist/passage](https://github.com/Integralist/passage)

##Sinatra

So, let me begin by introducing you to [Sinatra][4]. Sinatra is a micro web framework for Ruby that although small (much smaller than a framework such as Ruby on Rails) is still surprising complex and powerful (I'd highly recommend reading O'Reilly's book "[Sinatra: Up and Running][5]").

Sinatra does require you to do a lot of work off your own back, but at the same time it doesn't enforce a particular style on you. It's also very easy to get a site up and running with Sinatra and that is hopefully what I'll demonstrate here today.

##Hosting

Obviously running a site locally on your computer is one thing, but you want to be able to share it with the community and let others use it! Ruby hosting is something I'm still investigating but there are currently two options for those who aren't willing to put their money where their mouth is just yet. These are:

* [Heroku][6]
* [OpenShift][7]

Both offer professional services, and both offer free options which allow you to test their services, and if you discover your latest web creation is doing well then you can pay money to these companies to help your application scale with the increased users it is getting.

But for the moment we just want to get our application online and so we'll be using the first option Heroku (which is the more well known of the two and it one of the most popular cloud hosting services available - specifically for Ruby, although they also, like OpenShift, host other application types like: Python and Node.Js etc).

We'll come back to the hosting aspect at the end of this article when we're ready to actually upload our application.

##Example

###Requirements

OK, for us to build our application we're going to need to install some software. The first and most obvious being the Ruby programming language itself - please see my [introduction to the Ruby language][8] for more information on this.

The next thing we're going to need to install is Sinatra, and to do this you'll need your CLI (Command Line Interface) tool of choice. I'm on a Macintosh so by default that will be the Terminal app. All we need to do is to execute the following command `gem install sinatra` and this will start downloading the Sinatra files from the web**.

***When Ruby is installed it provides you with something called 'gems'. These gems are actually code projects (like repositories on GitHub) and are hosted publically on [http://rubygems.org/][10]. So if you're looking for a way to send email then you might search the Ruby Gems site and find a project called Pony which lets you send email very easily using a nice API and which can be install simply by running the command `gem install pony`.*

To see what 'gems' you have already installed you can run the command `gem list --local` and this will display something similar to this (which is what I have installed on my own machine currently)…

```sh
bundler (1.1.3)
cgi_multipart_eof_fix (2.5.0)
daemons (1.1.8)
eventmachine (0.12.10)
fastthread (1.0.7)
gem_plugin (0.2.3)
i18n (0.6.0)
mail (2.4.4)
mime-types (1.18)
polyglot (0.3.3)
pony (1.4)
rack (1.4.1)
rack-protection (1.2.0)
rake (0.9.2.2, 0.9.2)
rubygems-bundler (0.3.0, 0.2.8)
sass (3.1.17)
shotgun (0.9)
sinatra (1.3.2)
thin (1.3.1)
tilt (1.3.3)
treetop (1.4.10)
```

Now, there are some other gems we'll want to install: 

1. `bundler` (which I had installed by default but if you don't then just run `gem install bundler`) - Bundler manages an applications dependancies and we'll see this in use nearer the end. 
2. `shotgun` which makes it easier to test our application locally (run `gem install shotgun`). 
3. Lastly we want to install a different web server than what is used by default by Sinatra which is called `Thin` (run `gem install thin`).

So let's quickly recap on the applications we're installing:

* Sinatra (Web Framework)
* Bundler (Dependancy Manager)
* Shotgun (Utility)
* Thin (Web Server)

It's only four items and that'll be enough to get us going and on our way...

###Set-up

Now we have our software set-up, lets open our programming tool of choice and start writing some code.

First thing we want to do is to create a file that will be our application. So lets go ahead and do that and call it `app.rb` and lets add the following content to it…

```ruby
#!/usr/bin/env ruby

require 'sinatra'

get '/' do
    "Hello World!"
end
```

…now open your CLI, direct yourself to the folder where the above file is located and run `ruby app.rb`.

You should see a message similar to this…

```sh
== Sinatra/1.3.2 has taken the stage on 4567 for development with backup from Thin
>> Thin web server (v1.3.1 codename Triple Espresso)
>> Maximum connections set to 1024
>> Listening on 0.0.0.0:4567, CTRL+C to stop
```

You should now be able to open your web browser and go to: `http://localhost:4567/` and see the message "Hello World!"

If you can then OK, this is our app, good job!

Lets quickly recap what we've got in our file:

* `#!/usr/bin/env ruby`  
The first line tells the interpreter that's trying to run this file that we should interpret this file as a Ruby application.

* `require 'sinatra'`  
Here we're loading in the Sinatra framework ready for us to use.

* `get '/' do "Hello World!" end`  
This is Sinatra's "Classic"** DSL syntax which basically says when someone loads the home page show them the message "Hello World!".

***There are two ways to build Sinatra applications: one is the "Classic" way (which we'll be doing as it's much easier to understand) and the other is the "Modular" way which doesn't use the DSL syntax and taps straight into the base Sinatra Class.*

So at this point you've got some Ruby code and you're able to view it in a web browser. Now lets look further at Sinatra's DSL syntax...

###Loading pages

If you wanted to have a page called "Projects" that you accessed via `http://yourdomain.com/projects` then all you would need to do is to is this…

```ruby
get '/projects' do
    # page content
end
```

If you had a form on that page which POST'ed the data back to the current page and you wanted to do something with that data (e.g. a login form) then you could do this…

```ruby
post '/projects' do
    # do something with the form fields
    user = params[:user]
    pass = params[:password]
end
```

…you'll notice that the form data is passed to the block using special parameters called `params`. So in the above example we had two form fields with the names `user` and `password` and we're assigning their values to variables.

You can also access the data added to the URL path. For example if you had a page which added two numbers together then it could be handled directly via the URL as follows…

```ruby
get '/add/:a/:b' do |a, b|
    "#{a.to_i + b.to_i}"
end
```

…this would display on the page the result of adding `a` to `b`. So if I went to the URL `http://localhost:4567/add/2/2` then this would display `4` as that would be the result of `2 + 2` which was the two paths I specified within the URL.

###Loading templates

Now at the moment we're using the fact that with Ruby, the last expression inside a function is what gets returned from that function. These calls to `get` and `post` are actually function calls hidden behind an abstraction. So in my original example we were displaying "Hello World!" to the page because that was the last expression inside the `get` call.

But we ideally want to be displaying HTML to the user! To do this we'll use 'templates'.

Now templates can be inlined inside your ruby code, but personally I prefer them to be external because I think that's a lot cleaner.

Our code is going to start looking something like this…

```ruby
get '/' do
    erb :home
end

get '/projects' do
    erb :projects
end
```

…what this is doing is using ERB (which stands for 'Embedded Ruby') and is the standard templating language available in Ruby - although there are many templating languages you could load and use in its place. What we've said here in our code is load the `:home` template/ERB file when on the home page, and load the `:project` template/ERB file when on the projects page.

By default Sinatra looks for templates inside of the folder called 'views' (you can change this if you want but I didn't bother as 'views' made the most sense for a folder containing 'templates' - especially considering MVC is likely the way you'll code will want to go in the near future).

Inside our 'views' folder we'll need to create two files then: `home.erb` and `projects.erb` and they'll look a little bit like this…

```html
<!-- home.erb -->
<p>HTML content for my home page</p>
```

```html
<!-- projects.erb -->
<p>HTML content for my projects page</p>
```

Now this doesn't look like much of a HTML page, and that's because I'm using (or I'm going to be using very shortly) what Sinatra refers to as a main layout file. What this means is I can have a 'master' HTML file if you will, that stays the same for every page and I can load my templates into that master layout. This is done by creating a 'layout.erb' file (also within the 'views' folder). If Sinatra finds a 'layout.erb' file it will automatically use it as a master layout for all your templates.

But if I didn't create a 'layout.erb' file then I could have made my templates above look more like a proper page with full HTML…

```html
<!-- home.erb -->
<!doctype html>
<html>
	<head>
        <meta charset="utf-8">
        <title>My Page</title>
	</head>
	<body>
	   <p>HTML content for my home page</p>
	</body>
</html>
```

…but I prefer having a master layout to handle this stuff so lets create a 'layout.erb' file (within the 'views' folder) and add the following content to it…

```html
<!-- layout.erb -->
<!doctype html>
<html>
	<head>
        <meta charset="utf-8">
        <title>My Page</title>
	</head>
	<body>
	   <%= yield %>
	</body>
</html>
```

…you should notice the Ruby tags `<% %>` which are used to place Ruby code inside of them. Here we're telling Ruby to `yield` to the template file we're loading. So for example when a user accesses the home page and we load 'home.erb', we're effectively loading the 'layout.erb' file and telling it that when it reaches the `body` tag we want it to load in the content from the 'home.erb' file into it so it will end up rendering in the web browser like this…

```html
<!doctype html>
<html>
	<head>
        <meta charset="utf-8">
        <title>My Page</title>
	</head>
	<body>
	   <p>HTML content for my home page</p>
	</body>
</html>
```

If you want to load a different master layout for a specific page then you can do that also. For example I have a page that I display to users of Internet Explorer version 7 or lower. The master layout for that page is a lot simpler than the other pages of my site in that it loads different stylesheets specifically for this IE page. So in my application file I have the following…

```ruby
get '/internet-explorer' do
    erb :ie, :layout => :layout_ie
end
```

…and what you can see here is that I'm not only telling Sinatra to load the 'ie.erb' file but to also use the 'layout_ie.erb' file as the master layout file for this page rather than the default 'layout.erb'.

One other thing worth mentioning is that you can pass variables from your route block into your template using class instance variables…

```ruby
post '/contact' do
    redirect "/contact-error/name" if params[:user].empty?
    redirect "/contact-error/email" if params[:email].empty?
    redirect "/contact-error/message" if params[:message].empty?

    erb :contact_success
end

get '/contact-error/:field' do |field|
	@field = field
	erb :contact_error
end
```

…in the above example when a user makes an error on my contact form I redirect them to a page that shows them what field they made an error on. In my template file I have…

```erb
<p>Sorry there was an error with your form submission. Seems you didn't fill in the <%= @field %> field.</p>
```

…notice I picked up the field from the URL and stored it in a class instance variable and used that variable within my template.

###Static resources

OK, the next thing to be aware of is that Sinatra serves up ALL static resources from a folder called 'public' (again you can change this but I'm happy using it - as the name is perfectly logical already).

What this means is that if you have a file path like: `/path/to/file` then for that to work you need to have the folders/files within the 'public' folder otherwise Sinatra can't locate them. It DOESN'T mean you have to change all your paths to `/public/path/to/file`. You keep the path the same as it is already but you just move the files/folders inside of a folder called `public`.

So for example in all my projects I have my JavaScript files in the following directory…

`Assets/Scripts/`

…and all my CSS in the following directory…

`Assets/Styles/`

…all I need to do is create an 'Assets' folder within the 'public' folder. Within that I then have the 'Assets' folder, and inside of that is my 'Scripts' and 'Styles' folders which hold the relevant files.

If this sounds a bit confusing then have a look at the GitHub repo linked at the top of this article to see what I'm talking about.

###Handling errors

If the user tries to access a page that doesn't exist then you can direct them to your own `404 error` page by using the following route…

```ruby
not_found do
    erb :notfound
end
```

…as you can see I'm loading a 'notfound.erb' template for the content. This will get called anytime an unknown URL is specified by the user.

One thing you might not want to have happen is if someone types in `http://localhost:4567/projects/` instead of `http://localhost:4567/projects` (notice the extra forward-slash at the end) then that is considered a different 'route' and wont be recognised so the user is directed to the 'not found' route. To fix this you can do the following…

```ruby
before do
    request.path_info.sub! %r{/$}, ''
end
```

…what this is is a 'filter' block and it is executed before every HTTP request. So the code `request.path_info.sub! %r{/$}, ''` is run before every HTTP request and thus the Regular Expression finds the last forward-slash and removes it. Which means it doesn't matter if the user puts an extra slash at the end of the URL.

Note: there is also a `after` filter block as well for doing tidy up work (although I've not had any reason to use it - yet).

But if there is an actual error then you can use…

```ruby
error do
    erb :error
end
```

In a development environment Sinatra tries to be helpful by displaying very detailed error messages. But you may find this overrides your error page you want to show to a user. So if you want to test your error page is working correct then simply `disable :show_exceptions` to some where near the top of your application file and this will show you what your users will see in the live environment (e.g. they'll see your actual error page rather than some hideous looking error codes).

Also, within your 'error.erb' template file you can access the error using the environment variable `sinatra.error`…

```erb
<p>Sorry there was the following error: <strong class="error"><%= env["sinatra.error"] %></strong></p>
```

###Performance

Some performance things you can do with Sinatra are you can tell it to cache your static resources for a set amount of time…

```ruby
# We set the cache control for static resources to approximately 1 month
set :static_cache_control, [:public, :max_age => 2678400]
```

You can also tell Sinatra to use the 'Thin' server rather than the default 'WEBrick' server if it's available (Thin is a supremely better performing web server so do please use it!)

```ruby
# We specify which server we want to use (Thin is tried first and then failing that WEBrick)
set :server, %w[thin webrick]
```

We can also have the web server automatically GZIP all our static resources such as HTML content, JavaScript files, CSS files by using one line of Ruby code…

```ruby
use Rack::Deflater
```

…this uses the 'Rack' middleware application (Rack is what sits behind Sinatra as is the actual HTTP web server interface. But Sinatra hides all of that behind the DSL syntax of `get` and `post` calls (nice huh).

###Hosting our application

Right. Let's get our application uploaded shall we.

This is going to be short and quick…

1. Set-up account ([heroku.com][6])
2. Install toolbelt ([toolbelt.heroku.com][9])
3. Open your Command Line Interface (CLI) and enter:
	* `heroku login` (follow on screen instructions)
	* You may want to add additional SSH keys, if you do use: `heroku keys:add`
	* To create an app on heroku use: `heroku create --stack cedar` (you can also do: `heroku create yourappname --stack cedar`) - `cedar` will shortly be the *default* web stack on heroku but for the time being it's worth including it in the command `--stack cedar` otherwise your app will be created on an older web stack which isn't as good.
4. Create a `config.ru` file and add the following content:  
```ruby
require 'app' # where app is the name of your main file that initializes your web application
run Sinatra::Application
```

5. Create a Gemfile file (no file extension) and add the content:  
```
source 'http://rubygems.org'
gem 'sinatra', '1.3.2'
gem 'thin', '1.3.1'
```

6. Go back to your CLI and enter:
	* `bundle install` which will create a `Gemfile.lock` file specifying ALL the dependancies needed for your app to run (you shouldn't need to manually create your own Gemfile - as I've told you to do in step 5. - but just in case `bundle install` doesn't work for you then you can at least do it manually).
	* Create a Procfile (no file extension) and add the content: `web: bundle exec ruby app.rb -p $PORT`  
	   e.g.  
	   `touch Procfile`,  
	   `echo web: bundle exec ruby app.rb -p $PORT > Procfile`
	* Commit your files using Git (sorry not going into how to use Git here!)
	* If you've not done this already then create a `remote` for heroku: `git remote add heroku git@heroku.com:xxxxx.git` (when you created your app earlier heroku would have generated a remote URL for you to use)
	* `git push heroku master`
	* `heroku open` will open your default browser to the relevant app URL heroku has given you (they are normally pretty crazy looking URL's - but you can at some later stage - if you want - register a proper domain and point it to the app on heroku's server so you don't have to give out a dodgy long URL to your users)
	
Hopefully if you followed along, and had no errors, then you should see your new app online and hosted by heroku!

##Conclusions

Well, there you have it. Building a website using Sinatra and Ruby, and actually getting it hosted online.

It's a bit of a whirlwind pace we've set here, going through lots of different concepts such as performance tricks and handling errors, through to loading template files etc. But hopefully it wasn't too fast paced and you managed to use the information presented here. 

I'm actually writing this at record pace at home late at night feeling very tired so if this article doesn't read right, or you think it needs improving then please get in contact and let me know what you think would help improve it (and thus help other users benefit from it!)

Thanks.

[1]: http://www.ruby-lang.org/  "The Ruby Programming Language"
[2]: http://rubyonrails.org/ "Ruby on Rails"
[3]: http://www.php.net/ "PHP Hypertext Preprocessor"
[4]: http://www.sinatrarb.com/ "Put this in your pipe and smoke it"
[5]: http://shop.oreilly.com/product/0636920019664.do "Ruby for the Web"
[6]: http://www.heroku.com/ "Cloud Application Platform"
[7]: https://openshift.redhat.com/app/ "Develop and Scale Apps in the Cloud"
[8]: https://github.com/Integralist/Blog-Posts/blob/master/Introduction-to-Ruby.md "Introduction to Ruby (a front-end developers perspective)"
[9]: http://toolbelt.heroku.com/ "Everything you need to get started using heroku"
[10]: http://rubygems.org/ "Your community gem host"