Beginners guide to AMD and RequireJs
====================================

Here is a short list of what we’ll cover in this post:

* What is AMD?
* Why does it matter?
* How did we get here?
* How does RequireJs (and alternatives) fit in?
* Can we use jQuery?
* Basic Example
* What now?
* Links/Reference Material

What is AMD?
------------

AMD stands for “Asynchronous Module Definition” and is a proposed API for loading modules (i.e. scripts) asynchronously.

Why does it matter?
-------------------

A few reasons, the summary of which is:

* Performance (loading scripts asynchronously improves the loading speed of your web page).
* Modular (which means code that is easier to maintain and re-use).
* Best practice (helps to reduce global variables, maintain dependancies, and your code will follow guidelines that will in the future become the standards for the next iteration of JavaScript)

How did we get here?
--------------------

The way we insert scripts into our pages has evolved over the years:

* Multiple `<script>` tags

```html
<script src="script-1.js">
<script src="script-2.js">
<script src="script-3.js">
```

* Script Loader (e.g. LABjs / YepNope.js …and many many more)

```html
// This example uses LABjs    
<script src="LAB.js"></script>
<script>
    $LAB.script('script-1.js').wait().script('script-2.js').script('script-3.js');
</script>
```

* AMD Script Loader (e.g. RequireJs / Curl …and a couple of others)

```html
<script data-main="main" src="require.js"></script>
```

The ‘multiple scripts’ has served us long and true, but means that the rendering of the page takes three times as long, as each `<script>` tag must be downloaded, then executed before the browser can move onto the next `<script>` (this is what is meant by loading scripts ‘synchronously’). Imagine if you are loading a JavaScript framework followed by a whole bucket full of plugins and then some of your own scripts. The page load time would increase quite significantly.

The way forward from there is using a ‘Script Loader’ which effectively lets you load multiple scripts asynchronously (i.e. in parallel with each other) so no waiting for one script, then moving to the next and waiting, and moving to the next. Unfortunately using a Script Loader doesn’t require your code to be modular in any real sense. So you can still have a mess of scripts all thrown together onto a page. Not to mention a few sketchy hacks are usually required to get these script loaders to load your scripts in the correct order so they don’t break the hell out of your web page.

So moving forward from that slightly better situation we come to ‘AMD Script Loaders’ which also lets you load scripts asynchronously but the scripts you are loading aren’t just a hodge podgy of random scripts pulled from the different corners of the web, they are well structured ‘modules’ that define their own dependancies and can be easily re-used across different projects because of their loose coupling with other scripts. They are also defined within their own scope so they don’t interfere with other scripts that may have been added by another developer before you, and so make it easier not to have a page full of global variables floating around causing havoc.

How does RequireJs (and alternatives) fit in?
---------------------------------------------

