#Backbone Validation

* Introduction
* Dependencies
* HTML set-up
* JavaScript set-up
* Validation Module
* Conclusion

##Introduction

A while ago I had any issue with validating a form using the Backbone.Model's `validate` method and so I went to the official Backbone GitHub issues area to raise a query. The problem I had isn't necessarily important but the ultimate result I got from the discussion with the maintainers of Backbone was that validating a form using a Model wasn't really the way you should use a Model.

To me this didn't make a lot of sense because a form is just data and a Model holds data and so I would have thought using the built-in `validate` method of the Model would have been the best way to validate the form data. But apparently that's not considered a best practice using Backbone.

So I decided to create a validation module that would make validating a form (and displaying errors) a little bit easier, because lets face it, validating forms are tedious and boring.

You can download the [full project code from GitHub](https://github.com/Integralist/Backbone-Validation-View)

##Dependencies

* Backbone (obviously, so this includes the dependencies required by Backbone)
* [Hogan.js](http://twitter.github.com/hogan.js/) (for loading the error messages)

Now although I've used Backbone/Hogan you don't have to. The code is straight forward enough to port over to use whatever you like. You could rewrite the code to not use a framework at all. The principles are very simple.

##HTML set-up

OK, so the HTML requires a bit of setting up. Nothing major, but we utilise specific classes and custom data attributes in the HTML to help us validate the form.

For example, we mark-up any mandatory fields with a class of `js-mandatory-field` - you'll notice I've prefixed the class with 'js-' so other developers know that this class has no styles associated with it. It's literally only used as a JavaScript hook.

We also use a custom data attribute `data-message` to store specific validation error messages. I prefer having custom messages because otherwise at some point you'll want to validate a field that doesn't conform to the known validation methods. So having a custom message for each field gives better feedback to the user.

So as an example here is a form with the mandatory class and the custom data attribute

```
<input name="card" class="js-mandatory-field" data-message="Please ensure you enter a valid credit card number">
```

Our full form code looks like this…

    <form action="http://www.google.com/" method="post" id="js-form">
        <p>Name: <input name="fullname" class="js-mandatory-field" data-message="Please ensure you enter your full name"></p>
        <p>Date of Birth: <input name="dob" placeholder="dd/mm/yyyy" class="js-mandatory-field" data-message="Please ensure you enter a valid date format"></p>
        <p>Age: <input name="age" type="number" class="js-mandatory-field" data-message="Please ensure you enter a valid age"></p>
        <p>Email: <input name="email" type="email" class="js-mandatory-field" data-message="Please ensure you enter a valid email"></p>
        <p>Mobile: <input name="mobile" class="js-mandatory-field" data-message="Please ensure you enter a valid mobile number"></p>
        <p>Password (8 alpha-numerical characters, at least either 1 number or 1 text character): <input name="password" type="password" class="js-mandatory-field" data-message="Please ensure you enter a password that matches the criteria"></p>
        <p>Credit Card: <input name="card" class="js-mandatory-field" data-message="Please ensure you enter a valid credit card number"></p>
        <p>To proceed please tick this box: <input name="proceed" type="checkbox" class="js-mandatory-field" data-message="Please ensure you tick the box to proceed"></p>
        <p><input type="submit" value="Submit Form"></p>
    </form>

##JavaScript set-up

Next let's look at our JavaScript set-up: we're utilising AMD to help keep our scripts modular and I'm using the [Curl.js AMD loader](https://github.com/cujojs/curl).

So in our HTML file we'll load up the JavaScript files required…

    <script src="Assets/Scripts/curl.js"></script>
    <script src="Assets/Scripts/app.js"></script>

Inside our app.js file we set-up a configuration object which handles the paths to the different dependencies we'll be using. We then load the 'Validation' module (which is a Backbone.View)…

    var config = {
        baseUrl: './Assets/Scripts/',
        pluginPath: 'plugins',
        paths: {
            'jquery': 'Libraries/jquery',
            'lodash': 'Libraries/lodash',
            'backbone': 'Libraries/backbone'
        },
        preloads: ['jquery', 'lodash']
    };

    /*
        The `exports` suffix ensures we receive a reference to the global Backbone object
        It also provides a way to check that the script loaded correctly.
        Without the `exports` suffix, there's no way to tell if IE or Opera 404'ed.
     */
    curl(config, ['jquery', 'lodash', 'js!backbone!exports=Backbone'], init, error);

    function init ($, _, Backbone) {
        curl('Views/Validation');
    }

    function error (err) {
        console.warn(err);
    }

Now we have our JavaScript set-up lets move onto the Backbone.View that will handle the validation of our form.

##Validation Module

We will go through each of the methods within this module so we can better understand what they're doing. There are four main methods and the rest are different validation methods.

The four main methods are:

* `get_template`
* `validate`
* `process_errors`
* `display_errors`

The validation methods are:

* `validate_text`
* `validate_number`
* `validate_date`
* `validate_email`
* `validate_mobile`
* `validate_password`
* `validate_cardnumber`
* `validate_checkbox`

So within the initialisation stage of the View we do a few things…

    initialize: function(){
        // Self explanatory: stores all mandatory fields
        this.mandatory_fields = $('.js-mandatory-field');

        // This holds the template file we'll compile with data pulled from server
        this.template = null;

        // This holds the actual rendered template content
        this.template_content = null;

        // We'll go ahead and grab the error message template file (no point waiting until the last minute and making the user wait).
        this.get_template();
    }}

* Store a reference to all mandatory fields
* Create a `template` property
* Create a `template_content` property
* Call the `get_template` method

The reason we call the `get_template` method straight away (before the form has been submitted) is because it's a safe assumption that on a form, the user is likely to make an error in the data they submit so why not just go ahead and grab the template we'll need to render and display errors with.

The `get_template` method is called immediately but later on we also check to see if we need to try calling it again (e.g. when the user actually submits the form we check to see if `this.template` holds any value and if it does then we use it, otherwise we call `get_template` again to grab the data) so this is why we check within that method for a `callback` to be provided (when the template is loaded we want to continue on and process the errors found)

    get_template: function (callback) {
        $.ajax({
            url: 'Assets/Templates/FormErrors.txt',
            dataType: 'html',
            success: _.bind(function (tmp) {
                this.template = hogan.compile(tmp);

                if (callback) {
                    callback();
                }
            }, this)
        });
    }

We then listen out for the form submission event to happen and call the `validate` method to handle the event…

    events: {
        'submit': 'validate'
    }

The `validate` method creates an Array to hold any errors we encounter. The way we determine if there are any errors is to loop through all the mandatory fields and to check the name attribute value to see if we can find a match to known values. If we can't find a match then we just validate the field to make sure it has a value. If we do find a match (e.g. if the name attribute is "email") then we know which validation method to pass the field on to validate against.

If the validation method returns `true` then that means an issue was found and so the field failed the validation and we store the specific error message for that field in the `errors` Array.

After we've been through all the mandatory fields we check to see if the `errors` Array has any items, if it does then we know we need to process some errors and call `this.process_errors` and pass the `errors` Array through to that method.

    validate: function(){
        var errors = [];
        var method;
        
        // Loop through all the fields checking for validation errors
        this.mandatory_fields.each(_.bind(function (index, item) {
            // First we check what the current field is and validate it against a specific method
            switch (item.name) {
                case 'dob':
                    method = 'validate_date';
                    break;
                case 'age':
                    method = 'validate_number';
                    break;
                case 'email':
                    method = 'validate_email';
                    break;
                case 'mobile':
                    method = 'validate_mobile';
                    break;
                case 'password':
                    method = 'validate_password';
                    break;
                case 'card':
                    method = 'validate_cardnumber';
                    break;
                case 'proceed':
                    method = 'validate_checkbox';
                    break;
                default:
                    method = 'validate_text';
            }

            if (this[method](item)) {
                // Store just the messages and not the element itself (we only need the list of messages)
                errors.push(item.getAttribute('data-message'));
            }
        }, this));

        // If there are any errors then display warning messsage to the user
        if (errors.length) {
            this.process_errors(errors);
            return false;
        } else {
            return true;
        }
    }

The first thing the `process_errors` method does is it makes sure we are only dealing with unique error messages. For example if we have a set of 'sort code' fields (which are typically three separate fields) they will all have the same error message but we don't want that error message displayed three times so the `_.unique` method ensures our errors are unique.

After that we need to make sure that Hogan.js has the error messages in a format it can handle so we use the `map` method to convert the data to the relevant format required.

Next we need to convert the jQuery collection into an actual Array for Hogan.js to be able to loop through it.

Finally we check if the template file has been loaded already or not. If it hasn't then we load it via ajax and then call the `display_errors` method.

    process_errors: function (errors) {
        /*
            Clean the errors list so we only have unique data.
            This happens for things like the postcode validation (no point showing the postcode field error message twice)
         */
        var clean_list = _.unique(errors);

        // The list needs to be converted into an Object for Hogan.js to compile the data into its template
        var converted_list = $(clean_list).map(function (index, item) {
            return {
                error: item
            };
        });

        // Spent far longer on this than I wanted to, but this is it: jQuery collection is not an Array and so needs to be converted to an Array for Hogan.js to compile it
        var final_list = jQuery.makeArray(converted_list);

        // If the template file has been stored already then we'll just proceed to render the content
        if (this.template) {
            this.display_errors({ errors: final_list });
        } 
        // Otherwise grab the template file and then render the content
        else {
            // Have to rebind the `this` value as otherwise it would be equal to the global object (Window)
            this.get_template(_.bind(function(){
                this.display_errors({ errors: final_list });
            }, this));
        }
    }

The `display_errors` method takes in the object of errors we've created and tries to compile it into the template file using Hogan.js

But the first thing the method does is check if any errors already exist in the page. If it finds an errors element then it removes it.

We then compile the data into the template file and insert it into the page (thus displaying the errors to the user).

We then update the URL hash to include the id value of the form errors element we've just inserted into the page. This is because on a long form the errors are displayed at the top of the form, but the user may well not see it because the page only shows the bottom half the form, so we redirect the user back up to the top of the form to see the errors.

I know some people will disagree with doing that and would prefer to display an error next to each field but I find that makes the design of the page a lot more complicated, especially when dealing with multiple screen dimensions. I personally prefer to have a single place to display all errors.

    display_errors: function (data) {
        // First remove any errors that might already be on the page
        var existing_list = $('#js-formerrors');
        if (existing_list.length) {
            existing_list.remove();
        }
        
        // Render the data into the template
        this.template_content = this.template.render(data);
        
        // Insert the error messages into the page
        this.$el.prepend(this.template_content);

        /*
            Focus page to the error message element.
            Had issue with WebKit where it wouldn't reposition window to the named anchor unless we reset the hash back to nothing and then set it again to the named anchor
         */
        window.location.hash = '';
        window.location.hash = 'js-formerrors';
    }

The full module code is as follows...

    define(['../Utils/Templating/hogan'], function (hogan) {
        
        var Validation = Backbone.View.extend({
            initialize: function(){
                // Self explanatory: stores all mandatory fields
                this.mandatory_fields = $('.js-mandatory-field');

                // This holds the template file we'll compile with data pulled from server
                this.template = null;

                // This holds the actual rendered template content
                this.template_content = null;

                // We'll go ahead and grab the error message template file (no point waiting until the last minute and making the user wait).
                this.get_template();
            },

            el: $('#js-form'),

            events: {
                'submit': 'validate'
            },

            get_template: function (callback) {
                $.ajax({
                    url: 'Assets/Templates/FormErrors.txt',
                    dataType: 'html',
                    success: _.bind(function (tmp) {
                        this.template = hogan.compile(tmp);

                        if (callback) {
                            callback();
                        }
                    }, this)
                });
            },

            validate: function(){
                var errors = [];
                var method;
                
                // Loop through all the fields checking for validation errors
                this.mandatory_fields.each(_.bind(function (index, item) {
                    // First we check what the current field is and validate it against a specific method
                    switch (item.name) {
                        case 'dob':
                            method = 'validate_date';
                            break;
                        case 'age':
                            method = 'validate_number';
                            break;
                        case 'email':
                            method = 'validate_email';
                            break;
                        case 'mobile':
                            method = 'validate_mobile';
                            break;
                        case 'password':
                            method = 'validate_password';
                            break;
                        case 'card':
                            method = 'validate_cardnumber';
                            break;
                        case 'proceed':
                            method = 'validate_checkbox';
                            break;
                        default:
                            method = 'validate_text';
                    }

                    if (this[method](item)) {
                        // Store just the messages and not the element itself (we only need the list of messages)
                        errors.push(item.getAttribute('data-message'));
                    }
                }, this));

                // If there are any errors then display warning messsage to the user
                if (errors.length) {
                    this.process_errors(errors);
                    return false;
                } else {
                    return true;
                }
            },

            validate_text: function (field) {
                /*
                    If the length is zero (meaning the field is empty) then zero will coerce to false 
                    so we return the opposite of that using the ! operator (so the first part of the following condition is met) 
                    and if the length is greater than zero then the regex checks to see if the content isn't just empty spaces.

                    If there was an error then we return true, if there wasn't an error then undefined will be returned (which coerces to false)
                 */
                if (/^\s+$/.test(field.value) || !field.value.length) {
                    return true;
                }
            },

            validate_number: function (field) {
                if (/^\D+/.test(field.value) || !field.value.length) {
                    return true;
                }
            },

            validate_date: function (field) {
                /*
                    The regex allows either double figure formats or singular...
                    00/00/00
                    0/0/0
                 */
                if (!/^\d{1,2}\/\d{1,2}\/\d{1,2}$/.test(field.value) || !field.value.length) {
                    return true;
                }
            },

            validate_email: function (field) {
                if (field.value.indexOf("@") === -1 || !field.value.length) {
                    return true;
                }
            },

            validate_mobile: function (field) {
                /*
                    The regex allows the following formats:
                        +44 07000000000
                        07000000000
                        +4407000000000
                    
                    So there is an optional +000 country code at the start (wrapped in a non-capturing group)
                    We then allow for an optional space after the optional country code
                    Finally we allow for 11 digits (no spaces - but we also strip out any spaces found before running the regex so the 'no spaces' thing doesn't really matter)
                 */
                var strip_spaces = field.value.replace(' ', ''); // some people seem to add odd spacing when enterin numbers?
                if (!/^(?:\+\d{1,3})?\s?\d{11}$/.test(strip_spaces) || !field.value.length) {
                    return true;
                }
            },

            validate_password: function (field) {
                /*
                    The regex makes sure there is at least 8 alpha-numerical characters
                    And that at least one of those values is a number
                    And that at least one of those values is a text character
                    We use a positive lookahead (which checks to see if a sub pattern matches a specific position)
                    The lookahead checks for any character (zero or more times) is followed by a digit (e.g. making sure there is at least one digit)
                    The lookahead then checks for any character (zero or more times) is followed by a text character (e.g. making sure there is at least one text character)
                    Finally after the two lookaheads we have the standard regex which makes sure there is at least 8 alpha-numerical characters
                 */
                if (!/^(?=.*\d)(?=.*[a-z])\w{8,}/i.test(field.value)) {
                    return true;
                }
            },

            validate_cardnumber: function (field) {
                function luhn (cardnumber) {
                    // Build an array with the digits in the card number
                    var getdigits = /\d/g;
                    var digits = [];
                    var match;
                    
                    while (match = getdigits.exec(cardnumber)) {
                        digits.push(parseInt(match[0], 10));
                    }
                    
                    // Run the Luhn algorithm on the array
                    var sum = 0;
                    var alt = false;
                    
                    for (var i = digits.length - 1; i >= 0; i--) {
                        // On every other number in the card sequence...
                        if (alt) {
                            digits[i] *= 2; // multiple the number by itself
                            
                            // If the number is now over 9 then we'll minus 9 from the number
                            if (digits[i] > 9) {
                                digits[i] -= 9;
                            }
                        }
                        
                        // Add this digit onto the current total sum
                        sum += digits[i];
                        
                        // Alternate
                        alt = !alt;
                    }
                    
                    /*
                        The individual card numbers (after the above algorithm is applied and then when added together) 
                        should be possible to divide by 10 with zero left over
                     */
                    if (sum % 10 == 0) {
                        return true;
                    } else {
                        return false;
                    }
                }
                
                // The following regex was actually borrowed from The Regular Expression Cookbook (co-written by the regex legend @stevenlevithan)
                if (!/^(?:4[0-9]{12}(?:[0-9]{3})?|5[1-5][0-9]{14}|6(?:011|5[0-9][0-9])[0-9]{12}|3[47][0-9]{13}|3(?:0[0-5]|[68][0-9])[0-9]{11}|(?:2131|1800|35\d{3})\d{11})$/g.test(field.value)) {
                    return true;
                } else {
                    // If the card number appears to be valid we then run the Luhn test
                    if (!luhn(field.value)) {
                        return true;
                    }
                }
            },

            validate_checkbox: function (field) {
                if (!field.checked) {
                    return true;
                }
            },

            process_errors: function (errors) {
                /*
                    Clean the errors list so we only have unique data.
                    This happens for things like the postcode validation (no point showing the postcode field error message twice)
                 */
                var clean_list = _.unique(errors);

                // The list needs to be converted into an Object for Hogan.js to compile the data into its template
                var converted_list = $(clean_list).map(function (index, item) {
                    return {
                        error: item
                    };
                });

                // Spent far longer on this than I wanted to, but this is it: jQuery collection is not an Array and so needs to be converted to an Array for Hogan.js to compile it
                var final_list = jQuery.makeArray(converted_list);

                // If the template file has been stored already then we'll just proceed to render the content
                if (this.template) {
                    this.display_errors({ errors: final_list });
                } 
                // Otherwise grab the template file and then render the content
                else {
                    // Have to rebind the `this` value as otherwise it would be equal to the global object (Window)
                    this.get_template(_.bind(function(){
                        this.display_errors({ errors: final_list });
                    }, this));
                }
            },

            display_errors: function (data) {
                // First remove any errors that might already be on the page
                var existing_list = $('#js-formerrors');
                if (existing_list.length) {
                    existing_list.remove();
                }
                
                // Render the data into the template
                this.template_content = this.template.render(data);
                
                // Insert the error messages into the page
                this.$el.prepend(this.template_content);

                /*
                    Focus page to the error message element.
                    Had issue with WebKit where it wouldn't reposition window to the named anchor unless we reset the hash back to nothing and then set it again to the named anchor
                 */
                window.location.hash = '';
                window.location.hash = 'js-formerrors';
            }
        });

        new Validation();

    });

##Conclusion

OK, so that's our full validation method! It looks big but when you break it down it's not actually that complicated.

It works for me and has made my life a hell of a lot easier whenever I've needed to validate a form. Hopefully it helps someone else, or inspires some one to take the principles shown here and to build their own variation so their life is a little easier when dealing with forms.