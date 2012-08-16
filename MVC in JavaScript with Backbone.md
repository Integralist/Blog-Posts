#MVC in JavaScript with Backbone.js

##Table of Contents

* Introduction
* What is MVC?
* Backbone's interpretation of MVC
* Using AMD Modules and RequireJS
* Scope Access
* Project
* Set-up
* Architecture
* Wrap-up
* Conclusion

##Introduction

I'll be honest and tell you that I struggled for quite a while to actually see the point of using [Backbone.js](http://backbonejs.org/). 

It seemed like a lot of overhead for very little in return.

I also didn't like the fact that it tied me into using both Underscore and jQuery as dependencies.

Because of this, and because I knew a lot of these so called MVC frameworks weren't *really* MVC (in the traditional sense), I decided to 'roll my own' and so started writing [my own MVC library](https://github.com/Integralist/MVC-Start-up-Kit/tree/library_agnostic) (please do read the articles listed at the end of this post by [Addy Osmani](http://twitter.com/addyosmani) for an in-depth look at the massive MV* culture we have at the moment).

My own library worked fine, and it genuinely followed the MVC principles:

<table>
  <tr>
    <th>M (Models)</th>
    <th>V (Views)</th>
    <th>C (Controllers)</th>
  </tr>
  <tr>
    <td>Data</td>
    <td>User Interface</td>
    <td>Logic</td>
  </tr>
</table>

But my own biggest concern was that although my MVC library was *structurally* equivalent to the proper MVC pattern and the API itself was fairly clean and easy to use, there was a lot of areas where I had too tightly coupled my library to a particular way of working and that made it difficult to re-use on different projects without first spending a lot of time making those tight couplings 'loose' again each time.

It was obvious I needed to spend a lot more time on this library of mine for it to be genuinely usable in a production environment, and that's when I decided to instead take another look into Backbone.js to see what it could offer me, because from all the examples I had looked at in the past the code produced appeared very clean, flexible and well structured. Plus there are *lots* of agencies/companies implementing Backbone nowadays. So out of all the MVC libraries available (and there are quite a few) it seemed like a pretty safe bet to take another look at it.

##What is MVC?

For those who've not heard of the MVC Design Pattern it is quite simple: each letter of the acronym is a separate area of concern…

###M: Model

A Model is a piece of data.

If you had a photo album then each photo would be a Model.

If you had a car showroom then each car would be a Model.

If you owned a pet store then each animal would be a Model.

You get the idea.

###V: View

A View is the user interface.

This could literally be anything the user of your program interacts with. So any HTML element could be made into a 'View'.

###C: Controller

A Controller is the brains of the program and determines what happens and when.

###Communication between them?

