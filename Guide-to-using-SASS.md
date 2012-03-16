#Guide to using SASS

Note: for working examples see [https://github.com/Integralist/Guide-to-using-SASS](https://github.com/Integralist/Guide-to-using-SASS)

##Introduction

I agree that 'pre-processors' such as LESS, SASS, Compass etc are normally a bad idea because generally they are used badly by developers/designers who could do better without them (see: [Object-Oriented CSS](https://github.com/stubbornella/oocss/wiki)).

That being said, there are some areas where pre-processors can really help out, such as being able to use `@import` without worrying about the browser loading the stylesheet synchronously and thus making the page slower to load. Or being able to create a variable to hold your client's branding colours for easy re-use.

In this post I've purposely not included details on everything that SASS can do because I don't believe they are useful. I would rather you utilise principles of OOCSS and only use SASS as an addition to that which helps round off those rough edges when developing CSS for large scale applications. Two items I have mentioned which should be avoided (and I'll repeat this later as well) are `@extend` and `@mixin`. Both of these causes more problems than they solve and can be worked around with good OOCSS structuring, but I've mentioned them so curious readers don't think I've neglected them out of hand and so I've detailed some of the potential issues with using them (use at your own risk!)

Below are some of the items we'll cover:

* Installation
* How to run
* Comments
* Variables
* Calculations
* Colour functions
* Importing
* Extend
* Mixins
* Interpolation

###Installation

To install SASS you need to have `Ruby` installed.

For Mac users, you already have it!

For Window users there are quite a few installers available, but according to some: [http:/rubyinstaller.org](http:/rubyinstaller.org) is recommended.

Once `Ruby` is installed, you'll need to open your command line interface of choice (I'm on a Mac, so for me this is the `Terminal` app).

From the command line execute `gem install sass` (I needed to use `sudo` along with that because I didn't have authorisation to install in the directory it wanted)

###How to run

Within the command line navigate to your website directory and execute the command `sass --watch Assets/Styles/` (change `Assets/Styles/` to whatever path your SASS/CSS files are). As you can see in my example, from the root directory of my website I have my SASS files stored here: `Assets/Styles`. This command uses the `--watch` flag which means every time a `.scss` file is saved a corresponding `.css` file is compiled automatically for you.

One thing to be aware of is that if you aren going to use `nested` items (which apparently is a big selling point for SASS users but one that I personally think is a terrible feature for performance and efficiency) then you'll be better off starting up SASS using `sass --style expanded --watch Assets/Styles/` which means when your CSS is compiled the nested output will at least be laid out more logically than their default mess of a display (which is very difficult to try and understand).

One last point here is that if you execute the above command then you will need to have your `.scss` files in the same location as where your `.css` files should be. If on the other hand, like me, you prefer to keep your SASS files separate then amend the original command as follows:

`sass --style extended --watch Assets/Styles/Sass/:Assets/Styles/` 

…this basically says "watch the folder `Assets/Styles/Sass/` and compile any files into the parent folder `Assets/Styles`" - you'll notice that the colon character `:` is what helped make that happen.

###Comments

Comments are a standard feature of CSS, but sometimes it would be nice to use a easier syntax for writing them (as seen in other programming languages). Such as: `// this is a comment` rather than having to write `/* a typical CSS comment */`.

SASS lets you do this, but it's worth keeping in mind that the reason they provide this is so you can have 'private' comments, and by this they mean that comments like `// comment` are not included in the compiled CSS file, where as the standard comments style `/* comment */` are. Not that this should matter because when you push your CSS to the production server it should be minified for performance purposes, but it's worth knowing about.

###Variables

Variables are a great way to not have to repeat entering the same value over and over. The most common use case is the client's branding colours. Normally this colour will appear in lots of different areas of the site (links, hover effects etc) and if the colour does need to change slightly then you either do it by hand or you run a 'find and replace' function.

To create a variable in SASS you simply prefix the name of the variable with a dollar sign: `$brand_color: #C00;` I can now use `$brand_color` wherever I like. For example…

```css
.header {
	color: $brand_color;
}
```

This makes life a lot easier and although I've seen people claim that OOCSS can work around this, it can, but not easily and so using SASS for this alone is still extremely useful in my mind.

###Calculations

I can't imagine me ever using this feature, but I've included it because it also doesn't cause any negative effect (unlike `@extend` and `@mixin`).

You can use all standard operators (*, /, +, -) for example:

```css
$width: 10px; 
$double_width: $width * 2;
```

You can do calculations inline (i.e. where the property value is set) and you can also group calculations:

`width: $width * (1 - $sidebar_percent);`

###Colour functions

These are very useful. A lot of times you have for example 'hover' effects that need a colour that is similar in shade to the main brand colour. Normally you have to open up a colour palette and randomly pick something, whereas the following functions help with that process:

`lighten(colour, percentage)`
	
```css
.txt-light {
	color: lighten($brand_color, 30%);
}
```

`darken(colour, percentage)`

```css
.txt-dark {
	color: darken($brand_color, 10%);
}
```

`saturate(colour, percentage)`

```css
.txt-sat {
	color: saturate($brand_color, 100%);
}
```

`desaturate(colour, percentage)`

```css
.txt-desat {
	color: desaturate($brand_color, 20%);
}
```

`adjust-hue(colour, degrees)`

```css
.txt-hue {
	color: adjust-hue($brand_color, 180);
}
```

`grayscale(colour)`

```css
.txt-greyscale {
	color: grayscale($brand_color);
}
```

`mix(colour, colour)`

```css
.txt-mix {
	color: mix($brand_color, #C00);
}
```

###Importing

You can import other SASS files into your main stylesheet using `@import "other.scss"`

The biggest note of warning here is that if you import a SCSS file and that file generates a CSS file of its own then you wont be able to use variables that aren't imported or defined in the imported SCSS file. For example… 

If you have the main stylesheet `structure.scss` and within that file you import another SCSS file called `colours.scss` - if `colours.scss` generates its own CSS file `colours.css` then the SASS file `colours.scss` cannot use any variables that are defined inside `structure.scss` (or which have been imported separately into `structure.scss`). 

…there is a work-around to this which is to make sure `colours.scss` doesn't generate a CSS file, and the way you do that is prefix the file name with an underscore `_colours.scss`. You can also still import it without specifying the underscore: `@import "colours.scss";`

To be honest, it's likely that any stylesheets you have deemed modular enough to be imported you'll want them not to generate their own CSS files (what would be the point if when compiled they are being imported into the main CSS file?)

**Beware!** if you're main stylesheet has for example a `.brand` class and so does your imported stylesheet, when you compile the SASS file into CSS the `.brand` class will be listed twice.

For example:

```css
$brand_color = #0000FF;
.brand { 
	color: $brand_color; 
}
// MORE STYLES
@import "other.scss";
```

…generates the following CSS…

```css
.brand { 
	color: blue; 
}
// MORE STYLES
.brand { 
	background-color: red; 
}
```

…which obviously isn't as efficient or clean as…

```css
.brand { 
	color: blue; 
	background-color: red; 
}
// MORE STYLES
```

…but that's the trade-off between SASS features and the efficiency of the produced code.

Note: you can import a normal CSS file (as you would in standard CSS) but it'll be pushed to the top of the compiled CSS file. This is because in standard CSS an imported stylesheet is only allowed to be imported from the top of the file.

Lastly, be aware that you could end up trying to import the same SASS file multiple times, and the way SASS handles that situation is by only including the imported file once BUT in the last place it was referenced (which may or may not cause you specificity issues).

###Extend

This feature is almost pointless as you really should be developing OOCSS (object-oriented CSS). All this does is repeat properties in the compiled CSS file. 

The reason I'm even mentioning this feature is so you know not to bother with it and instead concentrate on the concepts of OOCSS more.

Anywhere you have a CSS class you can re-import that inside another rule:

```css
.myClass {
	border: 1px solid #969;
	color: red;
}

button {
	@extend .myClass;
	background-color: orange;
}
```

…which generates the following CSS…

```css
.myClass, button {
	border: 1px solid #969;
	color: red;
}

button {
	background-color: orange;
}
```

A couple of last words of caution: `extend` avoids code duplication but it also causes other problems in that the amount of selectors can become an issue. If you @extend the same base class multiple times you may end up with a rule that has thousands of selectors, which isn't good for performance and can even make the browser crash (limit its use if you must use it).

Also, you could end up adding properties that are already specified in the `extend`/`mixin` because let's face it you're unlikely to remember every property set inside them, so when you do use them and come back a few days/weeks/months later to make further updates you'll have to keep checking them before you can safely add another property just to make sure you're not doubling up on properties already there - which is hassle and can lead to mistakes.

###Mixins

These are functionally similar to `extend`, but a mixin's properties are copied into the class rather than referenced (`extend` is designed to be used as a mechanism for proper inheritance as seen in other classical object-oriented programming languages), but more importantly with mixins you can also change the values when calling the mixin into your class (like they were a function).

Remember that this can cause code duplication so please do NOT use 'Mixins' (OOCSS should be used instead).

You create a mixing like so:

```css
@mixin .myMixin { 
	color: blue; 
}
.product_title {
	@include .myMixin;
}
```

…and you can change the values like so…

```css
@mixin .myMixin($set_colour) { 
	color: $set_colour; 
}
.product_title {
	@include .myMixin(#FF0000);
}
```

…you can also define a default value if none is provided…

```css
@mixin .myMixin($set_colour: #0000FF) { 
	color: $set_colour;
}
```

…you can use mixin's for things like CSS3 properties…

```css
@mixin rounded_borders($color, $width: 5px, $rounding: 5px) { 
	-moz-border-radius: $rounding $rounding; 
	-webkit-border-radius: $rounding $rounding; 
	-khtml-border-radius: $rounding $rounding; 
	-o-border-radius: $rounding $rounding;     	border-radius: $rounding $rounding;     	border: $width solid $color;
}

@mixin opacity($opacity) {	filter: alpha(opacity=#{$opacity}); // IE 5-9+ 
	opacity: $opacity * 0.01; 
}
```

…and use the opacity like so…

```css
.h1 { 
	// Use the IE numbering style (instead of the W3C's 0-1 numbering style)
	@include opacity(60);
}
```

…or you could use the reverse…

```css
@mixin opacity($opacity) {	filter: alpha(opacity=#{$opacity*100}); // IE 5-9+ 
	opacity: $opacity; 
}
```

###Interpolation

One area where mixins can't help you is when there is some specific CSS3 syntax such as `background-image` (with gradients). This is because not only the value changes but the syntax itself is different for each browser. 

One way to work around this issue is to use `interpolation`. The way it works is that you wrap a variable name with `#{}` e.g. `#{$my_variable}` and that will dynamically insert the value at that place in your CSS. Might sound a bit confusing so best to demonstrate this with an example, and the best example I can think of is again the `background-image` property with multiple different vendor prefixes… 

```css
// Variable
$prefixes:-webkit,-moz,-ms,-o;

// Loop over each item in the $prefixes variable
// using interpolation to insert the relevant value dynamically
@each $prefix in $prefixes {
	background-image: #{$prefix}-linear-gradient(rgba(255,255,255,0), rgba(0,0,0,0.1));
}

```

…most of the time mixins will help you work around CSS3 vendor prefixes but in the above instance `interpolation` is the way forward.