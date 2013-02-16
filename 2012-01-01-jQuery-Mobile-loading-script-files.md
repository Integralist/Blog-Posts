#jQuery Mobile - loading script files

I’m working on a jQuery Mobile web application and I need to load specific JavaScript files for each page.

This is a common thing to do and if you check the jQuery Mobile forum you’ll see lots of people are suffering from the same issue that occurs when trying to achieve this, which is that when loading a page using ajax, jQuery is stripping out the `<script>` tags - I assume for security reasons to help protect the user, but then if they were doing that then they should be providing the user with a way to disable that feature as in my case I know the scripts I’m loading are safe.

So far on the jQuery Mobile Forums I’ve only really seen the same ‘solution’ proposed over and over which is to have all your JavaScript in a single script file that you include on every page of your application. In my opinion: that stinks!

I potentially will have LOTS of JavaScript code (in total) to load by the time my application is finished and the only solution proposed so far has been “hey, just load it all together on every page”.

My solution: have a single script file that yes is included on every page of your application but acts as nothing more than a ‘bootstrapper’ file which detects the current page and then inserts the JavaScript file(s) into the DOM.

Example is as follows…

First we need to write a function to insert our script(s):

	function insertScript(script, container) {
		var elem = document.createElement('script');
		elem.type = 'text/javascript';
		elem.src = 'Assets/Scripts/' + script + '.js';
		container.appendChild(elem);
	}

Next we need to detect the current page (details are in the code comments):

	// The 'pagechange' event is triggered after the changePage() request has finished loading the page into the DOM 
	// and all page transition animations have completed.
	// See: https://gist.github.com/1336327 for some other page events
	$(document).bind('pagechange', function(event){

		// grab a list of all the divs's found in the page that have the attribute "role" with a value of "page"
		var pages = $('div[data-role="page"]'),
			
			// the current page is always the last div in the Array, so we store it in a variable
			currentPage = pages[pages.length-1],
			
			// grab the url of the page the  was loaded from (e.g. what page have we just ajax'ed into view)
			attr = currentPage.getAttribute('data-url');
		
		// basic conditional checks for the url we're expecting
		if (attr.indexOf('home.html') !== -1) {
			// now we know what page we're on we can insert the required scripts.
			// In this case i'm inserting a 'script.js' file.
			// I do this by passing through the name of the file and the 'currentPage' variable
			insertScript('search', currentPage);
		}
		
		// rinse and repeat...
		if (attr.indexOf('profile.html') !== -1) {
			insertScript('profile', currentPage);
		}
		
	});

That’s all there is to it.

Let me know your thoughts or if you’ve found any better ways to work around this issue.