#Thoughts on current web development practices

Be warned, this is a 'long one', so go grab a cup of coffee… 

##Table of Contents

* Introduction
* HTML: the foundation
	* Example HTML
	* Semantic Elements
	* Brief Example of Media Queries
	* Better practice Media Queries
	* Mobile First Approach
* CSS: the styling
	* OOCSS & Sass
	* Modular Structure
	* Sass Example
	* Avoiding pre-processor pitfalls
	* How I write OOCSS
	* Selectors
	* Sprites
	* Responsive Design
	* Mobile Design: users on the move
	* Mobile First 
	* Tools
	* Linting via the Command Line
* JavaScript: the behaviour
    * AMD
    * Linting
    * Code Structure
    * Pure Functions
* Style Guides: keeping things consistent
* Testing: making sure stuff works
* Performance: running fast
* Version Control: keeping track of things
* Automation: making life easier
* Conclusion

##Introduction

I wanted to get down in writing my process for building websites using the *current* techology available to us (July 2012). It seemed like a good idea to share how I approach web development and to discuss the tools I use to help make my life easier. I've had a (un)fortunate history of being able to work on a few large scale applications that have turned out fine but have become a nightmare to maintain or scale - what this means is: I know what techniques just don't work *for me* and I'd like to share some insights as to what techniques and tools I think **do** work for getting your project off to the best start possible.