The traditional concept of MVC states that a Model should only handle its own data and should have no other logic bound to it. So for example, a Model can have a method for getting/updating its data but it should have no logic built-in to determine *when* it should get that data (that's what the Controller does). 

Both the Model and the View can alert the Controller of any changes or interactions and then the Controller can decide what to do with that information (e.g. when the user selects an option from a drop down menu within the View then the Controller might request specific data from the Model, when the Model gives that data to the Controller then it can pass that onto the View to display).

Typically you'll have one Controller for every View/Model but you could have multiple Views/Models per Controller.

##Backbone's interpretation of MVC

Backbone.js is much like all the other JavaScript MVC libraries available today (with the exception of Peter Michaux's [Maria](https://github.com/petermichaux/maria/)) where by it's not strictly MVC but a variation of the pattern.

Addy Osmani (from Google) has covered this subject in-depth and so I highly recommend you review his work (see the bottom of this post for links).

But the basic premise for most of these libraries is that they implement the Model and the View but the Controller is swapped out for something else. So in Backbone's case it doesn't have a Controller, instead the View handles both the traditional View responsibilities as well as the Controller logic.

###Collections?

Backbone.js also introduces another concept called a 'Collection'. A Collection is simply a group of a specific Models. So if you had a Model called "PhotoModel" and you created a few instances of that Model you might want to create a Collection called "PhotoAlbum". PhotoAlbum would then be a *collection* of PhotoModels.

Collections trigger events when a Model is added or removed from the Collection and you are able to iterate over the Collection and pull out a specific Model, so they can end up being pretty useful.

###Router/History?

Backbone.js provides a History object which monitors the `hashchange` event and also provides an opt-in HTML5 `pushState` variation (which falls back to standard AJAX style hash bang navigation).

There is also a Router object provided which handles the set-up of simple URL routing.

So far I've only lightly interacted with the `Backbone.Router`/`Backbone.History` aspects but I've provided examples below of how to utilise them.

##Using AMD Modules and RequireJS

Backbone.js can be used with standard `<script>` tags, so there is no need for you to use a module system such as AMD (or a module loader such as RequireJS). But my personal preference is to using both AMD with RequireJS, and that's what the example project I'll show you will use.

I wont go into the details of what AMD is or how to use RequireJS, I'll instead point you to [another article I've written on the subject](https://github.com/Integralist/Blog-Posts/blob/master/Beginners-guide-to-AMD-and-RequireJs.md).

##Scope Access

Because we're using AMD (which keeps our code self-contained so it doesn't introduce global variables) it can be awkward (in some situations) to share information across modules.

One thing I wanted to do recently was access a View instance from within another View. After reviewing the situation I found there were multiple ways to do this:

* Setting an instance property on the other View
* Passing the View through as a property of the argument object when creating the other View
* Adding a property to a global namespace

…which option you go with depends on personal preference more than anything. I went with the second option.

##Project

The project we'll build is a basic Contact Manager. 

The layout will be a `<select>` menu that is populated with some Contact names.

Selecting a Contact from the menu will display all details for that contact.

Next to the menu will be a form where we can add a new contact and once added it'll automatically appear within the menu for the user to select to view.

A finished version of this project can be found [on my GitHub](https://github.com/Integralist/Backbone-Playground).

##Set-up

Let's take a top level view of our directory structure…

* /Assets/
    * /Includes/
        * Contacts.php
    * /Scripts/
        * /App/
            * main.js
            * namespace.js
        * /Collections/
            * Contacts.js
        * /Models/
            * Contact.js
        * /Routes/
            * Routing.js
        * /Utils/
            * backbone.js
            * jquery.js
            * lodash.js
        * /Views/
            * AddContact.js
            * Contact.js
            * Contacts.js
        * require.js
    * /Styles/
        * styles.css
* index.html

##Architecture

###HTML

The HTML page `index.html` consists of a few different elements that we'll use as 'Views'. 

We have the `<select>` menu for displaying the initial list of Contacts, the `<form>` for allowing us to add more Contacts, and an empty `<div>` which will be used for displaying the full details of the selected contact.

The full HTML page looks like this...

```html
<!doctype html>
<html dir="ltr" lang="en">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <meta name="author" content="Mark McDonnell" />
        <title>Backbone.js</title>
        <link rel="stylesheet" href="Assets/Styles/styles.css">
    </head>
    <body>
        <div id="view-contacts">
    		<select>
    			<option>Please select a user</option>
    		</select>
		</div>
		
		<div id="view-contact"></div>
		
		<form id="view-add">
			<p><input type="text" placeholder="Name" name="fullname"></p>
			<p><input type="text" placeholder="Age" maxlength="3" name="age"></p>
			<p><input type="text" placeholder="Address" name="address"></p>
			<input type="submit" value="Add New Record">
		</form>
        
        <script data-main="Assets/Scripts/App/main" src="Assets/Scripts/require.js"></script>
        <script type="text/template" id="contact_template">
            <dl>
                <dt>ID</dt>
                <dd><%=id%></dd>
                <dt>Name</dt>
                <dd><%=name%></dd>
                <dt>Age</dt>
                <dd><%=age%></dd>
                <dt>Address</dt>
                <dd><%=address%></dd>
            </dl>
        </script>
    </body>
</html>
```

###Styles

The styles for this project are just basic - we're not worrying about design here… 

```css
body {
	font: normal small Arial, Verdana, sans-serif;
}

.hide {
	display: none;
}

#view-add {
	border: 1px solid #999;
	left: 350px;
	padding-bottom: 10px;
	padding-left: 10px;
	position: absolute;
	top: 10px;
	width: 166px;
}

#view-add div {
	background-color: green;
	color: #fff;
	margin-right: 10px;
	margin-top: 10px;
	padding: 10px;
}
```

###JavaScript

RequireJS allows us to utilise just a single `<script>` tag which points to our main script file that contains the following structure… 

```js
requirejs.config({
    shim: {
        '../Utils/backbone': {
            deps: ['../Utils/lodash', '../Utils/jquery'], // load dependencies
            exports: 'Backbone' // use the global 'Backbone' as the module value
        }
    }
});

require(['../Models/Contact', '../Collections/Contacts', '../Views/Contacts', '../Views/AddContact', '../Views/Contact', '../Routes/Routing'], function (Contact, Contacts, ContactsView, AddContactView, ContactView, Routing) {
    
    /**
     * Model Generation Examples
     */
    
    var manager = new Contact({
        id: _.uniqueId(),
        name: 'Mark McDonnell',
        age: 30
    });
    
    var developer = new Contact({
        id: _.uniqueId(),
        name: 'Ashley Banks',
        age: 23
    });
    
    // The following few lines are used just to demonstrate the Backbone.js API
    var dev_name = developer.get('name');
    var dev_age = developer.get('age');
        
    developer.birthday();
    
    // The .toJSON() method is a Backbone specific Model/Collection method which returns js object of specified Model
    console.dir(manager.toJSON())
    console.dir(developer.toJSON());
    
    
    /**
     * Collection Generation Example
     */
     
    var contacts = new Contacts([manager, developer]);
    
    // The .toJSON() method is a Backbone specific Model/Collection method which returns js object of specified Model
    console.log(contacts.toJSON());
    
    
    /**
     * View for <select> menu of Contacts
     */
    
    var contacts_view = new ContactsView({
        el: $('#view-contacts'),
        collection: contacts, // pass in the Collection into this View
        
        /**
         * View for displaying the selected Contact
         *
         * Originally I had created a global namespace property so I could access this View's "render()" method from within 'contacts_view' (/Views/Contacts.js)
         * And according to people smarter than I (i.e. Addy Osmani from Google) this was the most appropriate solution.
         * But I since discovered I could pass in additional data when creating a View instance and so that's what I've done here.
         */
         
        associated_view: new ContactView({
            el: $('#view-contact'),
            collection: contacts
        })
    });
    
    
    /**
     * View for <form> to add a new Contact
     */
    
    var add_contact = new AddContactView({
        el: $('#view-add'),
        collection: contacts // pass in the Collection into this View
    });
    
    
    /**
     * Lazy Load Models into Collection
     */
    
    contacts.fetch({
        add: true, // Prevent resetting the Collection (i.e. instead or clearing the Collection first we just add new Models on top of current set of Models),
    	error: function (collection, resp) {
        	// You can do something on error loading
    	},
    	success: function (collection, resp) {
        	// You can do something on successful loading
    	}
    });
    
    
    /**
     * Router/History API Examples
     */
    
    // Create new instance of our Routing Class
    var routing = new Routing();
    
    // Initialize the Router
    Backbone.history.start({ pushState: true });
    
});
```

…this breaks down to the following… 

* **We set-up configurations for RequireJS**  
Such as Backbone's dependencies - this is done using RequireJS' `shim` feature for libraries that aren't AMD compatible.  
&nbsp;  
You'll also notice we aren't using the Underscore library, instead we're using Lo-dash which is a better performing/API compatible utility library.

```js
requirejs.config({
    shim: {
        '../Utils/backbone': {
            deps: ['../Utils/lodash', '../Utils/jquery'], // load dependencies
            exports: 'Backbone' // use the global 'Backbone' as the module value
        }
    }
});
```
* **Specify dependencies and execute callback function once all are loaded**  
The dependencies we're loading are the relevant Models, Views, Collections required for this application to work - once loaded we can create new instances of them.

```js
require(['../Models/Contact', '../Collections/Contacts', '../Views/Contacts', '../Views/AddContact', '../Views/Contact', '../Routes/Routing', '../Utils/backbone'], 
function (Contact, Contacts, ContactsView, AddContactView, ContactView, Routing) {
```

* **Generate some data for the initial `<select>` menu population**  
We've loaded the `Contact` Model and we create two new instances of it and pass in the relevant properties for the Model data.

```js
/**
 * Model Generation Examples
 */

var manager = new Contact({
    id: _.uniqueId(),
    name: 'Mark McDonnell',
    age: 30
});

var developer = new Contact({
    id: _.uniqueId(),
    name: 'Ashley Banks',
    age: 23
});
```
* **Create a Collection that will hold the Model data we've just created**  
Backbone lets us pass through an Array of Models to instantiate the Collection with

```js
/**
 * Collection Generation Example
 */
     
var contacts = new Contacts([manager, developer]);
```

* **Create the View**  
You'll notice that when creating the view Backbone gives us the ability to pass in specific properties such as the HTML element we want to associate our View object with (in this case I'm passing in the `<select>` element).  
&nbsp;  
Backbone also lets us specify a Collection for this View to be associated with (this is so we can watch for any events - such as a adding/removing Models - so we can keep the View in sync with any data changes)  
&nbsp;  
Lastly, we pass in a custom property I've called `associated_view` and the value of this will be a new instance of `ContactView` (this View is the empty `<div>` which will be used to display the details of a specific record as selected by the user of our application). The reason I've passed in this View into another View is because I want the `contacts_view` to have access to the `associated_view` so when the user selects a Contact we can easily tell `contacts_view` to render the selected Contact data.

```js
/**
 * View for <select> menu of Contacts
 */

var contacts_view = new ContactsView({
    el: $('#view-contacts'),
    collection: contacts, // pass in the Collection into this View
    
    /**
     * View for displaying the selected Contact
     */
     
    associated_view: new ContactView({
        el: $('#view-contact'),
        collection: contacts
    })
});
```

* **Create a View for the `<form>` that will create new Model data**  
What you'll probably notice is that out of the three Views we have created instances for, they all use the same `contacts` Collection.

```js
/**
 * View for <form> to add a new Contact
 */
    
var add_contact = new AddContactView({
    el: $('#view-add'),
    collection: contacts
});
```

* **Load more Model data into our Collection**  
We initially loaded some Model data into our Collection when starting our application, but now we're using a Backbone provided method called `fetch` which lets us grab more data from the server and populate our Collection with that server data.

```js
/**
 * Lazy Load Models into Collection
 */

contacts.fetch({
    add: true, // Prevent resetting the Collection (i.e. instead or clearing the Collection first we just add new Models on top of current set of Models),
    error: function (collection, resp) {
        // You can do something on error loading
    },
    success: function (collection, resp) {
        // You can do something on successful loading
    }
});

```

###Fetching Collection Data

In the previous section we used `contacts.fetch` to grab Model data from the server.

To be able to understand how it works we need to look at the Collection module now. 

As we know in the main JavaScript file we required a load of dependencies such as Models, Views and Collections and the following is the Collection module we loaded - hopefully the comments will adequately explain what's going on… 

```js
// This Collection requires the 'Contact' Model and (obviously) Backbone
define(['../Models/Contact', '../Utils/backbone'], function (Contact) {
    
    // We set-up a collection of Contact Models
    // This is so we can manipulate the group of Models more easily
    var Contacts = Backbone.Collection.extend({
        // Backbone specific property
        model: Contact,
        
        // Backbone specific property
        url: '/Assets/Includes/Contacts.php',
        
        // Backbone specific method
        initialize: function(){
            // Collections fire the events 'add' and 'remove'
            this.on('add', this.model_added, this);
        },
        
        model_added: function (model) {
            // The View listens out for 'model:added'
            // Once it hears it, it updates the <select> menu to include the latest Model
            // We pass the latest Model through from here...
            this.trigger('model:added', model);
        },
        
        // We can't trigger a custom event from within the 'initialize' method
        // because the View listener wont be ready/set-up yet!
        // So we have to stick it inside a separate method which I call after I set-up the associated View
        populate: function(){
            this.trigger('contacts:populate');
        }
    });
    
    return Contacts;
    
});
```

You'll notice this Collection uses the Backbone specific property `url` which we've pointed to a PHP script (you would replace this with whatever back-end you prefer to use: Ruby, Python, ASP.NET etc). 

The PHP script we're using would normally connect to a database and return its data converted into JSON (JSON is the perfect data format for Backbone to process). But in this example we're simply just manually creating data and converting it to JSON… 

```php
<?php
    $json = array(
        array(
            "id" => 99,
            "name" => "Joe Bloggs",
            "age" => 12, 
            "address" => "9 Cables Street, London",
            "role" => "Manager"
        ),
        array(
            "id" => 98,
            "name" => "Dan Smith",
            "age" => 23, 
            "address" => "Bambridge Road, Essex",
            "role" => "Developer"
        ),
        array(
            "id" => 97,
            "name" => "Bradley Few",
            "age" => 22, 
            "address" => "Meeson Mead, Leads",
            "role" => "Developer"
        )
    );
    
    echo json_encode($json);
?>
```

…Backbone.js then populates the Collection with the new data it has 'fetched' from the server application.

You may have also noticed that back in our main JavaScript file (when we called the `fetch` method) we had specified a property called `add` and set its value to `true`. The reason we did this was because if we left that property out then when Backbone fetched the data from the server it would have cleared our Collection of all Model data and replaced it with the data from the server. Setting `add: true` means we don't overwrite any existing data but instead add the data on top of what's already there.

###Model Structure

So far we have a main script file where we have created new instances of some Backbone Views as well as created a Backbone Collection. We've populated that Collection with specific Model data, but now lets look at the Model itself that we've been using to construct our data from… 

```js
define(['../Utils/backbone'], function(){
    
    var Contact = Backbone.Model.extend({
        // Backbone specific object
        defaults: {
            name: 'No name provided',
            age: 0,
            address: 'No address provided'
        },
        
        // Backbone specific method
        initialize: function(){
            // Syntax: .on(type:property)
            this.on('change:age', function(){
                console.log(this.get('name') + '\'s age changed to ' + this.get('age'));
            });
            
            this.on('error', function (model, error) {
                console.warn('Error:', model, error);
            });
        },
        
        // Backbone specific method
        validate: function (attributes) {
            if (!attributes.id || attributes.id <= 0) {
                return 'An error has occurred? There should be an id generated!';
            }
            
            if (typeof attributes.age != 'number') {
                return 'Age needs to be a number';
            }
        },
        
        birthday: function(){
            var age = this.get('age');
            this.set({ 'age': ++age });
        }
    });
    
    return Contact;
    
});
```

…as you can see the only dependency this module has is Backbone.js itself. Don't worry about specifying Backbone.js in each module, when we run a build script using RequireJS' optimisation tool it'll only load one instance of Backbone. 

The reason we specify Backbone as a dependency in each of these module files (rather than just specifying it once in the top level main script file) is because if we decided to reuse this specific module in another project then it would be crystal clear what dependencies it has (I know in this example if you saw `Backbone.Model` then it would seem pretty obvious that Backbone.js would need to be loaded, but doing this is just good practice because some modules can have lots of dependencies and they wont always be that obvious, so it's best to get into the habit of specifying all dependencies for the sake of helpfulness).

Let's now break down the specific sections of the Model we've created:

* **First we specify default values**  
This is in case (when creating a new Model instance) we don't have all the values readily available

```js
defaults: {
    name: 'No name provided',
    age: 0,
    address: 'No address provided'
}
```

* **Carry out some initial set-up stuff when a new instance is created**  
In this example we want to set-up some event listeners for when the `age` property is changed as well as when an error occurs.

```js
initialize: function(){
    // Syntax: .on(type:property)
    this.on('change:age', function(){
        console.log(this.get('name') + '\'s age changed to ' + this.get('age'));
    });

    this.on('error', function (model, error) {
        console.warn('Error:', model, error);
    });
}
```

* **Validate data before actually making any changes**  
Backbone.js provides a `validate` method that returns your own specified error message.  
If the function doesn't return any value then it is assumed the data is valid.  
If the function returns *any thing* then whatever was returned is used as the error message.

```js
validate: function (attributes) {
    if (!attributes.id || attributes.id <= 0) {
        return 'An error has occurred? There should be an id generated!';
    }

    if (typeof attributes.age != 'number') {
        return 'Age needs to be a number';
    }
}
```

* **Create a custom `birthday` method**  
We use two Backbone methods: `get` and `set` to increment the age of the user.  
First we `get` the current age, then we `set` the age using the prefixed `++` increment operator

```js
birthday: function(){
    var age = this.get('age');
    this.set({ 'age': ++age });
}
```

So in our main script file you'll remember we used this custom method like so… 

```js
// Create a new Model instance
var developer = new Contact({
    id: _.uniqueId(),
    name: 'Ashley Banks',
    age: 23
});

// Now effectively set the user's age to 24
// i.e. the age was 23 and calling `birthday()` increments the age by one
developer.birthday();
```

###View Structures

So again, in our main script file we have created three new View instances. Lets now take a look at each of the View files so we can see what they're doing and how they work.

If you remember from our main script file we created the following View (well, this is two Views, one inside another)…

```js
/**
 * View for <select> menu of Contacts
 */
    
var contacts_view = new ContactsView({
    el: $('#view-contacts'),
    collection: contacts, // pass in the Collection into this View
        
    /**
     * View for displaying the selected Contact
     *
     * Originally I had created a global namespace property so I could access this View's "render()" method from within 'contacts_view' (/Views/Contacts.js)
     * And according to people smarter than I (i.e. Addy Osmani from Google) this was the most appropriate solution.
     * But I since discovered I could pass in additional data when creating a View instance and so that's what I've done here.
     */
         
    associated_view: new ContactView({
        el: $('#view-contact'),
        collection: contacts
    })
});
```

…the `contacts_view` was the `<select>` menu that held a list of names. The idea being the user selected a name from the list and the details for the selected user was displayed on the page.

So let's take a look at that particular View file… 

```js
define(['../Utils/backbone'], function(){
    
    var ContactsView = Backbone.View.extend({
        // Backbone specific method
        initialize: function (options) {
            this.select = this.$el.find('select');
            this.collection.on('contacts:populate', this.populate, this);
            this.collection.on('model:added', this.update, this);
            this.collection.populate(); // when this completes it triggers the custom event 'contacts:populate'
            this.associated_view = options.associated_view;
        },
        
        // Backbone specific 'events' management only applies to DOM elements (as this is a 'View' after all)
        // other custom events triggered are handled via 'this.on' within the initialize method 
        // because of this we have to use 'this.on' within a 'Collection'
        events: {
            'change select': 'display_selected'
        },
        
        populate: function(){
            var select = this.select; // scope of this changes within 'each' (refers to same thing as the 'model' argument)
            var frag = '';
            var option;
            
            this.collection.each(function(model){
                option = '<option value="' + model.cid + '">' + model.attributes.name + '</option>';
                frag += option;
            }, this);
            
            // We could of had the 'append' calls within the loop
            // but that would have meant multiple DOM interactions
            select.append(frag);
        },
        
        display_selected: function (event) {
            var targ = event.target;
            var selected_option = targ.options[targ.selectedIndex];
            var model = this.collection.getByCid(selected_option.value);
            
            // We call the 'render' method on the associated View we passed through when creating this View's instance
            this.associated_view.render(model);
        },
        
        // 'model' is passed through from Collection
        update: function (model) {
            var select = this.$el.find('select');
            var option = '<option value="' + model.cid + '">' + model.attributes.name + '</option>';
            select.append(option);
        }
    });
    
    return ContactsView;
    
});
```

…and let's break down what it's doing… 

* **Carry out some initial set-up stuff when a new instance is created**  
A few things going on here:  
&nbsp;  
We first grab the `<select>` element using Backbone's `$el` property (this property references the element we passed through to the View's `extend` method when creating a new instance. It's effectively a jQuery wrapped version of the element - which is why we're able to use jQuery's `find` method.  
&nbsp;  
Next we set-up a couple of event listeners (for custom events) - you'll remember the Collection triggers the custom events like so `this.trigger('model:added', model);` and `this.trigger('contacts:populate');`.  
&nbsp;  
This is where it gets a little weird I'm afraid. We now call the Collection's custom `populate` method. That method does literally nothing other than trigger the custom event `contacts:populate`. The reason that method even exists (and why we're calling it) is because we only want to populate the `<select>` menu when we know there is data available! We weren't able to trigger a custom event from the Collections `initialize` method (which would have been ideal, because once the Collection instance is created would have been a good time to trigger the custom event) and so we've had to create our own method within the Collection which we manually call from this View. It's a bit "arse about face" but that's just the way it is.  
&nbsp;  
Lastly we create a property for this instance that points to another View. The reason we do this is because later on in this View's `display_selected` method we need to call the associated View's `render` method.  
&nbsp;  
Because we're using AMD we've made our Views into separate modules/files, but because of this we aren't able to access code outside of a module (our module's code is protected) - so we're unable to access the `ContactView` View unless we pass around a reference to it like we've done here.

```js
initialize: function (options) {
    this.select = this.$el.find('select');
    this.collection.on('contacts:populate', this.populate, this);
    this.collection.on('model:added', this.update, this);
    this.collection.populate(); // when this completes it triggers the custom event 'contacts:populate'
    this.associated_view = options.associated_view;
}
```

* **Define some events**  
We specify a 'change' event for the `<select>` element  
which when triggered executes the `display_selected` method we've created for this View.

```js
events: {
    'change select': 'display_selected'
}
```

* **Custom `populate` function**  
This method builds up the content of the `<select>` menu.  
It does this by looping through the Collection data and accessing each individual Model stored within it.  
You'll notice that we set the value of each `<option>` element to be whatever the `cid` value for the Model is (the `cid` value is set automatically by Backbone on each Model)

```js
populate: function(){
    var select = this.select; // scope of this changes within 'each' (refers to same thing as the 'model' argument)
    var frag = '';
    var option;
            
   this.collection.each(function(model){
        option = '<option value="' + model.cid + '">' + model.attributes.name + '</option>';
        frag += option;
    }, this);
            
    // We could of had the 'append' calls within the loop
    // but that would have meant multiple DOM interactions
    select.append(frag);
}
```

* **Custom `display_selected` function**  
This methods works out which menu item was selected and then  
uses a Backbone specific method `getByCid` to access the required Model.  
We then call the `ContactView`'s `render` method and pass through the Model data that needs to be rendered.

```js
display_selected: function (event) {
    var targ = event.target;
    var selected_option = targ.options[targ.selectedIndex];
    var model = this.collection.getByCid(selected_option.value);
    
    // We call the 'render' method on the associated View we passed through when creating this View's instance
    this.associated_view.render(model);
}
```

* **Custom `update` function**  
Every time a Model is added to the Collection it triggers an `model:added` event.  
This event causes the View's `update` method to be executed, which inserts the new Model directly into the `<select>` menu.

```js
update: function (model) {
    var select = this.$el.find('select');
    var option = '<option value="' + model.cid + '">' + model.attributes.name + '</option>';
    select.append(option);
}
```

###Template for the Associated View

Let's take a look at the associated View that we created…

```js
define(['../Utils/backbone'], function(){
    
    var ContactView = Backbone.View.extend({
        render: function (model) {
            var model = model || this.collection.models[0];
            var attributes = model.attributes;
            var template = $('#contact_template');
            var compile = _.template(template.html(), {
                id: attributes.id,
                name: attributes.name,
                age: attributes.age,
                address: attributes.address
            });
            
            this.$el.html(compile);
        }
    });
    
    return ContactView;
    
});
```

…as you can see: it's a lot simpler! All it consists of is a `render` method.

The purpose of this View is to display the selected contact details.

When this method is called we pass it the Model that we want to display and then we use Underscore's `_.template` method to render the data in HTML.

If you've not used a template library before then it simply is just a text file which contains HTML tags where the dynamic content areas are replaced with specific delimiter tags. You would then pass through an object of data to the template library in which is would use to compile the template's HTML content so its tags are replaced with content from the data object.

For example, if the template was `<p>{{fullname}}</p>` (this assumes the template library you're using requires the delimiter tags to be double braces `{{ }}` then you could pass in a data object like `{ fullname: 'Mark' }` and then when the template is compiled it would return the HTML like this: `<p>Mark</p>`.

Underscore's `_.template` method uses different tags: `<%=fullname%>` and although the internal workings for each template library can be different (each one trying to out perform the other) the general principle of how they work stays the same.

In this example we've gotten the content of the template file from within the HTML page itself. If you remember, the main HTML page we had contained a `<script>` tag with the `type` attribute value set to `text/template`. This odd type value causes the browser to ignore it (a browser wont try to execute the HTML content within that script tag as JavaScript because it doesn't recognise the type value). 

Ideally though you'd have the template content as a separate file and then AJAX in that template file. The reason I recommend keeping your template in a separate file is because relying on the browser to not execute a script just because it has an unknown attribute value is very fragile and likely to break in future, but for the purpose of this example it'll do.

###Adding a new Model

We have one more View left to look at, this is the HTML `<form>` element which lets us add a new contact to the list of contacts…

```js
define(['../Models/Contact', '../Utils/backbone'], function (Contact) {
    
    var AddContactView = Backbone.View.extend({
        // Built-in object for handling DOM events
        events: {
            'click input[type=submit]': 'add_contact'
        },
        
        add_contact: function (e) {
            e.preventDefault(); // prevent form from submitting
            
            var message = document.getElementById('message-success');
            var errors = [];
            var fullname = this.el.fullname.value;
            var age = this.el.age.value;
            var address = this.el.address.value;
            var contact;
    		
    		// This regex tests for a first name with at least two characters, 
    		// followed by an optional middle name with at least two characters (we use a non-capturing group to save the regex engine some work), 
    		// followed by the last name with at least two characters.
    		// This regex allows the first-middle name (and the middle-last) to be joined by a hypen (e.g. Georges St-Pierre or Georges-St Pierre)
    		if (!/[\w-]{2,}(?:\s\w{2,})?[\s-]\w{2,}/.test(fullname)) {
    			errors.push('Name was invalid');
    		} 
    		
    		// We allow ages from 1-999
    		if (!/\d{1,3}/.test(age)) {
    			errors.push('Age was invalid (should be numeric value only)');
    		}
    		
    		if (!address.length) {
        		// Provide a default for the address (mainly because it's awkward to validate this type of field)
    			address = 'No address provided';
    		}
    		
    		// If there are any errors then we can't proceed
    		if (errors.length) {
    			// If the success message (from a previous successful record added) is still visible
    			// then remove it to save from confusing the user.
    			var success;
    			if (success = document.getElementById('message-success')) {
        			this.el.removeChild(success); // this.el provided by Backbone
    			}
    			
    			// Display errors
    			alert(errors.join('\n'));
    		} else {
    			// There should be an AJAX function to post data to server-side script (for storing in db)
    			// Backbone.Model.save() might be a built-in handler for this, I'm not sure yet?
    			
    			// Create a new Model
    			contact = new Contact({
        			id: _.uniqueId(),
                    name: fullname,
                    age: +age, // ensure data is an Integer
                    address: address
                });
    			
    			// Add the new Model into the Contacts Collection
    			// This should trigger an 'add' event which means the record is inserted into the <select>
                this.collection.add(contact);
    			
    			// Display success message and reset the form
    			this.el.reset(); // this.el provided by Backbone
    			
    			// Don't want to see a massive long list of 'Record added successfully!' messages
    			// So if one is already there then just remove it first
    			if (message) {
        			message.parentNode.removeChild(message);
    			}
    			
    			// Create an element to hold our success message
    			var doc = document;
    			var div = doc.createElement('div');
    			var txt = doc.createTextNode('Record added successfully!');
    			
    			div.id = 'message-success';
    				
    			div.appendChild(txt);
    			this.el.appendChild(div); // this.el provided by Backbone
    		}
        }
    });
    
    return AddContactView;
    
});
```

…this does a lot of stuff so lets break it down…

* **Event listeners**  
We set-up a listener for the `click` event for the form's submit button which calls the `add_contact` method.

```js
events: {
    'click input[type=submit]': 'add_contact'
}
```

* **Add a new contact and validate the user input**  
We call the `add_contact` method and immediately call `e.preventDefault` which stops the form from submitting and thus refreshing the page (which we don't want to have happen as we'll lose the state of the page).

```js
add_contact: function (e) {
    e.preventDefault();
            
    var message = document.getElementById('message-success');
    var errors = [];
    var fullname = this.el.fullname.value;
    var age = this.el.age.value;
    var address = this.el.address.value;
    var contact;
    		
    // This regex tests for a first name with at least two characters, 
    // followed by an optional middle name with at least two characters (we use a non-capturing group to save the regex engine some work), 
    // followed by the last name with at least two characters.
    // This regex allows the first-middle name (and the middle-last) to be joined by a hypen (e.g. George St-Pierre or Georges-St Pierre)
    if (!/[\w-]{2,}(?:\s\w{2,})?[\s-]\w{2,}/.test(fullname)) {
        errors.push('Name was invalid');
    } 
    		
    // We allow ages from 1-999
    if (!/\d{1,3}/.test(age)) {
        errors.push('Age was invalid (should be numeric value only)');
    }
    		
    if (!address.length) {
        address = 'No address provided';
    }
```

* **Handle any invalid data**  
If the success message (from a previous successful record added) is still visible then remove it to save from confusing the user.  
Then display any errors found.

```js
if (errors.length) {
    var success;
    if (success = document.getElementById('message-success')) {
        this.el.removeChild(success); // this.el provided by Backbone
    }
    			
    // Display errors
    alert(errors.join('\n'));
}
```

* **Add a new contact**  
This adds the new Model data, then updates the `<select>` menu, then inserts a success message to let the user know the details were added without issue.

```js
else {
    // Create a new Model
    contact = new Contact({
        id: _.uniqueId(),
        name: fullname,
        age: +age, // ensure data is an Integer
        address: address
    });
    			
    // Add the new Model into the Contacts Collection
    // This should trigger an 'add' event which means the record is inserted into the <select>
    this.collection.add(contact);
    			
    // Display success message and reset the form
    this.el.reset(); // this.el provided by Backbone
    			
    // Don't want to see a massive long list of 'Record added successfully!' messages
    // So if one is already there then just remove it first
    if (message) {
        message.parentNode.removeChild(message);
    }
    			
    // Create an element to hold our success message
    var doc = document;
    var div = doc.createElement('div');
    var txt = doc.createTextNode('Record added successfully!');
    			
    div.id = 'message-success';
    				
    div.appendChild(txt);
    this.el.appendChild(div); // this.el provided by Backbone
}
```

###URL Routing

I've not built in any real routing logic into this particular example application, but I've included below part of the example from the Backbone website so you at least have an idea of how it works...

```js
define(['../Utils/backbone'], function(){
    
    var Routing = Backbone.Router.extend({
        routes: {
            'test': 'test',
            'search/:query/:page': 'search'
        },
        
        test: function(){
            console.log('User has accessed this app from /test/');
        },
        
        search: function (query, page) {
            console.log(query, page);
        }
    });
    
    return Routing;
    
});
```

...what we have here is a new `Router` instance where we then defines a set of 'routes' into your application. The routes we have defined are:

* http://backbone:8888/#test  
If we access our application with this URL we'll see a log message of `User has accessed this app from /test/`

* http://backbone:8888/#search/testing/p7  
If we access our application with this URL we'll see a log message of `testing, p7`

...to initialise this Router system we need to trigger it via the main script file... 

```js
/**
 * Router/History API Examples
 */

// Create new instance of our Routing Class
var routing = new Routing();

// Initialize the Router
Backbone.history.start({ pushState: true });
```

Instead of displaying basic log messages we would use this routing feature to load a specific application state.

So for example, if we set-up the relevant route then the user could access the application via a URL like: http://backbone:8888/#contact/99 and that could load the contact details for Model item 99.

You also have the facility to take advantage of HTML5's `pushState` which removes the need for URL rewriting but does require extra application logic/work. The default for `Backbone.Router` is to use hashbangs.

If a user accesses the application with a hashbang URL and the application is set-up to use `pushState` then Backbone will update the URL from the hashbang to the proper HTML5 variation.

##Wrap-up

So this is where we end. 

We've got our three Views set-up, we have our Model defined and contacts created from it and we created a Collection based on that Model type.

The Views and Models/Collection are tied together and the application is functioning as it should be.

There are lots of things we could do to improve this example, but as a basic example of an MVC style application built using Backbone.js I think it does everything it needs to.

##Conclusion

When you start using Backbone.js the concepts can be a bit confusing, but you quickly learn your way around. 

Oddly, trying to *explain* how to use Backbone.js is harder than actually using it! So I would suggest just start using it.

The main [Backbone.js website](http://www.backbonejs.org/) is the best documentation available and is the ideal place to start after here. 

Also, the following links are excellent resources to understand the MV* concepts discussed at the start of this post.

##Links**

* [Backbone Fundamentals](http://addyosmani.com/blog/backbone-fundamentals/)
* [Short Musings On JavaScript MV* Tech Stacks](http://addyosmani.com/blog/short-musings-on-javascript-mv-tech-stacks/)
* [Understanding MVC And MVP (For JavaScript And Backbone Developers)](http://addyosmani.com/blog/understanding-mvc-and-mvp-for-javascript-and-backbone-developers/)
* [Understanding MVVM – A Guide For JavaScript Developers](http://addyosmani.com/blog/understanding-mvvm-a-guide-for-javascript-developers/)
* [Digesting JavaScript MVC – Pattern Abuse Or Evolution?](http://addyosmani.com/blog/digesting-javascript-mvc-pattern-abuse-or-evolution/)
* [Journey Through The JavaScript MVC Jungle](http://addyosmani.com/blog/javascript-mvc-jungle/)