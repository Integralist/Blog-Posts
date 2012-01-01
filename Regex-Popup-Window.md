Regex Popup Window
==================

This is a quick post to show you how to open up external website links within a pop-up window (without adding extra non semantic mark-up to your HTML code).

In the past I’ve heard a lot of people talk about adding either custom attributes or using existing attributes such as `rel` as a hook for your JavaScript code to find links that should open in a pop-up window. I disagree, and suggest using Regular Expressions (regexes) along with some procedural code to help figure this out for you (it will save you the time and hassle of adding this extra mark-up yourself).

This solution is an updated version. The previous version was recklessly using [capture groups](http://www.regular-expressions.info/brackets.html) when really they should have been using non-capturing groups (as the brackets in the following solution are solely for applying [quantifiers](http://www.regular-expressions.info/repeat.html) and [optional items](http://www.regular-expressions.info/optional.html)). The reason you should use capturing groups sparingly is to do with performance and saving the regex engine from having to remember content within the capturing groups. The Regular Expression checks for things like whether the URL is an SSL protected URL and uses a [negative look ahead assertion](http://www.regular-expressions.info/lookaround.html) to make sure that it doesn’t incorrectly match URL’s that *appear* to be external (e.g. they start with a http) but in fact actually are links to the current website URL (this happens a lot with Content Management Systems where a user will copy and paste the full URL to one of their own pages).

The (original) Regex Solution
-----------------------------

`^http(?:s)?:\/\/(?!(?:www.)?integralist)`

Example Code
------------

```js
/**
 * The Integralist global namespace object.
 *
 * @class Integralist
 * @singleton
 * @static
 */
function Integralist() {
   // Constructor
}

/**
 * Augment the Integralist class so it includes a method
 * which finds all <a> elements that link to an external website
 * and sets them to open in a popup window
 */
Integralist.prototype.external = function() {
   var that = this; // Required to keep scope within the following Closure
   this.settings = 'location=yes,resizable=yes,width=' + screen.availWidth + ',height=' + screen.availHeight + ',scrollbars=1,left=0,top=0';
   this.popup = function(node) {
      var url = node.href;
      return function() {
         window.open(url, 'external' , that.settings);
         return false;
      };
   };

   var a = document.getElementsByTagName('a'), // Private variable to store HTMLCollection of all <a> elements
       len = a.length, // Store array length in variable
       pattern = /^http(?:s)?:\/\/(?!(?:www.)?integralist)/; // RegExp pattern to match any external URL's but not the current website

   // Loop through the array checking for any A elements that link to an external URL
   while (len--) {
      var element = a[len].getAttribute('href');
      if (pattern.test(element)) {
         a[len].onclick = this.popup(a[len]);
      }
   }
};

// Create a singleton of the Integralist Class
var Integralist = new Integralist();

// Trigger 'external' method
window.onload = Integralist.external;
```

Update 1
--------

Tweaked the regex again to make it simpler. Decided using a character class for just a single dot character was pointless, might as well just escape the dot. Also got rid of the non-capturing group around the s in https and just used an optional token `?` on its own as again it was pointless wrapping a single character in a non-capturing group.

`^https?:\/\/(?!(?:www\.)?integralist)`

Update 2
--------

I’ve ended up tweaking the regex again to take into account files that don’t start with http but should still open in a pop-up window. For example if a website links to a self hosted PDF file it might store it in the following directory path `Assets/Documents/MyFile.pdf`. First thing I did was add a case-insensitive modifier flag (which I simply forgot last time). Now to work around the issue of linking to internal documents that should open in a pop-up I place a non-capturing group after the opening anchor `^`. Within this group it has a character class that allows a period `.` and a forward slash `/` and uses a quantifier `+` so it matches 1 or more times. I then make the whole group optional. Next, we take the original regex and wrap it in a non-capturing group and place it after the preceding code. Inside the non-capturing group, at the start we add the name of the directory we are looking for (in this case I just need to look for the word “Assets” and I know it’s going to link to a document and not a HTML file). I then put in the alternator meta-character so it will look for “Assets” and if it can’t find it the regex engine will backtrack to this point and try the rest of the regex which we’ve already discussed. So the final regex looks like this…

`^(?:[.\/]+)?(?:Assets|https?:\/\/(?!(?:www\.)?integralist))`