The beauty (or curse, depending on your perspective) of the web is that by the time you read this there may be better ways (again) of doing things. It wasn't long ago that some of the principles I'll be discussing today changed from how I used to do things (really they have only been progressively refactored to be more efficient, but some of the stuff I'll be talking about here have still only come onto my radar in the last year or so).

Ideally where you want to constantly be - doesn't matter how good you are (or think you are) - is at a stage where you're eager to improve your skill set. If you are passionate about what you do then this urge to learn should come easily.

For those of you who are on twitter/Google+ all day long and/or are at the cutting edge of all things web development (and even some of you who aren't) will probably yawn over a lot of what I'm going to be talking about (for example, talking about HTML and laying out a page can seem like a pretty bland subject I know).

This also isn't a 'beginners guide' or 'introduction to' style article, I wont be discussing how to write code in the languages that are mentioned, but if you do know everything there is to know about a particular section I've gone into then well, that's all good for you :-)

Now, some of the techniques and tools I'll be talking about just wont fit right for you and that's OK! You need to keep trying different things to see if they work *for you*. Just because everyone is using "Hot Sauce XYZ" software doesn't mean *you* have to. Just because "Hot Sauce XYZ" is the talk of the community and is heralded as a modern marvel doesn't mean *you* feel the same way about it. Just because I found "Hot Sauce XYZ" to be a life/time saver and I think it's the future of the web… doesn't mean *you* will agree or feel the same. That's OK! 

Take what you need and move on. Shall we begin?

##HTML: *the foundation*

> "Hamburgers! The cornerstone to any nutritious breakfast"

Well, the beginning of any web site (web page/app whatever you happening to be working on) is HTML.

Things have a come a loooong way since the good 'ole days of web development. Our HTML mark-up has become streamlined and easier to remember. Hell, I can now remember how to write my `doctype` without using a template file or Googling for the answer.

###Example HTML

Below is an example of a web page HTML structure…

```html
<!doctype html>
<!--[if IE 8]><html class="ie8" dir="ltr" lang="en"><![endif]-->
<!--[if IE 9]><html class="ie9" dir="ltr" lang="en"><![endif]-->
<!--[if gt IE 9]><!--> <html dir="ltr" lang="en"> <!--<![endif]-->

	<head>
		<meta charset="utf-8">
		<meta name="viewport" content="width=device-width, initial-scale=1">
		<meta name="author" content="Mark McDonnell" />
		<title>My thoughts on current web development practices</title>
		<!--[if lt IE 9]>
		<script src="/Assets/Scripts/Utils/Elements/html5.js"></script>
		<![endif]-->
		<link rel="author" href="/humans.txt" type="text/plain">
    	<link rel="stylesheet" href="Assets/Styles/mobile.css"  media="only screen and (min-width: 320px)">
        <link rel="stylesheet" href="Assets/Styles/tablet.css"  media="only screen and (min-width: 600px) and (max-width: 959px)">
        <link rel="stylesheet" href="Assets/Styles/desktop.css" media="only screen and (min-width: 960px)">
    	<!--[if (lt IE 9) & (!IEMobile)]>
    	<link rel="stylesheet" href="/Assets/Styles/desktop.css">
    	<![endif]-->
	</head>
	<body>
	    Content
	</body>
</html>
```

This breaks down into the following basic structure…

* Document Type
* Head
* Body

...we'll now discuss this a little more in depth, so we'll dip in and out of subjects (such as mobile and media queries etc) but effectively we're just taking a top level look around at what our HTML is doing and why.

The `doctype` lets the browser know how to render our content. We've all seen/written the hideously long doctypes, but this has been simplified in HTML5 to just `<!doctype html>` which makes it a lot easier to write (and to remember!).

We have some `meta` tags for…

* ensuring the browser renders the content with UTF-8 encoding:  
`<meta charset="utf-8">`
* set's the appropriate scale of the viewport on a mobile device:  
`<meta name="viewport" content="width=device-width, initial-scale=1">`
* confirms who the author of the page is/was:  
`<meta name="author" content="Mark McDonnell" />`

We then have a Microsoft 'conditional comment' which says if the browser is less than Internet Explorer version 9 then load a JavaScript file that helps those browsers understand the rendering of HTML5 tags. This can be omitted if you aren't going to use the newer HTML5 tags (such as: `<article>`, `<section>`, `<aside>` etc).

Next we have a line of code which lets the host know there is a `humans.txt` file available. The point of this file is to inform the user who built the site (similar to how a `robot.txt` file informs a Search Engine robot what can/can't be indexed). There are no strict rules on how the content of this file should be laid out, but I generally use:

```
/* the humans responsible & colophon */
/* humanstxt.org */

/* TEAM */
Company Name: Developer A, Developer B, Developer C
Site: http://www.domain.tld/
Twitter: http://twitter.com/username
Location: Address
```

###Brief Example of Media Queries

After this we link to some CSS files. These stylesheets apply styles based on certain 'conditions' (known as 'Media Queries') such as the width of the user's device. 

So for example, if the user's device is 960px wide then we make the assumption that they must be on a desktop computer (or potentially a large tablet device) and thus load styles specific to the device so the website can look its best on that device. 

Yes this 'assumption' about the users device screen can be problematic as it relies on the assumption that devices are of a known width - and obviously if history has taught us anything - these assumptions will fail at some point in the future. 

###Better practice Media Queries

While we're here I wanted to suggest a better practice for writing Media Queries which is to not have stylesheets load based on the dimensions of current devices, but instead to set-up 'break points' for where your site starts to naturally break down. 

So for example, rather than trying to target an iPhone (which you know has a dimension of xyz), instead look at your site and think "hmm, my site looks a bit crap when smaller than x width - I'll set a break point for that dimension". 

This is still a bit 'hit and miss', as you might decide your site doesn't look good at x pixels wide but that means certain devices either jump up/down a stylesheet when you didn't expect them to. But as long as you're using an 'adaptive' approach to loading your CSS then that can ultimately only be a good thing for your users compared to loading the desktop variation of your site's design on a smaller screen mobile device.

###Mobile First Approach

The stylesheets in this example are using a 'mobile first' approach, and by this I mean we're building a site from the mobile design upwards. If we didn't do this then we would end up loading lots of CSS onto the mobile device that was actually only needed for the desktop computer (this is because the desktop version of the site would have a more complicated design compared to the design for the mobile version of the site). I'll go into this in more detail later but it's important to note here because after the stylesheet links you'll see another 'conditional comment' `<!--[if (lt IE 9) & (!IEMobile)]>` which is there because Internet Explorer browsers less than version 9 do not understand CSS Media Queries and so if we take a mobile first approach then IE < 9 wont have any stylesheet to load (as it wont know 'how' to load them). So this conditional comment is a 'fallback' for those browsers. So if a user is on an older crappy Windows based phone then they'll at the very least get the desktop version of the site loaded (which is still likely to be a better experience than having no styling on the content at all).

###Semantic Elements

After that, we have the `<body>` element which will contain the content of your page.

It's important that you use only elements that semantically match the content. There are easy examples and there are slightly more awkward examples. An easy example is a top level navigation menu, I think most of us are aware by now that the most semantically correct element to use here is an un-ordered list `<ul>`…

```html
<ul>
	<li><a href="">Home</a></li>
	<li><a href="">About</a></li>
	<li><a href="">Services</a>
		<ul>
			<li><a href="">Service A</a></li>
			<li><a href="">Service B</a></li>
			<li><a href="">Service C</a></li>
		</ul>
	</li>
	<li><a href="">Contact</a></li>
</ul>
```

A slightly more awkward example though: imagine we have one of those large 'web 2.0' (how old does that sound already!) footer areas where the designer has a massive list of helpful links and contact information. The structure of that might be something like this…

Services     | Social        | Contacts
------------ | ------------- | ------------
Service A    | Facebook      | 00000 000000
Service B    | Twitter       | 11111 111111
Service C    | Google+       | 22222 222222


…you *could* use a `<dl>` element, but is this strictly the correct element to use? An example of what that would look like is…

```html
<dl>
	<dt>Services</dt>
		<dd>Service A<dd>
		<dd>Service B<dd>
		<dd>Service C<dd>
	
	<dt>Social</dt>
		<dd>Facebook<dd>
		<dd>Twitter<dd>
		<dd>Google+<dd>
	
	<dt>Contacts</dt>
		<dd>00000 000000<dd>
		<dd>11111 111111<dd>
		<dd>22222 222222<dd>
</dl>
```

…if we look at the specification for this element it says… 

> The dl element represents an association list consisting of zero or more name-value groups (a description list). Each group must consist of one or more names (dt elements) followed by one or more values (dd elements). Within a single dl element, there should not be more than one dt element for each name.

…so this looks to be the right element to use as it is an "association list" (i.e. we've associated a list of items with the relevant title) but maybe instead we should have used a `<table>` element because we are kind of dealing with tabular data. There are defined headers with *associated* rows of content (similar to the association made with a `<dl>`). An example of what this would look like is…

```html
<table>
    <thead>
        <tr>
            <th scope="col">Services</th>
            <th scope="col">Social</th>
            <th scope="col">Contacts</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Service A</td>
            <td>Facebook</td>
            <td>00000 000000</td>
        </tr>
        <tr>
            <td>Service B</td>
            <td>Twitter</td>
            <td>11111 111111</td>
        </tr>
        <tr>
            <td>Service C</td>
            <td>Google+</td>
            <td>22222 222222</td>
        </tr>
    </tbody>
</table>
```

…this is a bit of an anal analysis of 'semantic' usage, but the fact is this sort of thing happens all the time. We look at something in a design and try to think of the most semantic element to use and although `<dl>` seems like the right choice, is it really? I guess it depends on how you interpret the specification. I know I've used the `<dl>` elements many times but I sometimes wonder if I shouldn't have just gone with the `<table>` element instead (on a side note: I'd love to hear people's feedback on this)

###Automation

I've not discussed it here but one thing we'll come back to later in this article is the idea of 'automation' - so that your 'generic' HTML never need to be typed out by hand again. You should either have a re-usable template project/file or use a generator system that handles it for you.


##CSS: *the styling*

###OOCSS & Sass

CSS is the one area of web development that has been changing pretty much constantly for me these past couple of years. 

I've been trying to settle on a technique or style that would mean the maintenance of my projects are made easier & simpler, and I now believe I've found (at least so far) one such method that appears to work both in favour of maintainability and scalability: 'OOCSS' (Object-Oriented CSS).

The principles vary depending on the author (e.g. [stubbornella](https://github.com/stubbornella/oocss/), [SMACSS](http://smacss.com/), [the single responsibility principle](http://csswizardry.com/2012/04/the-single-responsibility-principle-applied-to-css/) and many more), and my way of utilising OOCSS is a bit of an amalgamation of the existing styles available today. Also, I utilise the CSS pre-processor [Sass](http://sass-lang.com/) which originally I wasn't too keen on (I saw it in a similar light to tools like jQuery where it wasn't necessary, and caused more problems than it solved) but I'm now more sold on it's benefits, and because of my skeptical mindset I know what issues can occur when using a pre-processor and how to avoid those issues.

###Modular Structure

Here follows is my current CSS/Sass structure…

* /`Assets`/
    * /`Styles`/
        * /`Lint`/
        	* csslint.txt (*this contains my generic terminal command for executing CSS linting*)
        * /`Sass`/
            * /`Base`/
            	* `_additions.scss`
            	* `_normalize.scss`
            	* `_placeholder.scss`
            	* `_theme.scss`
            * /`Configurations`/
            	* `_variables.scss`
            * /`Functions`/
            	* `_calcems.scss`
            	* `_calcpercentage.scss`
            	* `_fontsize.scss`
            * /`Layouts`/
            	* `_container.scss`
            	* `_grid.scss`
            	* `_matrix.scss`
            * /`Modules`/
            	* /`Components`/  
		`_button.scss`  
            	`_media.scss`
            	* /`Extensions`/  
            	*this contains _{n}.scss files that hold re-usable classes*
            	* /`Helpers`/  
            	`_clearfix.scss`  
            	`_hidetxt.scss`  
            	`_horizontal.scss`  
            	`_push.scss`
            	* /`Mixins`/  
            	`_border.scss`  
            	`_boxsizing.scss`  
            	`_radius.scss`  
            	`_shadow.scss`  
            	`_transform.scss`  
            	`_transition.scss`
            	* *this contains .scss files specific to this project*
            * /`Plugins`/
            	* `_slimbox.scss`
            * /`Queries`/
            	* `_320-home.scss`
            	* `_600-home.scss`
            	* `_960-home.scss`
		* `home.scss`
		* `sub_page.scss`
		* `another_page_type.scss`
	* `home.css`
	* `sub_page.css`
	* `another_page_type.css`


…now this may look like a **lot** of style sheets, but the beauty of using a pre-processor such as Sass is that it allows you to have a very modular code base and yet for deployment/production (e.g. the pushing of your code to the 'live' server) you can *build* your separate code modules into a single (compressed/minified) stylesheet.

###Sass Example

So where you see the `.css` files inside of the /`Styles`/ directory - this is actually a single file that has been built up from selected Sass files.

For example, the home.scss file (which is what generates the home.css file) could contain the following content (note: that this content changes depending on the requirements of your project)…

```css
// Configurations/Settings
@import "Configurations/variables";

// Functions
@import "Functions/calcems";
@import "Functions/calcpercentage";
@import "Functions/fontsize";

// Base
@import "Base/normalize";
@import "Base/additions";
@import "Base/theme";

// Layouts
@import "Layouts/grid";
@import "Layouts/container";

// Modules
    
    // Mixins
    @import "Modules/Mixins/radius";
    @import "Modules/Mixins/transition";

    // Components
    @import "Modules/Components/media";
    
    // Helpers
    @import "Modules/Helpers/clearfix";
    @import "Modules/Helpers/hidetxt";
    @import "Modules/Helpers/horizontal";

// Generic Styles for Modules that appear across the site (e.g. header/navigation/footer)
@import "Modules/header";
@import "Modules/navigation";
@import "Modules/footer";

// Page Specific Styles
@import "Modules/Queries/960-home";
```

…so let's go over a couple of points about this Sass file.

1. I'm using Sass to import components/layouts/modules that are relevant for the home page.
2. Something I won't go into detail on here - but I use a fair amount and find very useful are - Sass 'functions' ([take a look at some custom functions](https://github.com/Integralist/Passage/tree/master/public/Assets/Styles/Sass/Functions))
3. This style sheet is constructed of modules required for the home page stylesheet - so you can see I'm importing not only modules that are part of my 'templated' project but also modules that I've created specifically for this project (e.g. the header, navigation, footer modules).
4. In this project I was also using a 'Mobile First' approach and so you can see in this file I'm also importing a module which specifically handles the styles for the desktop experience.

Initially it seems like a bit of work is involved, but once you get used to structuring your stylesheets with the relevant modules the job becomes a lot easier to manage in the long run - no more do you need to sift through hundreds/thousands lines of CSS code looking for a particular rule; because your code is modular you now know where to look to find it and can access it a lot quicker, along with the ability to more easily re-use code (as long as you've taken an OOCSS approach that is!) 

There are many benefits to having a modular code base, but the most important thing to remember is: don't start thinking that because you're using Sass that it will some how magically make you write better code, it wont - that is still *your* responsibility. 

###Avoiding pre-processor pitfalls

Now before we get into OOCSS let's first quickly demonstrate some issues with using pre-processors.

Here is a typical usage of Sass:

```css
.nav{
    li{
        a{}
    }
}
```

Which when the pre-processor executes equates to...

```css
.nav {}
.nav li {}
.nav li a {}
```

But this might not have been the result you intended, instead you actually wanted…

```css
.nav {}
.nav li {}
.nav a {}
```

…which would have better performance and be more 'specific' to your DOM structure.

To do that you just need to tweak the Sass code slightly…

```css
.nav{
    li{
        
    }
    a {
        
    }
}
```

So just be careful with Sass and double check its output to ensure it is producing the selectors/rules you actually want. 

One tip for using nested selectors is to use the ampersand `&` character to reference the whole selector, so for example…

```css
a {
	text-decoration: none;

	// this will be compiled to 
	// a:hover, a:focus
    &:hover,
    &:focus {
        color: $brand-color;
    }
}

.rate-options {
    counter-reset: rateoption;
    list-style: none;
    margin-top: 2em;
    
    li {
        margin-right: 1.5em;
    
    	// this will be compiled to 
    	// .rate-options li:before
        &:before {
            color: $action-color;
            content: counters(rateoption, "") " ";
            counter-increment: rateoption;
        }
    }
}
```

Other issues with pre-processors can come from Mixins, which although very useful, are also a waste of time/space if good OOCSS is already in place. Remember that the content of a Mixin is copied into every rule you specify it to be used. 

The `@extend` statement is slightly better in the sense that you can write a class `.funky-border` which contains a single declaration: `border: 10px dashed blue;` and then for every element that uses that same styling you can simply do…

```css
.my-box {
	@extend .funky-border;
	color: red;
}
``` 

…what this ends up compiling to is something like this…

```css
.funky-border,
.my-box {
	border: 10px dashed blue;
}

.my-box {
	color: red;
}
```

…again, looks great but you need to be careful because in Internet Explorer there is a limit to the amount of selectors you can specify for a single rule! So if you were using `@extend` everywhere you might hit an issue (unlikely you'll reach that limit, but on a application big enough and with bad architecture in place that could still happen).

There is another more complicated example given within the 'performance' section of this article which discusses more structural concerns, but we'll come to that soon enough.

###How I write OOCSS

This is where things get interesting because OOCSS is still (although around for a few years now) a moving target and something that isn't a straight forward "*this is how it's done*" process.

I have the following terminology I use when discussing OOCSS…

Term        | Description
------------| -----------
'base'      | normalisation of styles across browsers + base styles specific to current project
'layout'    | structure of site (think of grid systems: they do nothing but give you a structure to place content inside)
'module'    | this is a complicated one because it means different things in different contexts (see below for more details)
'extension' | this is a class that can be applied to multiple modules/elements
'state'     | can be applied to both layouts and modules to affect how they look

####Base

My 'Base' styles cover two areas: 

1. Normalisation of browser quirks/formatting (handled by [@necolas](http://twitter.com/necolas)' [Normalize.css](http://necolas.github.com/normalize.css/))
2. Base level rules which are specific to the current project

So for example I have the following extra stylesheets in my `/Base/` folder: `_theme.scss` and `_placeholder.scss`.

My `_theme.scss` stylesheet is for base styles for the current project, so it might include a font-face used in the site's design, or setting all `<h1>`'s to have a standard brand colour (things of that nature).

My `_placeholder.scss` is used in conjunction with my the `/Scripts/Utils/Polyfills/placeholder.js` script which makes it possible for an input to be one colour (to signify a 'hint') but change to a different colour when the user focus' on the input (this is only used for browsers that don't natively support the `placeholder` attribute. Because placeholders appear quite heavily in a lot of the designs created (by the agency I work for), it became apparent that I should store these styles into their own stylesheet rather than repeating them for every project.

Eagle eye readers will probably notice that in my folder struture above I also had an `_additions.scss` stylesheet which consists of some extra base rules that `Normalize.css` doesn't cover. You'll see later in the section about 'automation' that I've now started using Git/GitHub to manage 3rd party code dependancies and so really I could add my 'additions' into the one Normalize.css file and then if Normalise.css ever had an update I could pull in those changes from GitHub and merge them into my version of Normalise, that way I wouldn't override my version completely but just integrate the latest updates. But for the time being, because Sass concatenates all stylesheets into one, it doesn't really matter that this `_additions.scss` is a separate file.

####Layout

These are the easiest component in the CSS stack so far. Layouts hold content. They don't try to style content in any way, they literally just contain a module or content of some kind.

Layouts can be manually created or they can be created using a grid system (see: [960 CSS Grid System](http://960.gs/)). I use a fork of Twitter's Bootstrap Grid that was originally written in [Less](http://lesscss.org/) but some kind soul had ported over to Sass (I modified it slightly to not include 'gutters' - as most of my grid layouts don't require gutters).

I'm still a bit skeptical of using 'grids' because of the extra overhead as far as HTML is concerned but they can be pretty useful when used sparingly and in the right situations. [Harry Roberts](http://csswizardry.com/) once told me to think of grids as 'shelves' which you place items upon, and that's pretty much an accurate portrayal of the role grids take - nothing more complicated than that really.

####Modules?

The word 'module' in my CSS means different things, mainly because it refers to something as being 'modular'.

The key to having a more modular code base is: 'abstraction' - taking a common design pattern and abstracting it into a self contained re-usable component.

Two prolific 'abstractors' are Nicole Sullivan (see: the ['media object'](http://www.stubbornella.org/content/2010/06/25/the-media-object-saves-hundreds-of-lines-of-code/)) and Harry Roberts (see: the ['island object'](http://csswizardry.com/2011/10/the-island-object/) and ['nav abstraction'](http://csswizardry.com/2011/09/the-nav-abstraction/)).

All of the following items are 'modular' in that sense (e.g. when it comes down to it, they are just *categorised* abstractions):

* Components (e.g. 'the media object')
* Extensions (these are project specific CSS classes that i use with Sass' `@extend` feature)
* Helpers (these are project agnostic, meaning they can be used in any probject - e.g. 'the nav abstraction')
* Mixins (these are specific to Sass in that they save you typing out the same content over and over - these can be avoided in most cases!)

Example Mixin:

```css
@mixin transition ($transition: all 0.2s linear) {
	-webkit-transition: $transition;
	   -moz-transition: $transition;
	    -ms-transition: $transition;
	     -o-transition: $transition;
	        transition: $transition;
}
```

####Extensions

Extensions are effectively re-usable classes which you can incorporate into any existing rule. An example of this could be having a class that lets you set a box shadow…

```css
.shadow {
    @include shadow(0 3px 6px #666);
    border: 3px solid #fff;
}
```

…which would be used like so… 

```css
.profile-photo {
    @extend .shadow;
    // other styles
}

.company-logo {
    @extend .shadow;
    // other styles
}
```

…you could argue that this type of class should be incorporated into a 'module' - and most of the time that is correct - but this isn't *always* appropriate.

####State

State classes are pretty self-explanatory in that they affect the state of an object/element, and the syntax is `.is-xxxx`.

For example, if you have an element that had both a hidden state and a visible state (and JavaScript was used to switch states) then the visible state would be the default (because if the user had no JavaScript support then you don't want the content to be hidden by default) and you could trigger the alternative state by applying a class like `.is-hidden`.

I used to have a separate `State.scss` file but I found that was a nightmare to maintain because I'd have a module in one file and a specific 'state' for that module in another file, and then on top of that I used to have my Internet Explorer fixes in separate `IE8.css` and `IE7.css` files and so if there were any tweaks needed to a module for IE then I'd have those in separate files as well, it was a total nightmare.

So now I find the best way to manage modules is to keep the module, and the state for that module, and any IE tweaks for that module ALL within the module file.

The way I keep the IE code within the same file is I stopped having them as separate files like so…

```html
<!--[if IE 8]>
<link rel="stylesheet" href="/Assets/Styles/IE8.css">
<![endif]-->

<!--[if IE 7]>
<link rel="stylesheet" href="/Assets/Styles/IE7.css">
<![endif]-->
```

…and instead went with the [Paul Irish solution](http://paulirish.com/2008/conditional-stylesheets-vs-css-hacks-answer-neither/)…

```html
<!--[if IE 8]><html class="ie8" dir="ltr" lang="en"><![endif]-->
<!--[if IE 9]><html class="ie9" dir="ltr" lang="en"><![endif]-->
<!--[if gt IE 9]><!--> <html dir="ltr" lang="en"> <!--<![endif]-->
```

…yes doing it this way *can* mean the overall file size of your CSS is larger for browsers that aren't affected by the IE specific code, but to be honest since dropping support for IE7 the amount of IE fixes needed has literally dropped off the radar!

###Selectors

Selectors are a tricky subject in CSS. The key principle is to be as 'specific' as possible.

Where you may have `ul.menu` just use `.menu`. This is because if you had another element which isn't a `ul` but which needed similar stylings to your `.menu` class you can now reuse that class, where as before you would of had to of either written another class with similar code, or added another element selector to the rule like so…

```css
ul.menu,
ol.menu {
    // styles
}
```

…both of which are bad situations, so just be specific (where possible/reasonable).

For a more indepth analysis of how best to write your selectors then I recommend you read ['Shoot to Kill'](http://csswizardry.com/2012/07/shoot-to-kill-css-selector-intent/) by Harry Roberts. In that article he gives us a good way to question our selectors…

> Ask yourself: am I selecting this because it’s a ul inside of .header or because it is my site’s main nav?

Using this type of questioning we know when it's OK to not be too specific (e.g. `.header > ul`) and when we *must* be specific to avoid changes in the future causing us problems.

###Sprites

The idea behind sprites is pretty straight forward. Take all the little icons or miscellaneous imagery and stick them in one single image file, like so…

![](http://f.cl.ly/items/1Z3M010W210t25130P2h/apple.jpg)

…now you have helped towards your performance goal of '[reducing HTTP requests](http://developer.yahoo.com/performance/rules.html#num_http)' because instead of having (using the above image as an example) 20 different images, meaning 20 separate HTTP requests, you now have just one HTTP request.

You could argue the overall size of the image sprite can become very large and some pages might not use all the icons/images within the sprite, but by that point the sprite would have been loaded and cached by the browser and so that argument becomes redundant.

####Image Source Files

One other important thing to do (and there was no where else appropriate for me to mention this) is to have a `/Source/` folder inside your `/Images/` folder to hold all the source files (same as if you had a `/Flash/` folder for your animations then you should have a sub folder inside that called `/Source/` to hold all the `.fla` files). 

This may seem over kill and I can hear people crying out already about using up loads of server space on these types of files that aren't used by the website in any way, but hear me out! 

In modern web development there isn't usually a whole lot of these types of files (and by these files I mean `.fla` or the source `.png` for an image sprite) - items that are likely to need changing in the future and I don't mean keep hi-res photos or the original Illustrator/Photoshop design files. 

The biggest benefit I've found is when going back to a website after (let's say) two years! You had all the source files on a backup server somewhere and now you have to go find it. Maybe it's in the loft, or locked in a fireproof safe at your work office, either way it's a pain in the ass to get to and try and locate these files when you could just connect to the web server and find them there alongside your website files!

###Responsive Design

The principle solution to making your site design responsive is to not set widths on anything. Depending on the design of your site the layouts will need a width set - for example a two column layout needs to have a width set on each column (you can't avoid that), but any modules you have shouldn't have a width set on them.

Anything you do set a width on needs to be in percentages.

Here is an example of how you can handle this using the two column layout example above…

* add two `<div>`'s that will be your columns
* add a width to both columns in pixels
* float the columns so they sit next to each other
* now convert the pixels into percentages using the following algorithm `target / context = result`.

So for example, if you set the width of the left `div`'s to be 150px and the `div`'s containing element happened to be a `<div>` with a width of 960px, then I would calculate the width of my left column like so: 

`150 / 960 = .15625`. 

But with percentages you wouldn't just set the width to `.15625%` you need to move the decimal place over by two places so it becomes `15.625%`. 

`target / context = result` is *THE* algorithm for responsive design!

####Responsive Images

To help your images scale appropriately along with your responsive design you can set the `max-width` property to be 100% which means the image will never be larger than its container but can happily resize/scale downwards on smaller screens…

```css
img {
    max-width: 100%;
}
```

####What about containing elements with unknown widths?

Normally what you see in web design is the main website content is wrapped in an element such as a `<div>` with a set `max-width` of 960px (so it fits onto a range of devices without requiring a horizontal scrollbar to appear). We use `max-width` instead of `width` for the same reason we use it for our responsive images, so it never gets larger than it should but can happily resize downwards depending on the size of the device viewing it.

But how do we convert 960px into a responsive unit? We know the algorithm for responsive design is `target / context = result` but we have no container in this instance (unless we count the `<body>` tag)? But then what's the width of the `<body>` tag? We don't know because it'll be different on every device!

Well there is one way to get 960px into a responsive unit and that is to take into account that the default font size on nearly all browsers is 16px. If you set `font-size: 100%` onto the `<body>` element then this equates to a font size of 16px.

This allows us to calculate the 960px `max-width` as 60em! 

By doing: `960 / 16 = 60` (again it's that `target / context = result` algorithm!)

We can now set our wrapper element like so…

```css
.container {
    max-width: 60em;
}
```

###Mobile Design: *users on the move*

Because I make sure my sites are built using a responsive approach, it isn't a whole lot of extra work to tweak the styles/design to look more appropriate for devices with smaller screen dimensions. 

The tool for that job are 'Media Queries'…

```css
@media only screen and (min-width: 320px) {
	// Mobile styles
}

@media only screen and (min-width: 600px) and (max-width: 959px) {
	// Tablet styles
}

@media only screen and (min-width: 960px) {
	// Desktop styles
}
```

…yes I know these examples are *conveniently* matching the dimensions of an iPhone and iPad - which is a bad thing! What you ideally want to do is target good 'break-points' in your design and not screen dimensions because let's face it: we know better by now that these assumptions will fail us in the future.

When you have your media queries in place you can amend your designs so they are easier to view on those smaller screen dimensions.

###Mobile First

Building sites that *look* good on mobile devices is one thing, but the performance of those sites will be worse than those built to take a 'mobile first' approach.

The 'mobile first' approach is effectively building the site to work for mobiles first and foremost, and to work your way up to the desktop version.

The beauty of this approach is that the DOM structure is clean and uncluttered meaning the browser has less to render and if there is any JavaScript interaction then a smaller DOM means your JavaScript can sift through the DOM more easily. Also, you don't end up downloading unnecessary assets on a smaller/less powerful device (e.g. if you built your site for desktop and then added Media Queries to make it *look* good on mobile devices then you're still loading desktop related stylesheets onto a mobile device which doesn't need them).

The 'mobile first' approach can be very strange and uncomfortable, but I guess that - like anything - if you do it enough then the benefits will shine through and you'll wonder how you did things any other way.

###Tools

There is only really one CSS tool I use and that is a [CSS gradient generator](http://www.colorzilla.com/gradient-editor/) from ColorZilla. The reason I use it is because it generates a gradient based on an image! So I can take our designers files and then export a slice of any gradient used, upload it and get CSS generated for me that perfectly matches the gradient the designers have used.

###Linting via the Command Line

Linting your code is very important. Linting helps highlight problems and potential bugs in your code.

There is an online tool called [CSS Lint](http://csslint.net/) that lets you paste your stylesheet code into a field and have it return results on its quality (based on specific 'rules' that you would like it to abide by).

I use CSS Lint as my linting tool of choice for CSS. The only problem is that it's a pain to have to copy/paste my code every time I want to lint it. So instead, I use the [command line interface](https://github.com/stubbornella/csslint/wiki/Command-line-interface) version of the online tool as it's the easiest/quickest way to lint my code (and again, this is where using tools that make your job easier comes into play - why copy/paste the code from EACH of my CSS files into an external website when I can run a single command in my terminal which lints all of the files for me).

To install the command line version of CSS Lint you'll need [NodeJs](http://nodejs.org/) installed.

To install simply run this line via your command line: `npm install -g csslint`.

This means you can now use your command line to navigate to your CSS folder and lint it.

There are different options/ways to use the command line interface of CSS Lint, and they are as follows…

```
csslint [options] [file|dir]*
csslint file1.css file2.css
csslint ./
csslint --errors=box-model,ids test.css // => decide what should be errors
csslint --warnings=box-model,ids test.css // => decide what should be warnings
```

Below is my general usage command (which I keep in a txt file inside my `/Styles/Lint` folder for quicker copy/pasting into the command line interface): 

```
csslint --errors=import,compatible-vendor-prefixes,display-property-grouping,overqualified-elements,fallback-colors,duplicate-properties,empty-rules,gradients,universal-selector,vendor-prefix,zero-units --warnings=important,known-properties,font-sizes,outline-none,shorthand,unqualified-attributes ./
```

When I run that command I see any bugs/issues with my CSS code that doesn't appear to abide by the 'rules' specified via the command line options.


##JavaScript: *the behaviour*

Wow, have we really managed to get through the CSS section!?

OK, JavaScript, here we go…

###AMD

I guess the best place to start is with a good foundation and that is: AMD

> The Asynchronous Module Definition (AMD) API specifies a mechanism for defining modules such that the module and its dependencies can be asynchronously loaded. This is particularly well suited for the browser environment where synchronous loading of modules incurs performance, usability, debugging, and cross-domain access problems.

…what this effectively means is rather than having one monolithic script file (while you're developing) you instead load a 'bootstrapper' file which then loads in the relevant modules that make up the functionality of the page and each module has its own set of dependancies which are also loaded by the module.

This gives you a modular code base that is easily re-usable because each 'module' (and its specified dependancies) should be completely transferable between projects. I have [a whole load of modules](https://github.com/Integralist/Passage/tree/master/public/Assets/Scripts/Utils) that I use across lots of different projects and I'm adding to them all the time.

Because modules aren't natively supported in browsers yet (they're in discussion for ES6) then AMD is the next best thing. To use AMD you need a module loader and that's where [RequireJs](http://requirejs.org/) comes in - there are other AMD loaders available but I prefer to use RequireJs. I won't go into the details of how to use RequireJs as [I've already written about it in the past](https://github.com/Integralist/Blog-Posts/blob/master/Beginners-guide-to-AMD-and-RequireJs.md) but suffice to say it makes it very easy to work on a large scale project and to deploy your JavaScript as a single minified file.

That's right, using modules is great but you still need to consider performance (all those HTTP requests aren't good for you) so RequireJs provides a handy build tool that lets you use NodeJs (or Java) to concatenate and minify all your modules into a single file. I've said it before and I'll say it again: I appreciate that some people think that the overall file size could be worse than loading multiple files but when I consider low connectivity devices the idea of loading one single file just feels better to me. 

The great thing to remember about modules is the maintainability of the project and the re-usable code you get from it.

###Linting

Similar to my CSS work flow (see above), I use the command line to lint my JavaScript code - which helps me find silly errors in my code before I go off to production.

There is an online tool called [JS Hint](http://www.jshint.com/) that lets you paste your JavaScript code into a field and have it return results on its quality (based on specific 'rules' that you would like it to abide by).

I use JS Hint as my linting tool of choice for JavaScript. The only problem is that it's a pain to have to copy/paste my code every time I want to lint it. So instead, I use the [command line interface](https://github.com/jshint/node-jshint) version of the online tool as it's the easiest/quickest way to lint my code (and again, this is where using tools that make your job easier comes into play - why copy/paste the code from EACH of my JavaScript files into an external website when I can run a single command in my terminal which lints all of the files for me).

To install the command line version of Js Hint you'll need [NodeJs](http://nodejs.org/) installed.

To install simply run this line via your command line: `npm install jshint`.

This means you can now use your command line to navigate to your CSS folder and lint it.

There are different options/ways to use the command line interface of CSS Lint, and they are as follows…

```
jshint path path2 [options] // => run against specific scripts
jshint *.js // => run against all scripts
jshint main.js --show-non-errors // => show non-errors (e.g. Implied globals etc)
jshint main.js --config ./Lint/config.json // => use specific configuration options
jshint main.js --show-non-errors --config ./Lint/config.json // => example of showing non-errors against specific configuration settings
```

Below is my general usage command (which I keep in a txt file inside my `/Scripts/Lint` folder for quicker copy/pasting into the command line interface): 

```
jshint **/*.js --config ./Lint/config.json
```

...this relies on a specific `config.json` file which is easier than manually typing all the options. It looks like this:

```js
{
	// Settings
    "passfail"      : false,  // Stop on first error.
    "maxerr"        : 200,    // Maximum error before stopping.


    // Predefined globals whom JSHint will ignore.
    "browser"       : true,   // Standard browser globals e.g. `window`, `document`.

    "node"          : false,
    "rhino"         : false,
    "couch"         : false,
    "wsh"           : false,   // Windows Scripting Host.

    "jquery"        : true,
    "prototypejs"   : false,
    "mootools"      : false,
    "dojo"          : false,

    "predef"        : [  // Custom globals.
    	// this is because we use require() from RequireJS library
        "require",
        "define",
        
        // this is because we use Jasmine BDD for unit-testing
        "jasmine",
        "describe",
        "beforeEach",
        "afterEach",
        "it",
        "expect"
    ],


    // Development.
    "debug"         : false,  // Allow debugger statements e.g. browser breakpoints.
    "devel"         : false,  // Allow developments statements e.g. `console.log();`.


    // ECMAScript 5.
    "es5"           : true,   // Allow ECMAScript 5 syntax.
    "strict"        : false,  // Require `use strict` pragma  in every file.
    "globalstrict"  : false,  // Allow global "use strict" (also enables 'strict').


    // The Good Parts.
    "asi"           : false,  // Tolerate Automatic Semicolon Insertion (no semicolons).
    "laxbreak"      : true,   // Tolerate unsafe line breaks e.g. `return [\n] x` without semicolons.
    "bitwise"       : true,   // Prohibit bitwise operators (&, |, ^, etc.).
    "boss"          : true,   // Tolerate assignments inside if, for & while. Usually conditions & loops are for comparison, not assignments.
    "curly"         : true,   // Require {} for every new block or scope.
    "eqeqeq"        : true,   // Require triple equals i.e. `===`.
    "eqnull"        : false,  // Tolerate use of `== null`.
    "evil"          : false,  // Tolerate use of `eval`.
    "expr"          : false,  // Tolerate `ExpressionStatement` as Programs.
    "forin"         : false,  // Tolerate `for in` loops without `hasOwnPrototype`.
    "immed"         : true,   // Require immediate invocations to be wrapped in parens e.g. `( function(){}() );`
    "latedef"       : true,   // Prohipit variable use before definition.
    "loopfunc"      : false,  // Allow functions to be defined within loops.
    "noarg"         : true,   // Prohibit use of `arguments.caller` and `arguments.callee`.
    "regexp"        : false,  // Prohibit `.` and `[^...]` in regular expressions.
    "regexdash"     : false,  // Tolerate unescaped last dash i.e. `[-...]`.
    "scripturl"     : true,   // Tolerate script-targeted URLs.
    "shadow"        : true,   // Allows re-define variables later in code e.g. `var x=1; x=2;`.
    "supernew"      : false,  // Tolerate `new function () { ... };` and `new Object;`.
    "undef"         : true,   // Require all non-global variables be declared before they are used.


    // Personal styling preferences.
    "newcap"        : true,   // Require capitalization of all constructor functions e.g. `new F()`.
    "noempty"       : true,   // Prohibit use of empty blocks.
    "nonew"         : true,   // Prohibit use of constructors for side-effects.
    "nomen"         : true,   // Prohibit use of initial or trailing underbars in names.
    "onevar"        : false,  // Allow only one `var` statement per function.
    "plusplus"      : false,  // Prohibit use of `++` & `--`.
    "sub"           : false,  // Tolerate all forms of subscript notation besides dot notation e.g. `dict['key']` instead of `dict.key`.
    "trailing"      : false,  // Prohibit trailing whitespaces.
    "white"         : true,   // Check against strict whitespace and indentation rules.
    "indent"        : 4,      // Specify indentation spacing
    "smarttabs"		: true	  // Suppress warnings about mixed tabs and spaces
}
```

When I run that command I see any bugs/issues with my JavaScript code that doesn't appear to abide by the 'rules' specified via the command line options. If I see nothing then that means there were no errors and I'm good to go.

###Code Structure

One thing I find very useful to do for all my JavaScript files is to include a code structure comment at the top like so...

```js
/*
 * Code Structure:
 * - Variables
 * - Functions
 *   - fn_name_1
 *   - fn_name_2
 *   - fn_name_3
 *   - fn_name_4
 *   - fn_name_5
 *   - fn_name_6
 *   - fn_name_7
 *   - fn_name_8
 *   - fn_name_9
 * - Initialisation
 */
```

...this makes understanding where specific code is in the file a lot easier (at a glance). As you can see this code structure follows my [JavaScript Style Guide](https://github.com/Integralist/Style-Guides/blob/master/JavaScript%20Style%20Guide.md) which goes into more detail about the specifics of my JavaScript code structure.

###Pure Functions

Something else I've started to do more recently - and this was something I used to avoid in the past as I was 'scare mongered' into believing it was a performance concern - is the idea of making more use of functions in a 'pure' style, and by this I mean writing functions that do one thing and one thing only without causing side effects.

Writing functions that only do one thing - rather than 5 or more different things - make it much easier to write unit-tests for but also makes it a hell of a lot easier to just debug in general.

On top of that it just *feels* cleaner. A bit *Zen* like and how they say if you clear your desk then you've cleared your mind. I find more relaxed knowing each of my functions do one thing and one thing only, I know they aren't becoming overly complex and bloated and I can more easily refactor them without worrying about side effects on my code.

##Style Guides: *keeping things consistent*

I strongly recommend the use of a Style Guides for a team of developers. Mainly because it means there is a consistency in the code style, and that alone can make a great difference in the ease of maintaining a large code base.

I've written a few Style Guides already so I wont go into specifics here:

[JavaScript Style Guide](https://github.com/Integralist/Style-Guides/blob/master/JavaScript%20Style%20Guide.md)

[HTML Style Guide](https://github.com/Integralist/Style-Guides/blob/master/HTML%20Style%20Guide.md)

[CSS Style Guide](https://github.com/Integralist/Style-Guides/blob/master/CSS%20Style%20Guide.md)

##Testing: *making sure stuff works*

Well, we all know we should test our code and I admit I just don't do this enough.

But just to be clear: when I say 'test our code' I don't mean 'unit testing' because, although that helps, that doesn't eliminate all bugs. I also don't like the idea of writing tests *after* the code is written - I feel like the tests we write will miss something important (almost like a subconscious design to not break the code we've spent a long time writing, so we don't write the most robust tests we probably could). 

I much prefer the TDD (Test-Driven Development) process where you:

1. write tests before you write any code (that way you get to design the perfect API with no compromise). 
2. then start writing code to make the failing tests pass
3. then once the tests are passing you go back and refactor the code (note: *refactor* does not mean *redesign*)

This process will ensure you have a rock solid code base. I should do it more than I do but my excuse so far as always been that the time up-front it takes to do this process is something I've not been able to properly justify at work considering the types of deadlines we have to deal with.

**That being said…** It is our responsibility as developers (we know better than any one else) that if something is to be done properly then it needs to be done right - so we need to be dictating to our managers (or whoever's making the decisions about time scales) what the real life time scale should be. We're the experts in this field, so why should any one else tell us how long we have to get to complete a job? And if we're the one's giving the estimates/quotes (based on how long something will take to build) then why aren't we adding in extra time for writing tests the TDD way?

So although I *currently* don't don't do this, on my next job the Test-Driven Development process will be taking a much more prominent place in my developer work flow. It has to, for my sanity.

I highly recommend you use the TDD specific [BusterJs](http://busterjs.org/), which is without a doubt the best testing toolkit available today. I love it. It can be run from the browser or via the command line and makes it very easy to do mocking and stubbing, writing deferred and async tests, nested tests as well as something really useful which is to tell BusterJs to NOT run specific tests based on the results of your own feature detection (e.g. testing a specific feature that is known to crash IE6 is a bit pointless). OH, and it also works with AMD!

I've also written an article about [using the Behaviour-Driven Development testing framework Jasmine](https://github.com/Integralist/Blog-Posts/blob/master/Beginners-guide-on-how-to-test-your-code-%28using-Jasmine-BDD%29.md) - it goes into detail about testing your code and the differences between TDD/BDD and 'unit testing'.

##Performance: *running fast*

Performance has had a mention in quite a few areas of this post already so it seems silly not to dedicate a section to it.

I - like many of you - abide by the (now) standard rules of [YSlow](http://developer.yahoo.com/yslow/) and [Google Pagespeed](https://developers.google.com/speed/pagespeed/) - so things like:

* Minifying JavaScript/CSS
* Reducing HTTP Requests
* GZIP all components
* Stylesheets at the top of the page
* JavaScript at the bottom of the page
* Optimise your images (using CSS Sprites or a tool like [ImageOptim](http://imageoptim.com/))

...and the list goes on and on. 

If you're not already doing it then make sure you take the time to read through them - as they are all straight forward items that don't take much effort but REALLY make a difference as far as your website/app's performance is concerned.

To make your life a little easier you can even use the YSlow and Pagespeed Firebug plugin extensions which make it much easier for checking a site's compliance with these rules.

But there are other aspects of your site that aren't simple to resolve - JavaScript performance for one is a massive topic and one that I could never do justice to here, so I will instead simply recommend you read [High Performance JavaScript ](http://shop.oreilly.com/product/9780596802806.do) by Nicholas C. Zakas which is an excellent compilation of information about fine tuning your JavaScript.

Below is an example of a minor performance dilemma I had recently while writing CSS with the Sass pre-processor. Although this example would likely be an extremely neglible performance hit I still found it interesting (afterwards) how potentially easily it is to make mistakes when using a pre-processor. So here is some Sass driven CSS code… 

```css
.dashboard-box {
    @include box-sizing(border-box);
	@include shadow(1px 1px 3px rgba(0, 0, 0, .2));
	@extend .box-gradient-bg;
	
	border: 1px solid $grey;
	margin-bottom: 1.5em;
	
	&,
    > div {
	   @include radius(.5em);
	}
	
	> div {
    	border: 1px solid $white;
    	padding: 0.2em;
	}
	
	.title {
    	@include radius(.3em);
    	@extend .header-gradient-bg;
    	font-size: 1.166666667em;
    	margin: 0;
    }
}
```

…which generates the following CSS…

```css
.dashboard-box {
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  -o-box-sizing: border-box;
  box-sizing: border-box;
  -webkit-box-shadow: 1px 1px 3px rgba(0, 0, 0, 0.2);
  -moz-box-shadow: 1px 1px 3px rgba(0, 0, 0, 0.2);
  -o-box-shadow: 1px 1px 3px rgba(0, 0, 0, 0.2);
  box-shadow: 1px 1px 3px rgba(0, 0, 0, 0.2);
  border: 1px solid #999999;
  margin-bottom: 1.5em;
}
.dashboard-box,
.dashboard-box > div {
  -webkit-border-radius: 0.5em;
  -moz-border-radius: 0.5em;
  -o-border-radius: 0.5em;
  border-radius: 0.5em;
}
.dashboard-box > div {
  border: 1px solid white;
  padding: 0.2em;
}
.dashboard-box .title {
  -webkit-border-radius: 0.3em;
  -moz-border-radius: 0.3em;
  -o-border-radius: 0.3em;
  border-radius: 0.3em;
  font-size: 1.166666667em;
  margin: 0;
}
```

…but at first I wasn't sure if having duplicated selectors (i.e. `.dashboard-box` and `.dashboard-box > div`) was very efficient, so I went back and changed my Sass code. This time I put `@include radius(.5em);` both at the top of the main declaration block as well as inside the `> div` declaration...

```css
.dashboard-box {
    @include radius(.5em);
    @include box-sizing(border-box);
	@include shadow(1px 1px 3px rgba(0, 0, 0, .2));
	@extend .box-gradient-bg;
	
	border: 1px solid $grey;
	margin-bottom: 1.5em;
	
	> div {
	    @include radius(.5em);
    	border: 1px solid $white;
    	padding: 0.2em;
	}
	
	.title {
    	@include radius(.3em);
    	@extend .header-gradient-bg;
    	font-size: 1.166666667em;
    	margin: 0;
    }
}
```

...which resulted in the following CSS… 

```css
.dashboard-box {
  -webkit-border-radius: 0.5em;
  -moz-border-radius: 0.5em;
  -o-border-radius: 0.5em;
  border-radius: 0.5em;
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  -o-box-sizing: border-box;
  box-sizing: border-box;
  -webkit-box-shadow: 1px 1px 3px rgba(0, 0, 0, 0.2);
  -moz-box-shadow: 1px 1px 3px rgba(0, 0, 0, 0.2);
  -o-box-shadow: 1px 1px 3px rgba(0, 0, 0, 0.2);
  box-shadow: 1px 1px 3px rgba(0, 0, 0, 0.2);
  border: 1px solid #999999;
  margin-bottom: 1.5em;
}
.dashboard-box > div {
  -webkit-border-radius: 0.5em;
  -moz-border-radius: 0.5em;
  -o-border-radius: 0.5em;
  border-radius: 0.5em;
  border: 1px solid white;
  padding: 0.2em;
}
.dashboard-box .title {
  -webkit-border-radius: 0.3em;
  -moz-border-radius: 0.3em;
  -o-border-radius: 0.3em;
  border-radius: 0.3em;
  font-size: 1.166666667em;
  margin: 0;
}
```

Now, what has this given us? OK so yes this means I don't have the `.dashboard-box` or `.dashboard-box > div` selectors duplicated (which is good), BUT now I've got two different problems:

* the `border-radius` code is duplicated (so overall file size is now bigger than what it was previously)
* code maintainence takes a hit

...all at the cost of two selector lookups?

I don't know which performs better. I could imagine the larger file size GZIP'ing better, but in my Sass code I now have two places I need to update the border radius value (if it ever needed to change), and that's not efficient with regards to my own time maintaining this code. I know that doesn't seem like much in this example, but imagine you had this sort of stuff happening all over the place, then it would soon start adding up to be a right pain in the ass.

So in the end I went back to the original solution I had which meant the selector was duplicated but it had the two pros of: overall file size was down and developer maintenance was easier.

In this example neither option was really wrong, but it goes to show that you not only need to be careful when using a pre-processor but also you need to be thinking about how performance and maintenance tie together when writing your code.

##Version Control: keeping track of things

Using version control software is a no-brainer really. Depending on whether you use the comand line interface or a visual app is a personal choice and I prefer to use the command line as it's just more efficient.

There are many different version control systems, but the one I learnt to use was [Git](http://git-scm.com/) (which as of 2012 has had a beautiful site redesign).

The good thing about using Git is that it makes it extremely easy to create 'branches' for different areas of development. If the client asks for a new feature to be added then we'll create a new branch for that feature and then merge back into our master branch when ready to deploy the code.

One of the tenets of version control which I really like and I make sure to follow is: 

> the 'master' is always deployable

…so not matter what you're working: on your 'master' branch should always be clean.

I also recommend using [GitHub](https://github.com/integralist) for storing your code - there are both public and private accounts so you can keep business critical code safe. One nice feature of GitHub is the ease at which you can create promotional pages for your open-source projects (see: [http://integralist.co.uk/Passage/](http://integralist.co.uk/Passage/) for an example using a pre-designed/built responsive template)

You can also have GitHub host simple website pages for you - [my site](http://www.integralist.co.uk/) for example is hosted by GitHub and so I can push my site changes live straight from the command line without needing an FTP program (which is amazing in it's simplicity and ease) - a nice side effect from having my site hosted on GitHub is that all my GitHub pages are also mapped to my domain - so for example [http://integralist.github.com/Passage](http://integralist.github.com/Passage) actually redirects to [http://integralist.co.uk/Passage/](http://integralist.co.uk/Passage/).

##Automation: *making life easier*

There are many ways to automate your work flow, doing so makes you more efficient and productive and in general can just save you hassle.

I've already discussed some of the command line tools I use that make my life easier (e.g. jshint, csslint, the RequireJs build script).

But there are other things you can do so as using a content generator like [Yeoman](http://yeoman.io/) which I'll be honest I don't use but some of the things it does for you are quite nice, such as:

* Generates project based on criteria you specify
* Automatically lints your scripts
* Built in preview server
* Image optimizer
* AppCache manifest generation
* Build process
* Package management
* Unit Testing

…all of which sounds great but I really *hate* the HTML it generates for you. I know that sounds like a minor issue but if that can't be changed (and apparently it can - so that's something I'm going to look into) then that's going to annoy me every single time I use it because it means I'll end up wasting time on a tool that's supposed to make things easier *for me*. 

So (in the mean time) I instead have two template projects on GitHub (one for work based PHP projects and one for personal Ruby based projects) that I can clone for each new project and which covers the general set-up each of my sites have.

I already have CSS and JavaScript linting on the command line which works for me and the process of optising images using the ImageOptim app works fine (drag and drop images folder and have it take care of the rest) - but I am currently looking into installing both [OptiPNG](http://optipng.sourceforge.net/) and [JPEGTran](http://jpegclub.org/jpegtran/) so I can move away from the ImageOptim app and use the command line instead.

I use the command line to handle my dependancies by pulling in GitHub hosted repositories as sub modules and then any time there is an update I can just run `git pull` on the relevant sub modules to get the latest versions - not quite as automatic as something like Google's Yeoman but it works for me.

My CSS and JavaScript files are concatenated and minified with a combination of Sass and the RequireJs build script and so the only thing left to do is start programming.

##Conclusion

I felt this (damn)long post deserved a 'conclusion' but I've suddenly realised that I'm not actually very good at writing them, but here we go:

There is a lot of new cool tech available at the moment - along with the current batch of good/useful development best practices - which has made programming really fun and refreshing for me once again (every few years you get bored of the environment you're in and then something happens to inject new life into the industry - and that is what has happened over the past year for me). 

If you haven't already, then it's time to just get stuck in and start taking advantage of what's out there and help take your skills to the next level.