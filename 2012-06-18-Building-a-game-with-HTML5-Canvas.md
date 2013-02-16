#Building a game with HTML5 Canvas

##Introduction

I've been playing around with the [Canvas](https://developer.mozilla.org/en/HTML/Canvas) element for a little bit now, using it for more realistic features such as manipulating user uploaded images (using [XHR2](http://dvcs.w3.org/hg/xhr/raw-file/tip/Overview.html)). For example, within a custom Content Management System where the user controls the images that appear on their website we would normally let the server handle the manipulation of the uploaded image using an external library such as [ImageMagick](http://www.imagemagick.org/), but now we handle this all on the client-side using Canvas.

I've also played with the raw pixel data in images to apply filters and effect the colours of an image, which is interesting (although I have no actual use for that sort of manipulation at the present time).

So I thought I would dive a little bit further into the Canvas element and use it for building a game (as that's what most people are using it for). Nothing fancy, just the classic "sliding image puzzle" game we have all played at some point.  If you've never played this game: the idea is to take an image and to split it into pieces. You then jumble up the pieces and remove one piece so there is an empty space. You then have to re-assemble the original image by sliding the pieces around but only able to move one piece at a time via the empty space that is available to you.

The game works on both desktop and mobile devices (e.g. 'touch' based devices).

To see an example of the game then go to my [GitHub repository here](https://github.com/Integralist/HTML5-Image-Slider-Game/) and run the HTML file in a browser that supports the HTML5 Canvas element.

In this article we're going to discuss the process I went through to build this game in more detail. The first two sections (HTML, CSS) are brief because the main chunk of work is residing inside the JavaScript so for that section we're going to step through each section of the code and explain what's happening.

##Table of Contents

* HTML
* CSS
* JavaScript
	* requestAnimationFrame
	* Variables
	* Events alias'
	* Image loading
	* Clearing the Canvas
	* Load image onto Canvas
	* Initialise Game
	* User interaction
	* Automatic puzzle piece animation
	* Drag and Drop interaction
	* Determining the end of game

---

###HTML

The HTML is pretty straight forward, it consists of a `meta` tag which tells the device to set the width to be 760px wide (this is so the game fits the full width of the device) and also specifies that the user cannot resize the content - the reason being is we want to ensure the game is always fitting the screen. After this we have a link to our style sheet - note: there are some declarations in our CSS which are included specifically to help with 'touch' based devices (e.g. devices such as smart phones which don't utilise a 'mouse' to interact with the content) - along with a single `canvas` element (which we could have created via JavaScript but I decided I preferred to have the element coded into the HTML). We also have a checkbox on the page which lets the user make the game a little easier by allowing them to move certain 'illegal' puzzle pieces, and finally we have the `script` element which obviously is pointing to our JavaScript file which creates the game… 

    <!doctype html>
    <html lang="en" dir="ltr">
    	<head>
    		<meta charset="utf-8">
    		<meta name="viewport" content="width=760, user-scalable=0">
    		<title>HTML5 Slider Image Game</title>
    		<link rel="stylesheet" href="Assets/Styles/base.css">
    	</head>
    	<body>
    		<canvas id="game"></canvas>
    		<p class="allow">Make game easier by allowing 'drag and drop' of invalid pieces: <input type="checkbox" name="allow" id="allow"></p>
    		<script src="Assets/Scripts/game.js"></script>
    	</body>
    </html>

###CSS

The CSS is also pretty straight forward - with the exception of one part we'll come to in a moment - the main chunk of this style sheet is simply setting generic styles and normalising certain elements. 

You'll notice though a rule called `.drag-canvas` which will be applied to another `canvas` element which you haven't seen yet as it's not part of the HTML, it is instead created via JavaScript (I'll explain what this 2nd `canvas` is for later when we start discussing the JavaScript side of the project).

The `canvas` selector sets a rule which has some specific declarations (along with appropriate vendor prefixes) that are applied to any `canvas` element found in the page. The declaration we're applying is [`user-select`](https://developer.mozilla.org/en/CSS/-moz-user-select) which controls the selection a user makes on the content. You'll see we've set this to `none`. What this does is it prevents any iOS devices (and potentially other devices - hence the use of the vendor prefixes) from showing an additional option which lets the user carry out an action (e.g. 'copy'). The reason I want to prevent this is because in our game we also want users to be able to 'drag and drop' puzzle pieces (rather than just letting them 'click' or 'touch' to move a puzzle piece) and the way we do this (again, we'll go into this in more detail later) is by checking how long the user holds their 'touch' down on the puzzle piece. But if you have ever used an iOS device you'll know that if you hold your touch down for more than a second you'll see a context menu pop up asking you to either 'Select, Select All, Paste' or 'Copy' and it's these options we want to disable while the user is playing our game.

    body {
        font-family: Helvetica, Arial, sans-serif;
        font-size: 100%; /* this sets 1em to equal approximately 16px */
        margin: 0;
        padding: 0;
    }

    h1, h2, h3, h4, h5, h6, p {
        line-height: 1.3em;
        margin: 0 0 1em;
    }

    h1 {
        font-size: 2em;
        margin-bottom: 0.5em;
    }

    .drag-canvas {
        left: 0;
        position: absolute;
        top: 0;
    }

    canvas {
        -webkit-user-select: none; /* prevents the 'copy' option > http://www.bernskiold.com/2011/02/02/tips-and-tricks-for-ios-web-development/ */
           -moz-user-select: none;
                user-select: none;
    }

    .allow {
        margin-top: 1em;
        text-align: center;
        width: 760px;
    }

###JavaScript

Now the JavaScript is where it gets interesting…  

####requestAnimationFrame

The first thing you'll notice is a polyfill for [requestAnimationFrame](https://developer.mozilla.org/en/DOM/window.requestAnimationFrame). If you don't know what it is, `requestAnimationFrame` is a more efficient alternative for `setInterval `and `setTimeout`. It's actually based more on `setTimeout` than `setInterval`, and what I mean by this is your specified function to be executed must itself call `requestAnimationFrame` (unless you want the animation to stop).

The polyfill I'm using I've modified slightly so that it creates a property of the global object whose value is a boolean which indicates if it is supported by the user's browser or not. The reason I store this result is because later in the script when I specify a 'step amount' (e.g. how far I want the puzzle piece to move on each iteration) I found that I got better performance setting a higher step amount for browsers that support `requestAnimationFrame` so I want to change the step amount depending on if I'm using `setInterval` or not.

    // Polyfill for requestAnimationFrame which I've modified from Paul Irish's original
    // See: http://paulirish.com/2011/requestanimationframe-for-smart-animating/
    (function (global) {
    	var lastTime = 0,
    		vendors = ['ms', 'moz', 'webkit', 'o'];
    	
    	for (var x = 0; x < vendors.length && !global.requestAnimationFrame; ++x) {
    		global.requestAnimationFrame = global[vendors[x]+'RequestAnimationFrame'];
    		global.cancelAnimationFrame = global[vendors[x]+'CancelAnimationFrame'] || global[vendors[x]+'CancelRequestAnimationFrame'];
    	}
    	
    	global.nativeRAF = !!global.requestAnimationFrame; // store reference to whether it was natively supported or not (later we need to change increment value depending on if setInterval or requestAnimationFrame is used)

    	if (!global.requestAnimationFrame) {
    		global.requestAnimationFrame = function (callback, element) {
    			
    			var currTime = new Date().getTime(),
    				timeToCall = Math.max(0, 16 - (currTime - lastTime)),
    				id = global.setTimeout(function(){ 
    					callback(currTime + timeToCall);
    				}, timeToCall);
    			
    			lastTime = currTime + timeToCall;
    			
    			return id;
    			
    		};
    	}

    	if (!global.cancelAnimationFrame) {
    		global.cancelAnimationFrame = function (id) {
    			clearTimeout(id);
    		};
    	}
    }(this));

Following this is our main script which we've wrapped in an 'immediately invoked function expression' (or [IIFE](benalman.com/news/2010/11/immediately-invoked-function-expression/) for short). You'll see that we're passing in the value `this` which in this context is the global object (i.e. `Window`)… 

    (function (global) {
        // REST OF OUR CODE
    }(this));

####Variables

Now, if you look at my [JavaScript Style Guide](https://github.com/Integralist/Style-Guides/blob/master/JavaScript%20Style%20Guide.md#javascript-style-guide) you'll see that I like to set-up the structure of my code as follows… 

* Variables
* Supporting functions
* Code

…and this structure applies to all executing contexts (e.g. sub functions also have the same structure). So looking at our code you can see we've got a whole ton of variables specified which are used throughout the program. The reason for this is because of how the JavaScript interpreter handles variables when the program starts, which is to invisibly move variable declarations to the top of their containing scope. So to avoid confusion I try (wherever possible) to keep my variable declarations at the top… 

    // Following variables are related to the creation of the canvas' and specific configuration
    	var doc = global.document,
    		canvas = doc.getElementById("game"),
    		context = canvas.getContext("2d"),
    		dragCanvas = doc.createElement("canvas"),
    		dragCanvasContext = dragCanvas.getContext("2d"),
    		img = new Image(),
    		canvas_height,
    		canvas_width,
    		canvas_grid = 4, // e.g. 4 cols x 4 rows
    		piece_height,
    		piece_width,
    		opening_message = "Start Game",
    		text_dimensions = context.measureText(opening_message),
    	// Following variables are related to the puzzle pieces
    		puzzle_squares = [],
    		puzzle_randomised,
    		empty_space,
    		current_piece,
    		removed_piece,
    		random_number,
    	// Following variables are related to moving puzzle pieces around
    		drag = true,
    		eventObject,
    		eventsMap  = {
    			select: "click",
    			down: "mousedown",
    			up: "mouseup",
    			move: "mousemove"
    		},
    		touchSupported = false,
    		event_moving = false,
    		upTriggered = false,
    		wasJustDragging = false,
    		allow_input = doc.getElementById("allow");

…looking at these variables you can see that we're not only getting a reference to the `canvas` element in the page but we're also creating another `canvas` element. The purpose of having two `canvas`'es is that the one found inside the HTML will be used for holding the puzzle pieces. The second `canvas` (created via JavaScript) is used for handling event listeners and also handling the 'drag and drop' of individual puzzle pieces.

####Events alias'

You'll also see that we've created an object called `eventsMap` which we're using to map our own events (such as 'select', 'down', 'up', 'move') to the relevant mouse events available. This is because it makes it easier for us to swap to using touch events if they are supported by the users browser. So for example, just after setting up these variables we do a check for whether touch events are supported and if they are we swap the mouse specific values for touch specific values… 

    if ("ontouchstart" in global) {
        touchSupported = true;
        eventsMap  = {
            select: "touchstart",
            down: "touchstart",
            up: "touchend",
            move: "touchmove"
        };
    }

####Image loading
	
You'll also see that within the variable declarations we're creating an `Image` object and after the variables we now set a `src` value which points to the image we're going to use for our game. We then set an `onload` event listener to be triggered when the image has loaded. 

Once the image is loaded we know we can set-up the dimensions of the `canvas` and the puzzle pieces and also get the 'drag' `canvas` placed on top of the main `canvas`. You'll notice that we've set the 'drag' `canvas` to have `globalAlpha = .9;` which basically makes the top `canvas` slightly opaque/transparent (e.g. if a puzzle piece was drawn on the 'drag' `canvas` then you would be able to see through the puzzle piece to the bottom `canvas`).

We then finally call `loadImageOntoCanvas` to load the image on to the main `canvas`… 

    img.src = "Assets/Images/photo.jpg";	
    img.onload = function(){
        piece_height = ~~(this.height / canvas_grid);
    	piece_width = this.width / canvas_grid;
    	canvas_height = piece_height * canvas_grid;
        canvas_width = piece_width * canvas_grid;
        canvas.height = dragCanvas.height = canvas_height;
        canvas.width = dragCanvas.width = canvas_width;
        
        dragCanvas.className = "drag-canvas";
        dragCanvasContext.globalAlpha = .9;
        doc.body.appendChild(dragCanvas);
    	
        loadImageOntoCanvas();
    };

…one other small note is the use of the bitwise operator `~~` which you can see I've used inside the `onload` listener: `piece_height = ~~(this.height / canvas_grid);`. What this does is functionally equivalent to `Math.floor` and is a technique I discovered from [James Padolsey](http://james.padolsey.com/javascript/double-bitwise-not/).

####Clearing the Canvas

Next we see a function called `clearCanvas`… 

    function clearCanvas (c) {
       c.width = c.width;
    }

This does exactly what you think it does. But instead of using the API method `clearRect` to clear the `canvas` it uses a trick where by if you set the width and height of the `canvas` to the same dimensions as the `canvas` itself then the `canvas` will clear. **BUT BE WARNED:** this will also erase all state from the `canvas`! (for more information on 'state' [see here](http://html5.litten.com/understanding-save-and-restore-for-the-canvas-context/))

####Load image onto Canvas

Now we move onto the function `loadImageOntoCanvas`… 

    function loadImageOntoCanvas(){
    	context.drawImage(img, 0, 0, canvas_width, canvas_height);
    	context.save();
    	
    	context.fillStyle = "#FFF";
    	context.shadowColor = "#000";
    	context.shadowOffsetX = 2;
    	context.shadowOffsetY = 2;
    	context.shadowBlur = 2;
        context.font = "bold 25px Helvetica";
        context.fillText(opening_message, (canvas_width / 2) - text_dimensions.width, (canvas_height / 2));
        
        dragCanvas.addEventListener(eventsMap.select, init, false);
    }

…which first draws the image onto the `canvas`, then writes some text onto the `canvas` which says "Start Game". We position the text centrally to the `canvas` by using the API method `measureText` which gives us the dimensions of the text. Remember up in the variable declarations we had… 

```
text_dimensions = context.measureText(opening_message);
```

…well now we can use that to write the text centrally onto the `canvas`… 

```
context.fillText(opening_message, (canvas_width / 2) - text_dimensions.width, (canvas_height / 2));
```

The last part of that function is an event listener for the `click`/`touchstart` event (depending on the browser support) which will call the `init` function when the user clicks on the `canvas` (to start the game)… 

```
dragCanvas.addEventListener(eventsMap.select, init, false);
```

####Initialise Game

In short: the `init` function clears the `canvas` and splits up the image into individual pieces and then displays them in a jumbled order. From there it sets up further event listeners for 'down' and 'up' events (which map to `mousedown`/`mouseup` and `touchstart`/`touchend`).

Lets look at this function in more detail… 

    dragCanvas.removeEventListener(eventsMap.select, init, false);
    clearCanvas(canvas);

    // Remove shadow (otherwise all puzzle pieces would get shadow applied to them)
    context.shadowOffsetX = 0;
    context.shadowOffsetY = 0;

…so we first remove the event listener we previously set. We then clear the `canvas` (don't worry, the rest of the function executes in a fraction of a second so the user wont see the `canvas` go white, they'll just see the end result which is the jumbled up puzzle pieces).

We then set the `shadowOffsetX` and `shadowOffsetY` to zero. The reason for doing this is that when I originally wrote the text "Start Game" onto the `canvas` I had set the shadow properties to help make the text stand out more. But if I don't reset the values back to zero then all items will have a shadow (so when I was originally building this game I noticed that the puzzle pieces all had a shadow on them which made the game look broken!)

We'll ignore the variable declarations and the two functions at the beginning of `init` and move down to the executing code within this function.

So first thing we see is we call the `loop` function. Now this function is actually called twice within `init` and the reason for that is because the main chunk of the `loop` function is (as you would expect) a loop and that loop has to happen twice: once for splitting the image into pieces and the second time is for actually drawing those pieces (after they've been jumbled up) back onto the `canvas`. But some extra things need to happen when the loop runs a second time, so we pass in a parameter so the `loop` function knows that this time it will need to draw the puzzle pieces onto the `canvas`…

    // Build map of co-ordinates
    loop();

    // Randomise puzzle pices
    puzzle_randomised = shuffle(puzzle_squares);

    // Draw puzzle pieces
    loop(true);

…you can see we are using a `shuffle` function to mix up the puzzle pieces. This function I modified from the [Underscore](http://documentcloud.github.com/underscore/) JavaScript library.

From here we randomly select a puzzle piece to remove (remember we need to have one empty space for the other pieces to move into) and then we set the initial empty space values (which we use throughout the rest of the game as a way to tell which part of the `canvas` is empty and can have a puzzle piece moved into it)… 

    random_number = Math.round(Math.random()*puzzle_randomised.length-1);

    if (random_number < 0) {
        random_number = 0;
    }

    random_piece = puzzle_randomised[random_number];

    removed_piece = puzzle_randomised.splice(random_number, 1);

    context.clearRect(random_piece.drawnOnCanvasX, random_piece.drawnOnCanvasY, piece_width, piece_height);

    empty_space = {
        x: random_piece.drawnOnCanvasX,
        y: random_piece.drawnOnCanvasY
    };

And the last part of the `init` function is to set-up the event listeners for the 'down'/'up' events (which as we've mentioned already are just alias' to the relevant mouse and touch events)… 

    dragCanvas.addEventListener(eventsMap.down, checkDrag, false);
    dragCanvas.addEventListener(eventsMap.up, toggleDragCheck, false);

…these event listeners are going to help us detect when the user wants to move a puzzle piece.

####User interaction

So we can see that the 'down' event will call the `checkDrag` function, so lets start from there… 

    dragCanvas.removeEventListener(eventsMap.down, checkDrag, false);

    global.setTimeout(function(){
        if (drag) {
            startDrag(e);
        } else {
            movePiece(e);
        }
    }, 150);

You see we're first removing the event listener for the 'down' event. We do this so the user can't accidentally (or purposely) try to move another puzzle piece while we're still in the process of moving the previous one.

We then use a short timer which when executed checks to see if the variable `drag` is true or false. If `drag` is true then we know the user wants to actually 'drag and drop' the puzzle piece rather than have it move automatically for them. If `drag` is false then we just start animating the selected puzzle piece for them.

The reason we use a timer here is because we only have one 'down' event, but that one event needs to do two things. So the timer delays the JavaScript engine just long enough for us to tell whether the user has released their click/touch. Remember we also set an 'up' event which calls `toggleDragCheck` - well that function sets `drag` to false - so if the user just clicks/taps the puzzle piece then the 'up' event will trigger and we'll set `drag` to false and so the timer will then know the user wants to have the piece moved for them. If the user keeps their click/touch down then when the timer runs `drag` will still equal true and so we know they want to 'drag and drop' the selected piece.

Now as far as user interaction is concerned, there are a number of different scenarios that can play out during this game (e.g. user presses on illegal piece, user drops piece over non-empty space, the order of functions called changes when the user 'drag and drops' and so the value of the variable `drag` can be changed incorrectly) and so we have to add in extra checks inside the `toggleDragCheck` function which work around these different scenarios.

For example, I had a problem where if the user did a 'drag and drop' movement then the `toggleDragCheck` function would get called in a different order. So if the user then tried to perform another 'drag and drop' movement they couldn't. So I had to put in checks that determined if the user was just in a 'drag and drop' motion or not and reset specific variables to make sure the user could perform the actions they wanted… 

    function toggleDragCheck (e) {
    	upTriggered = true;
    	
    	// If the user releases while piece is moving but the piece hasn't yet been placed then stop the drag
    	// And move the piece back into the empty space it came from
    	if (upTriggered && event_moving) {
    		upTriggered = false;
    		event_moving = false;
    		stopDrag();
    		putPieceBack();
    	} else {
    		drag = false;
    	}
    }

UPDATE: I have since realised another way I could have implemented 'drag & drop' which is to check the 'move' event (remember this is an alias for `mousemove` and `touchmove`) while the 'down' event was triggered and to set a threshold of let's say 4px in any direction before triggering the drag and drop mechanism. I don't know how much this would have simplified things but maybe that could be an exercise for the reader to investigate. 

####Automatic puzzle piece animation

We have two routes to go down now: `startDrag` or `movePiece`.

We're going to start with `movePiece` as that's the simpler of the two routes at the moment.

    var i = puzzle_randomised.length,
        j = canvas_grid,
        selected_piece,
        potential_spaces,
        // Firefox only recognised properties pageX/Y
        eventX = e.offsetX || e.pageX, 
        eventY = e.offsetY || e.pageY,
        pieceMovedX,
        pieceMovedY,
        moveAmount = (nativeRAF) ? 20 : 10, // setInterval worked fine when moving by 10 but rAF could do with up'ing the number of pixels per movement
        interval,
        coord,
        direction, 
        storeSelectedX, 
        storeSelectedY,
        foundPieceForAnimation;

OK, so the first thing we find inside of the `movePiece` function are the variable declarations. We can see that the only variables that are actually defined are `i`, `eventX`, `eventY` and `moveAmount`. For `moveAmount` you can see what we were referring to earlier when we were talking about the `requestAnimationFrame` polyfill…

`moveAmount = (nativeRAF) ? 20 : 10`

…we're checking if `requestAnimationFrame` is natively supported (rather than just a `setInterval`) and then we change the amount the puzzle piece should move depending on that result (we find moving by 20 on every iteration is a lot smoother using `requestAnimationFrame`, whereas moving by 20 using `setInterval` is just too fast).

Our first requirement from here is to find out which puzzle piece was clicked on. When we find out what piece was selected we can then decide whether we want to continue within the function to actually animate the piece into the empty space or not (I say "*we can decide*" because at this stage we don't know if the selected puzzle piece is a valid piece that has been clicked on - e.g. there is no empty space immediately next to it)… 

    // Find the piece that was clicked on
    selected_piece = findSelectedPuzzlePiece(i, eventX, eventY);

    // We're resetting 'wasJustDragging' variable to false as we're now attempting to move the puzzle piece 'automatically'
    // So even if we don't actual move the puzzle piece, our program knows we haven't just tried to 'drag and drop'
    wasJustDragging = false;

    // If no piece found (or user clicked on 'empty space') then don't continue
    // But we need to reset some settings ready for next user interaction
    if (!selected_piece) {
        resetOptions();
        return;
    }

…so the above code is making sense so far (e.g. we need to get the selected puzzle piece and then check if it's valid). But lets take a closer look at the two functions mentioned in the above snippet: `findSelectedPuzzlePiece` and `resetOptions`.

    function findSelectedPuzzlePiece (i, eventX, eventY) {
        while (i--) {
            // Make sure we haven't selected the current empty space
            if (eventX >= empty_space.x && eventX <= (empty_space.x + piece_width) && eventY >= empty_space.y && eventY <= (empty_space.y + piece_height)) {
                return false;
            }
            
            if (eventX >= puzzle_randomised[i].drawnOnCanvasX && eventX <= (puzzle_randomised[i].drawnOnCanvasX + piece_width) && eventY >= puzzle_randomised[i].drawnOnCanvasY && eventY <= (puzzle_randomised[i].drawnOnCanvasY + piece_height)) {
                return puzzle_randomised[i];
            }
        }
    }

As you can see the `findSelectedPuzzlePiece` is just a simple loop through the Array (`puzzle_randomised`) which holds the jumbled up puzzle pieces `puzzle_randomised` and it checks the current X and Y co-ordinates to see if they match any of the items in the Array (while at the same is also checking if the X and Y co-ordinates have matched the currently empty space in the puzzle).

The `resetOptions` function is a little bit more complicated in that it needs to delay resetting some values. This is because of how the different functions are used to configure the 'drag and drop' settings. For example, if the user clicked too quickly after just 'dragging and dropping' their puzzle piece then they wouldn't be able to move another piece (which was fixed by setting a fraction of a second delay before re-applying the event listeners).

    function resetOptions(){
    	if (wasJustDragging) {
    	    global.setTimeout(function(){
    		    dragCanvas.addEventListener(eventsMap.down, checkDrag, false);
    		    dragCanvas.addEventListener(eventsMap.up, toggleDragCheck, false);
    		}, 500);
    	} else {
    		dragCanvas.addEventListener(eventsMap.down, checkDrag, false);
    	}
        
        // Make sure drag is reset to true so we can check whether the user wants to "drag & drop"
        drag = true;
    }

OK, now we've taken a look at those two functions, lets continue on with the next step of the `movePiece` function which is to create an object with the four potential empty spaces that could be around the selected puzzle piece… 

    potential_spaces = [
        {
            x: selected_piece.drawnOnCanvasX,
            y: selected_piece.drawnOnCanvasY - piece_height
        },
        {
            x: selected_piece.drawnOnCanvasX - piece_width,
            y: selected_piece.drawnOnCanvasY
        },
        {
            x: selected_piece.drawnOnCanvasX + piece_width,
            y: selected_piece.drawnOnCanvasY
        },
        {
            x: selected_piece.drawnOnCanvasX,
            y: selected_piece.drawnOnCanvasY + piece_height
        }
    ];

…now we move onto actually looping through the `potential_spaces` Array to see if we can find a match of the potential empty spaces and the *actual* empty space… 

    // Check if we can move the selected piece into the empty space (e.g. can only move selected piece up, down, left and right, not diagonally)
    while (j--) {
        if (potential_spaces[j].x === empty_space.x && potential_spaces[j].y === empty_space.y) {
            // NOW WE NEED TO LOOP THROUGH 'puzzle_randomised'          
            break;
        }
    }

…once we've found a match we can start looping through the Array that holds the jumbled puzzle pieces (i.e. `puzzle_randomised`) and see if we can find a match between the selected puzzle piece and the pieces in the Array… 

    while (i--) {
        if (puzzle_randomised[i].drawnOnCanvasX === selected_piece.drawnOnCanvasX && 
            puzzle_randomised[i].drawnOnCanvasY === selected_piece.drawnOnCanvasY) {
            
            // We'll keep track of how far the piece has moved
            pieceMovedX = selected_piece.drawnOnCanvasX;
            pieceMovedY = selected_piece.drawnOnCanvasY;
            
            // We'll also keep track of the original selected piece as we'll need these co-ordinates for resetting the empty space
            storeSelectedX = selected_piece.drawnOnCanvasX;
            storeSelectedY = selected_piece.drawnOnCanvasY;
            
            // Animate the piece into place
            foundPieceForAnimation = puzzle_randomised[i]; // had to store in var rather than pass as an argument as requestAnimationFrame doesn't have any way to pass an argument :-(
            raf();
                                    
            break;
        }
    }

At the end of that loop we put in a quick conditional check that calls the `resetOptions` function only when no matches from the previous loops were found… 

    // User must have clicked on an item that couldn't have been moved
    if (j < 0) {
        resetOptions();
    }

So the entire chunk of code (two loops and conditional statement) looks like this… 

    // Check if we can move the selected piece into the empty space (e.g. can only move selected piece up, down, left and right, not diagonally)
    while (j--) {
        if (potential_spaces[j].x === empty_space.x && potential_spaces[j].y === empty_space.y) {
            // We then loop through the shuffled puzzle order looking for the piece that was selected by the user
            while (i--) {
                if (puzzle_randomised[i].drawnOnCanvasX === selected_piece.drawnOnCanvasX && 
                    puzzle_randomised[i].drawnOnCanvasY === selected_piece.drawnOnCanvasY) {
                    
                    // We'll keep track of how far the piece has moved
                    pieceMovedX = selected_piece.drawnOnCanvasX;
                    pieceMovedY = selected_piece.drawnOnCanvasY;
                    
                    // We'll also keep track of the original selected piece as we'll need these co-ordinates for resetting the empty space
                    storeSelectedX = selected_piece.drawnOnCanvasX;
                    storeSelectedY = selected_piece.drawnOnCanvasY;
                    
                    // Animate the piece into place
                    foundPieceForAnimation = puzzle_randomised[i]; // had to store in var rather than pass as an argument as requestAnimationFrame doesn't have any way to pass an argument :-(
                    raf();
                                            
                    break;
                }
            }
            
            break;
        }
    }

    // User must have clicked on an item that couldn't have been moved
    if (j < 0) {
        resetOptions();
    }

Within the second loop (once we've made a match) we now need to begin the actual process of animating the selected puzzle piece into the empty space.

We do this by calling the `raf` function which itself sets-up the `requestAnimationFrame` and then calls the `animate` function… 

    function raf(){
    	interval = global.requestAnimationFrame(raf);
    	animate(foundPieceForAnimation);
    }

…when `animate` is called we pass through the relevant puzzle piece. The `animate` function first clears the space where the selected piece last was found and then checks to see if we need to update the 'x' or 'y' co-ordinates… 

    // Clear the space where the selected piece is currently
    context.clearRect(pieceMovedX, pieceMovedY, piece_width, piece_height);

    // We don't want to move the x/y co-ordinates if they're already the same
    if (pieceMovedX !== empty_space.x) {
        coord = "x";
        // Check which direction the piece needs to move in
        if (pieceMovedX > empty_space.x) {
            direction = 0;
            pieceMovedX -= moveAmount;
        } else {
            direction = 1;
            pieceMovedX += moveAmount;
        }

    } else if (pieceMovedY !== empty_space.y) {
        coord = "y";
        // Check which direction the piece needs to move in
        if (pieceMovedY > empty_space.y) {
            direction = 0;
            pieceMovedY -= moveAmount;
        } else {
            direction = 1;
            pieceMovedY += moveAmount;
        }
    }

…once the co-ordinates are updated we then re-draw the puzzle piece into the next position and keep going until we reach the end of the animation. 

We check for the end of the animation by looking at the current co-ordinates of the puzzle piece compared to the final co-ordinates and if there is a match we stop the animation by clearing the time out and drawing the image one last time into it's final position. 

We then update the co-ordinates for that now moved puzzle piece so it thinks it was last drawn in the same position as what once was an empty space. We then update the empty space co-ordinates so they point to where the selected puzzle piece just came from (confused yet?!)

Lastly, we call `resetOptions` and do one final check to see if all the pieces are now in the end position and thus the end of the game…

    if (direction && coord === "x" && pieceMovedX >= empty_space.x || 
        direction && coord === "y" && pieceMovedY >= empty_space.y || 
        !direction && coord === "x" && pieceMovedX <= empty_space.x || 
        !direction && coord === "y" && pieceMovedY <= empty_space.y) {

    	global.cancelAnimationFrame(interval);
    	
    	// Draw one last time directly into the empty space
        // Note: I was finding that because of the loop interation sometimes the y position would be -2 or 2+ but I decided that near enough the position drawing directly into the empty space the user wont even notice
        context.drawImage(img, piece_to_move.x, piece_to_move.y, piece_width, piece_height, empty_space.x, empty_space.y, piece_width, piece_height);

        // Also update the drawnOnCanvasX/Y properties so they reflect the last place on the canvas they were drawn
        piece_to_move.drawnOnCanvasX = empty_space.x;
        piece_to_move.drawnOnCanvasY = empty_space.y;

        // Reset the empty space co-ordinates to be where the image we've just moved was.
        empty_space.x = storeSelectedX;
        empty_space.y = storeSelectedY;

        resetOptions();

        if (checkIfGameFinished()) {
            drawBackMissingPiece();
        }
    } else {
    	// Then redraw it into the empty space
        context.drawImage(img, piece_to_move.x, piece_to_move.y, piece_width, piece_height, pieceMovedX, pieceMovedY, piece_width, piece_height);
    }

####Drag and Drop interaction

So now we've been through the automatic moving of puzzle pieces lets start looking at how the 'drag and drop' route works.

So if you remember we had a conditional that was checking if the variable `drag` was true or false. But it would check the value after a few milliseconds (which was long enough for our program to change the value of `drag` to true if the user had kept their click/touch down - signifying they intended to drag the puzzle piece).

If the user intended to drag the piece we would call the appropriately named function `startDrag`… 

    var selected_piece,
        i = puzzle_randomised.length,
        j = canvas_grid,
        potential_spaces,
        eventX = e.offsetX || e.pageX, 
        eventY = e.offsetY || e.pageY;

    function setUp(){
    	context.clearRect(selected_piece.drawnOnCanvasX, selected_piece.drawnOnCanvasY, piece_width, piece_height);
    	
    	dragCanvasContext.drawImage(img, selected_piece.x, selected_piece.y, piece_width, piece_height, global.user_positionX, global.user_positionY, piece_width, piece_height);
    	
    	dragCanvas.addEventListener(eventsMap.move, eventObject, false);
    	
    	event_moving = true;
    }

    global.user_positionX = eventX - (piece_width / 2);
    global.user_positionY = eventY - (piece_height / 2);

    eventObject = {
        handleEvent: function (e) {
            dragPiece(e, selected_piece);
        }
    };

    current_piece = selected_piece = findSelectedPuzzlePiece(i, eventX, eventY);

    if (!allow_input.checked) {
    	
    	potential_spaces = [
    	    {
    	        x: selected_piece.drawnOnCanvasX,
    	        y: selected_piece.drawnOnCanvasY - piece_height
    	    },
    	    {
    	        x: selected_piece.drawnOnCanvasX - piece_width,
    	        y: selected_piece.drawnOnCanvasY
    	    },
    	    {
    	        x: selected_piece.drawnOnCanvasX + piece_width,
    	        y: selected_piece.drawnOnCanvasY
    	    },
    	    {
    	        x: selected_piece.drawnOnCanvasX,
    	        y: selected_piece.drawnOnCanvasY + piece_height
    	    }
    	];
    	
    	outer_loop:
    	while (j--) {
    	    if (potential_spaces[j].x === empty_space.x && potential_spaces[j].y === empty_space.y) {
    	        while (i--) {
    	        	if (puzzle_randomised[i].drawnOnCanvasX === selected_piece.drawnOnCanvasX && 
    	        		puzzle_randomised[i].drawnOnCanvasY === selected_piece.drawnOnCanvasY) {
    	         		
    	         		selected_piece = puzzle_randomised[i];
    	         		setUp();
    	         		break outer_loop;
    	         		
    	        	}
    	        }
    	    }
    	}
    	
    	if (j < 0) {
    		event_moving = true;
    	    resetOptions();
    	}
    	
    } else {	        
    	setUp();	        
    }

…OK so there is a fair bit going on here so lets analyse what's happening. 

First we see the varible declarations, we expect this now and know that they relate to things used throughout this function.

Next we have a function called `setUp` which we'll ignore for now as this is what actually starts the 'drag and drop'. 

The rest of this function is actually a repeat of code from the `movePiece` function! So although it looks a lot you've already seen most of it any way. But before we discuss this further lets quickly look at the code after the `setUp` function… 

    global.user_positionX = eventX - (piece_width / 2);
    global.user_positionY = eventY - (piece_height / 2);

    eventObject = {
        handleEvent: function (e) {
            dragPiece(e, selected_piece);
        }
    };

    current_piece = selected_piece = findSelectedPuzzlePiece(i, eventX, eventY);

…so here we're setting some global properties (they need to be global because we want to be able to change them from outside of this function) to store the users selected co-ordinates. You'll notice that I've named them quite generically. The reason I did that was because this game works for mouse and touch based devices I didn't want to call them `click_positionX` because then on a touch device that wouldn't have strictly been true (anal I know).

The `eventObject` is the listener for the 'move' event (which as you already know is an alias for either `mousemove` or `touchmove` depending on device support). It's called by the listener within the `setUp` function. I'll come back to this later when I start discussing the `setUp` function.

And finally we store a reference to the currently selected puzzle piece.

OK, now we come to the conditional statement… 

    if (!allow_input.checked) {
    	
    	// Move piece into available empty space.
    	// There are 4 potential spaces around the selected piece which it can move in (diagonal doesn't count - as we're not worrying about the 'drag and drop' yet)
    	// So loop through each of them checking to see if any of their co-ordinates match the empty space
    	// If they do match then move the selected piece into that space and set the selected piece to be the new empty space
    	potential_spaces = [
    	    {
    	        x: selected_piece.drawnOnCanvasX,
    	        y: selected_piece.drawnOnCanvasY - piece_height
    	    },
    	    {
    	        x: selected_piece.drawnOnCanvasX - piece_width,
    	        y: selected_piece.drawnOnCanvasY
    	    },
    	    {
    	        x: selected_piece.drawnOnCanvasX + piece_width,
    	        y: selected_piece.drawnOnCanvasY
    	    },
    	    {
    	        x: selected_piece.drawnOnCanvasX,
    	        y: selected_piece.drawnOnCanvasY + piece_height
    	    }
    	];
    	
    	// Check if we can move the selected piece into the empty space (e.g. can only move selected piece up, down, left and right, not diagonally)
    	// We use a labelled statement to break out of the outer loop once a match is found within the inner loop
    	outer_loop:
    	while (j--) {
    	    if (potential_spaces[j].x === empty_space.x && potential_spaces[j].y === empty_space.y) {
    	        // We then loop through the shuffled puzzle order looking for the piece that was selected by the user
    	        while (i--) {
    	        	if (puzzle_randomised[i].drawnOnCanvasX === selected_piece.drawnOnCanvasX && 
    	        		puzzle_randomised[i].drawnOnCanvasY === selected_piece.drawnOnCanvasY) {
    	         		
    	         		selected_piece = puzzle_randomised[i];
    	         		setUp();
    	         		break outer_loop;
    	         		
    	        	}
    	        }
    	    }
    	}
    	
    	// User must have clicked on an item that couldn't have been moved
    	if (j < 0) {
    		event_moving = true; // this is so when the mouseup/touchend event is triggered we can catch this error out inside toggleDragCheck() which otherwise would reset drag=false and cause problems with the user's next interaction
    	    resetOptions();
    	}
    	
    } else {	        
    	setUp();	        
    }

…the purpose of this is to see if we're allowed to move 'illegal' puzzle pieces. If we aren't allowed to move them then we have a whole load of code which is repeated from the `movePiece` function and as you now already know it just works out if the puzzle piece selected is illegal or not. Now the reason I didn't just abstract this code into a separate function is because it has some slight tweaks to it (but mainly the code is the same). The tweaks are things like providing a 'labelled statement' for the outer while loop `outer_loop: while (j--)` (which makes it easer for us to break out of both loops, otherwise we'd have to set a variable in the inner loop for the outer loop to check against to see if it needed to break).

The `else` statement just calls the `setUp` function as it doesn't have to worry about checking if the puzzle piece is legal or not, it knows whatever was selected can be moved.

Now we get to the `setUp` function itself… 

    function setUp(){
    	context.clearRect(selected_piece.drawnOnCanvasX, selected_piece.drawnOnCanvasY, piece_width, piece_height);
    	
    	dragCanvasContext.drawImage(img, selected_piece.x, selected_piece.y, piece_width, piece_height, global.user_positionX, global.user_positionY, piece_width, piece_height);
    	
    	dragCanvas.addEventListener(eventsMap.move, eventObject, false);
    	
    	event_moving = true;
    }

…so the first line removes the puzzle piece from the canvas now we know which piece it is.

Next we draw the puzzle piece onto the top canvas which handles the user interaction.

Then we set-up the event listener so that we call the `dragPiece` function (which is specified within the `eventObject`) every time the user moves either their mouse cursor or their finger.

Lastly we set `event_moving` to true which is useful for doing some checks later on to make sure the user's next interaction isn't broken.

But lets just quickly review the `eventObject`. Most people don't realise that with `addEventListener` you don't have to specify a function to be the listener and that you can specify an object, but that object must have a `handler` property with a function assigned to it which acts as the listener. The reason I've used an object rather than a straight function is because I wanted to pass some additional parameters but couldn't of done that otherwise without using an anonymous function and if you don't know: it's best to avoid using anonymous functions for an event listener because if you do then you wont be able to remove the event listener (without maybe building your own abstraction of the events API to workaround the issue - I wont go into the details of why you can't remove an anonymous function referenced event listener as that's outside the scope of this post, but suffice to say it's similar to checking if `{} == {}`).

We've now reached the `dragPiece` function… 

    var eventX = e.offsetX || e.pageX, 
        eventY = e.offsetY || e.pageY,
        storeSelectedX = selected_piece.drawnOnCanvasX,
        storeSelectedY = selected_piece.drawnOnCanvasY,
        halfWidth = piece_width / 2,
        halfHeight = piece_height / 2;
        
    dragCanvasContext.clearRect(global.user_positionX, global.user_positionY, piece_width, piece_height);

    global.user_positionX = eventX - halfWidth;
    global.user_positionY = eventY - halfHeight;

    if (global.user_positionX <= empty_space.x + (piece_width - 20) && 
        global.user_positionY <= empty_space.y + (piece_height - 20) && 
        global.user_positionY + piece_height >= empty_space.y + 20 && 
        global.user_positionX + piece_width >= empty_space.x + 20) {
        highlightEmptySpace();
    } else {
        context.clearRect(empty_space.x, empty_space.y, piece_width, piece_height);
    }

    // Check if mouse is over the empty space or not
    if ((eventX >= empty_space.x && eventX < (empty_space.x + piece_width)) && (eventY >= empty_space.y && eventY < (empty_space.y + piece_height))) {
        dragCanvas.removeEventListener(eventsMap.move, eventObject, false);
        event_moving = false;
        context.drawImage(img, selected_piece.x, selected_piece.y, piece_width, piece_height, empty_space.x, empty_space.y, piece_width, piece_height);
        selected_piece.drawnOnCanvasX = empty_space.x;
        selected_piece.drawnOnCanvasY = empty_space.y;
        empty_space.x = storeSelectedX;
        empty_space.y = storeSelectedY;
        stopDrag();
    } else {	
        dragCanvasContext.drawImage(img, selected_piece.x, selected_piece.y, piece_width, piece_height, global.user_positionX, global.user_positionY, piece_width, piece_height);
    }

…so again lets look through what's happening.

The `halfWidth` and `halfHeight` variables are there because when we draw the puzzle piece onto the top canvas we want the puzzle piece to be centered to the mouse cursor/user's touch.

The line:

`dragCanvasContext.clearRect(global.user_positionX, global.user_positionY, piece_width, piece_height);`

…clears where the puzzle piece was last drawn. We then update the co-ordinates for mouse/touch position…

    global.user_positionX = eventX - halfWidth;
    global.user_positionY = eventY - halfHeight;

…the next step is to see if the user has moved the puzzle piece 'near' the empty space. The way we do this is we highlight the empty space so it has a red background whenever the user moves 20px within it. This is so they are aware that at any point soon the puzzle piece will be dropped into this empty space… 

    if (global.user_positionX <= empty_space.x + (piece_width - 20) && 
        global.user_positionY <= empty_space.y + (piece_height - 20) && 
        global.user_positionY + piece_height >= empty_space.y + 20 && 
        global.user_positionX + piece_width >= empty_space.x + 20) {
        highlightEmptySpace();
    } else {
        context.clearRect(empty_space.x, empty_space.y, piece_width, piece_height);
    }

…the `else` statement is there for when the user moves the puzzle piece to a position that is again outside of the puzzle piece (e.g. we remove the red background).

Now we just have one more conditional statement before we reach the end of the function… 

    if ((eventX >= empty_space.x && eventX < (empty_space.x + piece_width)) && (eventY >= empty_space.y && eventY < (empty_space.y + piece_height))) {
        dragCanvas.removeEventListener(eventsMap.move, eventObject, false);
        event_moving = false;
        context.drawImage(img, selected_piece.x, selected_piece.y, piece_width, piece_height, empty_space.x, empty_space.y, piece_width, piece_height);
        selected_piece.drawnOnCanvasX = empty_space.x;
        selected_piece.drawnOnCanvasY = empty_space.y;
        empty_space.x = storeSelectedX;
        empty_space.y = storeSelectedY;
        stopDrag();
    } else {	
        dragCanvasContext.drawImage(img, selected_piece.x, selected_piece.y, piece_width, piece_height, global.user_positionX, global.user_positionY, piece_width, piece_height);
    }

…here we check to see if the mouse/touch is over the empty space and if it is we then remove the `move` event (as there is no point calling it again now we've placed the puzzle piece in the empty space). After that we update some of the properties such as `event_moving`, `empty_space` object and the `drawnOnCanvas` properties and then we call the `stopDrag` function which we'll get to in a few moments.

If the user isn't over the empty space then the `else` statement kicks in and we simply draw the puzzle piece onto the canvas in the current mouse/touch position.

So now lets look at the contents of the `stopDrag` function… 

    dragCanvas.removeEventListener(eventsMap.move, eventObject, false);
    dragCanvas.removeEventListener(eventsMap.up, toggleDragCheck, false);
    wasJustDragging = true;
    resetOptions();
    if (checkIfGameFinished()) {
        drawBackMissingPiece();
    }

…the first thing we do is remove the 'move' event - *now to be honest, as I'm writing this post a few weeks after completing the game, I'm not sure why I'm removing the event again in that section as it should have already been removed? I'll leave this in for now and re-evaluate the code at a later date to see if it is indeed as redundant as it appears to be now* - then we update `wasJustDragging` so we know that we just completed a drag and drop motion. Then we call the `resetOptions` function (which we went over earlier) and finally we call the function `checkIfGameFinished` which nicely leads us into our final section… 

####Determining the end of game

The `checkIfGameFinished` function simply loops through all puzzle pieces to see if they match the correct order… 

    function checkIfGameFinished(){
    	var copied_puzzle_randomised = puzzle_randomised.slice(0);
    	    copied_puzzle_randomised.splice(random_number, 0, removed_piece[0]);
    	    complete = false;
    	
    	complete = copied_puzzle_randomised.every(function (item, index, array) {
    		if (item.drawnOnCanvasX === item.x && item.drawnOnCanvasY === item.y) {
    		    return true;
    		} 
    		// The final piece is actually the missing piece so we check the x/y against the empty_space x/y
    		else if (item.x === empty_space.x && item.y === empty_space.y) {
    			return true;
    		} else {
    			return false;
    		}
    	});
    	
    	return complete;
    }

…some other things to note about the `checkIfGameFinished` function is that we copy the `puzzle_randomised` Array and add back in the missing piece before we then check all the co-ordinates are correct and match (you'll see we're setting the variable `complete` to be the return value of the Array method `every` and then the function returns `complete` which will be either true or false (see: [https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Array/every](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Array/every) for details).

Quickly now we'll jump back to the `stopDrag` function where we'll see that if `checkIfGameFinished` returns true then we'll call `drawBackMissingPiece` which (as you would expect by the name of the function) draws the missing puzzle piece back onto the canvas as we know the game is complete and then displays an `alert` which tells the user the game is complete (this part you can change to suit your needs).

And now! finally! we have reached the end of this post :-)

Hopefully this has made *some* sense? I would say "go through the code and see how everything works" - and you can do that obviously - but with things of this nature they can get quite complex and there are problems the developer goes through and works around which might not be clear in the code itself (I've tried to document the reasoning behind most of the code so hopefully that will help).

But any questions then just let me know.