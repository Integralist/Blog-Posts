#Maintainable CSS with BEM

* Introduction
* BEM: Block, Element, Modifier
* Example
* Why BEM over the others?
* Conclusion

##Introduction

This is a super quick post to introduce you to a method of writing more maintainable CSS by using what's called "[BEM](http://bem.info)".

Update: [@necolas](http://twitter.com/necolas) made a comment worth noting, that I'm using a modified version of the BEM naming conventions (BEM is a total framework that goes beyond just naming of classes and writing maintainable CSS). So I thought it best to make note of that here so as to not cause any confusion.

##BEM: Block, Element, Modifier

BEM stands for "Block, Element, Modifier" and is a simple but effective way to group together different components/widgets (as shown by the following visual aid).

![Visual Example of BEM](http://www.integralist.co.uk/Assets/Images/BEM.png)

Within each defined 'Block' you can have multiple 'elements' that make up the object, and for each element (depending on where it appears within the block) you might need to 'modify' the state of the element.

The principles are similar to other methods of structuring CSS ([OOCSS](https://github.com/stubbornella/oocss/wiki)/[SMACSS](http://smacss.com)) but they are greatly simplified in comparison without giving up any of the architectural benefits.

The best way to understand BEM is to see an example of how it's used (see next section). But if you want the full details of its history and some more detailed/visual break down of the concepts then please see the [BEM](http://bem.info) website.

##Example

Below we have a money calculator widget. You enter an amount of money (e.g. £2.12p) and when you press on 'calculate' it'll return to you a list of coins required to make up the amount specified.

The HTML is very simple...

```html
<section>
    <h1>Sterling Calculator</h1>
    <form action="process.php" method="post">
        <p>Please enter an amount: (e.g. 92p, &pound;2.12)</p>
        <p>
            <input name="amount"> 
            <input type="submit" value="Calculate">
        </p>
    </form>
</section>
```

So lets add in our classes for styling this widget and lets go on to break down what we've added and why...

```html
<section class="widget">
    <h1 class="widget__header">Sterling Calculator</h1>
    <form class="widget__form" action="process.php" method="post">
        <p>Please enter an amount: (e.g. 92p, &pound;2.12)</p>
        <p>
            <input name="amount" class="widget__input widget__input--amount"> 
            <input type="submit" value="Calculate" class="widget__input widget__input--submit">
        </p>
    </form>
</section>
```

First thing to notice is that we've determined the top level `<section>` element to be our 'block'. This is the top level containing element. We've added a class of `widget` and this will be our namespace for this object/widget (whatever you prefer to call it).

From here on all elements that we added classes to within this 'block' will be namespaced to the top level name of `widget`.

I wanted to style the `<form>` element so I added the class `widget__form`. The double underscores allow us to easily recognise a class as being part of the `widget` block. We see this used on the `<input>` elements as well: `widget__input`.

Here is a list of the elements styled…

* `widget`
* `widget__header`
* `widget__form`
* `widget__input`

Notice that there are two other classes used: `widget__input--amount` and `widget__input--submit`. These are our 'modifiers'. They modify the state of our elements.

Let's look at where these have been used. I've applied the same class of `widget__input` on both `<input>` elements (because they both have the same base structure/styling). But both elements do have slight differences in their appearance, hence the use of a 'modifier' to apply the additional unique styles. 

Modifiers are written with two hyphens(dashes) like so: `block__element--modifier`.

This means that our CSS code for this widget ends up looking like this…

```css
.widget {
    background-color: #FC3;
}

.widget__header {
    color: #930;
    font-size: 3em;
    margin-bottom: 0.3em;
    text-shadow: #FFF 1px 1px 2px;
}

.widget__input {
    -webkit-border-radius: 5px;
       -moz-border-radius: 5px;
         -o-border-radius: 5px;
            border-radius: 5px;

    font-size: 0.9em;
    line-height: 1.3;
    padding: 0.4em 0.7em;
}

.widget__input--amount {
    border: 1px solid #930;
}

.widget__input--submit {
    background-color: #EEE;
    border: 0;
}
```

##Why BEM over the others?

I've tried a lot of different ways of writing CSS over the years. It went something like this…

* No structure, everything in one file loaded on every page of a site.
* Separate files to try and keep page specific content together, but still no real structure.
* Standard [OOCSS (Object-Oriented CSS)](https://github.com/stubbornella/oocss/wiki)
* [SMACSS (Scalable and Modular Architecture for CSS)](http://smacss.com)

…and now BEM.

**The reason I choose BEM over other methodologies comes down to this: it's less confusing than the other methods (i.e. SMACSS) but still provides us the good architecture we want (i.e. OOCSS) and with a recognisable terminology.**

For me OOCSS isn't strict enough. It let's developers go wild with how they name their objects. But I've seen that get really messy on larger projects, or projects with more than one developer and because of the lack of strictness in naming conventions developers become confused on what classes are supposed to be doing.

With regards to SMACSS: it's almost too strict in the sense that I think it's *over structured*. When I first started using it I thought this was the solution I had been searching for but all that ended up happening was that I had so many fragmented areas of CSS that I didn't know where to go first. It was too over whelming.

This might not be the case for some people, but for me these are all instances of the old adage: "*don't make me think*". If I have to think too hard about how something works, or where I need to find the code for something then (in my opinion) that methodology has failed.

BEM succeeds because it provides a good object oriented structure with a familiar terminology and is simple enough to not get in your way.

But like with any tool, it can be misused. In the end it comes down to the overall skill and understanding of the developer.

###Simplicity

As I said before, the reason I find BEM a better option is the simplicity. 

Even down to the terminology used is simplified compared to other methodologies. For example, depending on who you talk to about structured CSS you may hear the words: 

* objects
* modules
* widgets
* components

…notice the terminology is different but what they refer to are effectively the same thing. No wonder it can become confusing to some people.

BEM is different in that its terminology is based around the environment it works for: HTML and CSS. We all know when working in CSS what a 'block' is, it's the fundamental building block (no pun intended) of how elements on the page are rendered, but that term can also be understood when used like so… 

> I saw this block of code the other day, it was hideous.

…you know within the context of that sentence the person speaking is referring to a chunk of code, a grouping of code.

The word 'Block' is simple but a very focused term, and more importantly it is a very familiar term. 

We also know when working in CSS that ultimately we're targeting 'elements'. No other word better fits the description, because that is exactly what we're doing.

And lastly, the word 'modifier' again is a simple but fully understood and familiar term used by developers… 

> I want to modify this element, how should I do that?

###But still structured

But with this simplified terminology/structure it gives us all the tools we need to write maintainable and easily understandable code. BEM easily scales with the size of a project.

##Conclusion

I know I've said it before about SMACSS ("*wow, I think this is it!*") but even when I first started using SMACSS I still had niggling feelings about "*hmm, it's a little complicated getting this all in place, but it seems to work well*". With BEM I've not had any of those concerns. The only initial concern I had was with the look of it. I didn't like the double underscores or the double dashes. But now I actually like them!

If you want to see more good usage of BEM then I'll refer you to a small CSS abstraction library called [inuit.css](https://github.com/csswizardry/inuit.css) by [Harry Roberts](http://csswizardry.com/) as well as [my own website's source code](https://github.com/Integralist/integralist.github.com)