RequireJs is one of the more well known (and thus ‘popular’) AMD script loaders. It follows the ‘Modules Transport/C’ specification laid out by the CommonJs group ([http://wiki.commonjs.org/wiki/CommonJS](http://wiki.commonjs.org/wiki/CommonJS)).

The RequireJs website has a lot of information to get you started, but can be a bit confusing for those new to the concept. So we’ll dive into some of our own examples which will make it easier to understand how you can use RequireJs and AMD in general in your projects.

Can we use jQuery?
------------------

Sure! RequireJs actually has a special build of it that includes jQuery, but as jQuery too has seen AMD as the future, it has made itself AMD compatible (well, for those who might have more knowledge about AMD, it’s compatible in the sense that it can be loaded as a ‘named module’). 

So for the 1.7 release of jQuery it will be possible to not need the RequireJs specific build that includes jQuery - you’ll be able to just load jQuery as a dependancy when defining your ‘module’.

Basic Example
-------------

“OK, this is sounding groovy, how do I get involved?”

Well, to get you started I have a Github repo set-up which demonstrates the basics of using RequireJs (and trust me it’s basic, but then that’s the idea!)

[https://github.com/Integralist/RequireJs-Example/](https://github.com/Integralist/RequireJs-Example/)

There are comments in the code to help clarify what’s happening, but we’ll go over it here too.

The order of things will be:

1. Insert RequireJs into your web page.
2. Set-up the main script for the page (this script will load your other scripts (‘modules’) needed in this page).
3. Define your modules (ideally you’d define your modules first, but it made more sense to write it in this order).
4. Run a ‘build script’ when you’re ready to deploy your application (this takes your separate modules and combines them into a single script rather than having multiple scripts that need to be downloaded**++**)

**++**There is a trade-off here. Some people will argue that multiple scripts loaded asynchronously is better than one monolithic script. If you’re code is written to be modular in the first place, then I believe running a build script that combines/minifies each module into a single script is better performing than multiple http requests for all these separate scripts (especially when you consider mobile devices where the connection can quite easily drop out while loading a page and caching resources is a lot more stringent)

So here we go…

### 1. Insert RequireJs

I keep all my files for my application/website in an ‘Assets’ folder.

For this example the structure would be:

* Assets
	* Scripts
		* App
			* people.js
		* Model
			* Person.js
		* Utils
			* jquery.js
			* random.js
		* Require.js
		* main.js
		* app.build.js *(build script)*
		* r.js *(optimisation tool)*
	* Styles
		* layout.css
	* Images
	* Includes
	
In my main page I’ll insert the following script tag at the bottom of the page, just above the closing `</body>` tag…

```html
<script data-main="Assets/Scripts/main" src="Assets/Scripts/Require.js"></script>
```

…you’ll notice that we have specified a custom attribute on the script tag that points to our script folder and a file called ‘main’.

This does two things, it loads the Assets/Scripts/main.js file but it also tells RequireJs that all your other scripts are located in the Assets/Scripts folder as well.

### 2. Set-up your ‘main’ script

Inside main.js we have the following code…

```js
// This allows us to specify jQuery as a dependancy in one of our modules
// You'll notice all paths are relative to Assets/Scripts/
require.config({
    paths : {
        'jquery' : 'Utils/jquery'
    }
});

/*
    When we're defining a module we use the define() method.
    We'll see this used shortly.
    But as this is our main 'bootstrapping' script we're using the require() function instead.

    The require() function is similar to define() in that you pass it an optional array of dependencies, 
    and a function which will be executed when those dependencies are resolved. 
    However this code is not stored as a named module, as its purpose is to be run immediately.
*/

require(["App/people"], function(iCanCallThisAnythingILike) {
    // The argument passed through is the returned value from the function definition we defined inside App/people.js
    // In this case it was an object literal with two properties: 'list' & 'scripts'
    // If we had specified two dependancies then we'd pass through a second argument which again would be the return'ed value from that module
    
    console.log(iCanCallThisAnythingILike.list, iCanCallThisAnythingILike.scripts);
});
```

So far we have our main script loading in a single dependancy (Assets/Scripts/App/people.js).

Lets have a look at that dependancy…

### 3. Define your modules

Here is the content of our ‘Assets/Scripts/App/people.js’ file…

```js
/*
 * You see we've specified the jQuery library as a dependency without specifying its correct path (we've just specified the name 'jquery').
 * This is because jQuery's AMD support is based on it being defined as a 'Named Module'.
 * So if you look at the script main.js (which is our bootstrapper file) you'll see we've set the require.config() to include the full path to jQuery.
 */

define(["Models/Person", "Utils/random", "jquery"], function (Person, randomUtility, $) {

    // Notice that our dependancy modules(scripts) don't use the full path /Assets/Scripts/
    // This is because in our HTML page (where we loaded the RequireJs library) we had specified the main path already as a data- attribute on the script tag.
    // So RequireJs already knows that when we say 'Models/Person' we mean 'Assets/Scripts/Models/Person.js'

    var people = [],
        scriptsOnPage = $('script');

    people.push(new Person('Jim'));
    people.push(new Person(randomUtility.someValue));

    return { 
        list: people, 
        scripts: scriptsOnPage 
    };

});
```

Be aware that nothing should be declared outside of a single define() call. 

All our modules work in the same way. We have a define() function that specifies some dependancies and then runs a callback when those dependancies have loaded, and then finally your module can return a value, object, function …etc.

In the above example you can see that this module has loaded jQuery as a dependancy (I mention this for those of you who love jQuery and want to make sure you can keep using it if you move to AMD - which is a definite “yes”).

Have a look at the modules defined in ‘Models/Person.js’ and ‘Utils/random.js’ to see that they work the same way.

### 4. Run a build script

RequireJs provides you with an optimisation script which you can run from the command line (I use the Terminal on the Mac, but your mileage may vary on Windows or other OS’) which helps you concatenate your different modules into one script and then minifies it for you and exports this single file into a folder of your choosing so you can link to this new file rather than your original main.js file (like we have at the moment). You could set-up the build script to just export it in the same folder as your main.js file and simply call it main.min.js if you like - up to you.

To run the build script we’re using NodeJs (as recommended by the author of RequireJs), but you can use Java as well if that’s your preference. Have a look at the RequireJs site to find out about that or if you need more information on installing NodeJs (as that is outside the realms of this post).

I’ve placed my build script (and the optimisation script) in the my main scripts folder ‘Assets/Scripts’ as I like to keep everything together (I know some people prefer to have this optimisation script completely separate from their project files, so you can place it wherever you feel comfortable).

My build script looks like this…

```js
/*
 * http://requirejs.org/docs/optimization.html
 *
 * Use NodeJs to execute the r.js optimisation script on this build script
 * node r.js -o app.build.js
 *
 * See: https://github.com/jrburke/r.js/blob/master/build/example.build.js for an example build script
 *
 * If you specify just the name (with no includes/excludes) then all modules are combined into the "main" file.
 * You can include/exclude specific modules though if needed
 *
 * You can also set optimize: "none" (or more specific uglifyjs settings) if you need to.
 */

({
    appDir: "../../",
    baseUrl: "Assets/Scripts",
    dir: "../../project-build",
    modules: [
        {
            name: "main"
            /*
                include: ["App/people"],
                exclude: ["Utils/random"]
            */
        }
    ]
})
```

See the code comments above for the command you need to use to execute the build script using the optimisation tool.

As you can see above we could tell the build script to ‘include’ or ‘exclude’ specific modules. I’ve not needed to use this yet, but as I understand it this ‘excluding’ of modules is useful for debugging purposes (can anyone confirm this)?

The above build script only takes a second and it generates a lovely single script file called main.js and actually places it *outside* of my website directory into a folder called ‘project-build’ (i don’t like to risk accidentally over-writing my source JavaScript files… if you know what I mean).

What now?
---------

Well… start using AMD in your projects. 

The best way to learn it is to use it. Try out some of the basic structural changes to get yourself used to architecting your apps in a modular fashion, and then just go for it!

I’ve covered RequireJs here, but I’ve heard good things about [@unscriptable](http://twitter.com/unscriptable)’s Curl loader ([https://github.com/unscriptable/curl](https://github.com/unscriptable/curl)) so I’d recommend checking that out too.

Links/Reference Material
------------------------

* [https://github.com/amdjs/amdjs-api/wiki/AMD](https://github.com/amdjs/amdjs-api/wiki/AMD)
* [http://unscriptable.com/index.php/2011/03/30/curl-js-yet-another-amd-loader/](http://unscriptable.com/index.php/2011/03/30/curl-js-yet-another-amd-loader/)
* [http://wiki.commonjs.org/wiki/CommonJS](http://wiki.commonjs.org/wiki/CommonJS)