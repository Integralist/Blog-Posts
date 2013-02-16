#Node.js as a tool

NodeJs is best known for running server-side JavaScript, but it can also be used for other processes that run on your local machine: See: [Node.js as a build script](http://blog.millermedeiros.com/node-js-as-a-build-script/) (by [@millermedeiros](https://github.com/millermedeiros/)) for lots of examples of this.

With the help of [Homebrew](http://mxcl.github.com/homebrew/) (yes I know I'm only catering for Mac users here - what a scum bag I am - but the principles are the same I'm just using `Homebrew` to get these packages installed) the items we'll cover are:

* CSSLint
* JsHint
* RequireJS/r.js
* gh-markdown-cli


Prerequisite
---

I have to assume you've already got Homebrewm, Node and NPM installed? If not then open up your terminal/command line tool of choice and follow these steps:

1. Install Homebrew by executing this command:  
	`/usr/bin/ruby -e "$(curl -fsSL https://raw.github.com/gist/323731)"`
	
2. Install Node by executing this command:  
	`brew install node`
	
3. Install NPM by executing this command:  
	`curl http://npmjs.org/install.sh | sh`

After that you should be able to execute `brew --version`, `node --version` and `npm --version` and get version numbers back to show that they've installed.

CSSLint
---

"*Automated linting of Cascading Stylesheets*"

[https://github.com/stubbornella/csslint](https://github.com/stubbornella/csslint)

**Installation**:  
`npm install -g csslint`

**Example usage:**

    csslint [options] [file|dir]*
    csslint file1.css file2.css  
    csslint ./  
    csslint --errors=box-model,ids test.css // => decide what should be errors  
    csslint --warnings=box-model,ids test.css // => decide what should be warnings

**Configuration:**  
You can view the options by running `csslint --help`  
All 'rules' can be viewed using: `csslint --list-rules`  
The rules have #ID's that you can specify as errors|warnings

**Generic usage:**  
`csslint --errors=import,compatible-vendor-prefixes,display-property-grouping,overqualified-elements,fallback-colors,duplicate-properties,empty-rules,gradients,universal-selector,vendor-prefix,zero-units --warnings=important,known-properties,font-sizes,outline-none,shorthand,unqualified-attributes`

**Prettier Syntax:**  
It's recommended to create a shell script to wrap the CSS Lint functionality so you can use the same syntax as the Node.js CLI. 

For Linux/Mac, create a file named `csslint` and add the following to the file:  

    #!/bin/bash
    java -jar js.jar csslint-rhino.js $@

After creating the file, you need to ensure it can be executed, so go to the command line and type:  
`chmod +x csslint`


JsHint
---

"*CLI and NPM package for JSHint*"

[https://github.com/jshint/node-jshint](https://github.com/jshint/node-jshint)

**Installation:**  
npm install jshint

**Example usage:**  

    jshint path path2 [options] // => run against specific scripts
    jshint *.js // => run against all scripts
    jshint main.js --show-non-errors // => show non-errors (e.g. Implied globals etc)
    jshint main.js --config ./Lint/config.json // => use specific configuration options
    jshint main.js --show-non-errors --config ./Lint/config.json // => example of showing non-errors against specific configuration settings

If you get no errors then that means the script ran fine

**Generic usage:**  
`jshint **/*.js --config ./Lint/config.json`

**Configuration:**  
Your `config.json` file could look like the following…

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


RequireJS/r.js
---

"*Runs RequireJS in Node and used to run the RequireJS optimizer*"

[https://github.com/jrburke/r.js](https://github.com/jrburke/r.js)

**Generic usage:**  
`node r.js -o app.build.js`

**Configuration:**  
You create a 'build' script file (see below for an example) and then you use NodeJs to execute the `r.js` optimization script and pass through your build script as an argument

    /*
     * http://requirejs.org/docs/optimization.html
     *
     * See: https://github.com/jrburke/r.js/blob/master/build/example.build.js for an example build script
     *
     * If you specify just the name (with no includes/excludes) then all modules are combined into the "main" file.
     * You can include/exclude specific modules though if needed (this helps with 'lazy loading' scripts)
     *
     * You can also set optimize: "none" (or more specific uglifyjs settings) if you need to.
     *
     * Node: if you set relative paths then do them relative to the baseUrl
     */
    ({	
        appDir: '../../../',
        baseUrl: 'Assets/Scripts',
        dir: '../../../project-build',
        /*
         * The below 'paths' object is useful for when using plugins/named module paths.
         * If you use plugins or named modules in your code then don't forget to specify the same paths again in your build script.
         * Otherwise your build script wont be able to find your plugins/named modules and will generate an error when building.
         */
        paths: {
            async: 'Plugins/async',
            jquery: 'Utils/jquery',
            tpl: 'Plugins/tpl'
        },
        optimize: 'none',
        modules: [
            {
                name: 'main'
                /*
                include: ['module'],
                exclude: ['module']
                */
            }
        ]
    })


gh-markdown-cli
---

"*Node.js command-line tool to batch convert Markdown files into HTML*"

[https://github.com/millermedeiros/gh-markdown-cli](https://github.com/millermedeiros/gh-markdown-cli)

**Installation:**  
`sudo npm install gh-markdown-cli -g`

**Example usage (cd into directory where your md files are):**  
`mdown -o "./" --include "*.md" --header "header.html" --footer "footer.html"`

Once installed, use the terminal to locate your .md files and then run the above command (you don't have to use the --header and --footer flags as these are optional)