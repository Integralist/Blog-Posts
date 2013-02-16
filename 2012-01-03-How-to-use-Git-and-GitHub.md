#How to use Git and GitHub

If you’re having trouble understanding how to get up and running with GitHub, or you just wanted to find a free version-control system and heard about this thing called ‘Git’ then hopefully the following information should help…

When I first heard about GitHub I was unsure as to its purpose and why it was such a hit with other developers. I had never heard of ‘Git’ before and whenever people spoke of GitHub they would mention Git at the same time, and so because they both had ‘Git’ in their name I just assumed they were one and the same thing - which as it turns out was quite wrong indeed.

To clarify, ‘Git’ is a program. Once installed on your computer it can be accessed via the Command Line. You could very well have yourself a nice little ‘version control’ set-up on your computer using just Git and without ever needing to look at, or even having heard of, ‘GitHub’.

GitHub on the other hand is a website that hosts Git ‘files’ (known as ‘repositories’) and so it makes it a lot easier for open-source projects to have other developers help extend the project by allowing these developers to grab(`fork`) the project code (even though they didn’t create the project on GitHub) and to duplicate the project set-up and to modify it however the developer wishes and then allow the developer to submit changes, bug fixes, new features etc back to the original developers to integrate if they were so inclined.

It’s an incredibly powerful tool.

So with this in mind it’s best to learn about Git first, so then using GitHub will suddenly make a whole lot more sense! And this is why I think I struggled with GitHub initially: because I had no idea about what Git was and how it worked.

To help you read through this post, I’ve broken the page down into sections:

* Using the GitHub interface
* Installing Git
* Setting up a new Git repository
* Adding files for Git to track
* Commiting files to Git
* Setting up a GitHub account
* Generating an SSH Public Key
* Creating a new GitHub repository
* Pushing your project up to GitHub’s repository
* Removing/Editing files
* Git Tips
* Tell Git to ignore certain files and formats
* Best practices for Commit Messages

But before we get started…

Using the GitHub interface
--------------------------

Now I know I said we would start with learning Git first, THEN GitHub. But it is worth noting at this stage (mainly by [@paul_irish](http://twitter.com/paul_irish)’s request) that you can in fact update files directly via the GitHub website!

So if you want to avoid the command line altogether but you still want to help work with open-source projects on GitHub and in the simplest/easiest way possible, then read this section only and stop there (as the rest of the sections of this post will likely be of no interest to you).

If on the other hand using the GitHub interface doesn’t interest you at all and you really want to learn about what Git is and how to use it (and thus be able to use GitHub more effectively) then just skip this section and move onto “Installing Git”. So first thing you guys want to do is to sign-up for a GitHub account here:

[https://github.com/signup/free](https://github.com/signup/free) (to get to this page you need to go through the ‘Pricing and Signup’ link at the top of the GitHub home page, and from there you’ll be able to find the ‘free’ account set-up).

Once you’ve got your free account created you can start using GitHub for the more ‘basic’ Git features, such as ‘forking’ (i.e. taking a copy of…) an open-source project and making changes to it. We’ll create a new `fork` of the ‘jQuery JavaScript Library’ and start making some changes to the code base, and then submitting ‘commits’ to our own copy of the jQuery library project (i.e. saving our project’s current state). Then when happy with our changes we’ll submit a `pull` request to the jQuery owners! (and basically, by ‘pull’ we’re really saying “please jQuery, accept the changes we’ve made to your code base - we’d love for you to integrate them into the jQuery source”). So let’s take a look at the jQuery JavaScript Library page here:

[https://github.com/jquery/jquery](https://github.com/jquery/jquery). On this page you should see a ‘fork’ link near the top right of the page. Click on it. You’ll notice that GitHub will start making a copy of the jQuery JavaScript Library project and will generate a new project within your account under the same name. 

Now, you’ll see a list of all the files within this project and you can click on any folder/file to see its content. So let’s take a look at the README.md file by clicking on it’s name. You’ll see that GitHub does a nice little AJAX sliding animation and then shows you the README.md file… wonderful.

The next step is to click the ‘Edit this file’ link, which you’ll see on the right-hand side of the page. The file you were looking at has now moved into ‘edit’ mode and so you can start typing out some changes to the file (obviously if you’re a developer then you can open up the core JavaScript files and start making tweaks there in the same way).

After you’ve made the changes you wanted to the file, simply move to the input field below the file, just where it says “Commit message” and enter a description of the change you made (note: if the changes you are making are for your own personal use then just write whatever you like, but if you are actually planning on submitting your changes to jQuery to integrate into the source then you’ll need to find out what jQuery requires you to include within the ‘commit’ message - for example the jQuery UI project has written up some submission guidelines here: [http://wiki.jqueryui.com/w/page/25941597/Commit-Message-Style-Guide](http://wiki.jqueryui.com/w/page/25941597/Commit-Message-Style-Guide) and so I assume there are similar guidelines for the main jQuery project).

Once your commit message has been entered then you can click the “Commit Changes” button at the bottom of the page. Now when the page reloads you’ll see the latest note/commit for this file is actually yours and shows your commit message. But this is still only changes to *your* copy of the jQuery library.

You need to now tell jQuery about your very important change and get them to integrate it into their source version. To do this you now need to click the “Pull Request” link at the top of the page (near where you clicked on the “Fork” link from earlier. Clicking the “Pull Request” link will redirect you to a page where you must enter a message to go along with your commit (this will be reviewed by core committers!). Final step! Click “Send pull request” at the bottom of that page (just under where you entered your message).

From here you can sit back and relax, I think? Again I’ve read somewhere previously (for jQuery UI) that you need to add a link to your commit to the ticket in their tracker system (jQuery may very well have something similar I’m not sure though). But enough of this GUI nonsense, lets start using Git how it was meant to be used… on the command line!

Installing Git
--------------

OK, first thing first, lets get Git installed (by the way I work on a Mac so hopefully this guide is still somewhat useful to PC users). I personally found the easiest way to install git is via a pre-compiled installer: [http://code.google.com/p/git-osx-installer/](http://code.google.com/p/git-osx-installer/).

There are installers for PC as well as Mac and Linux so take your pick and you can install from the original Git source code but that’s a bit more ‘involved’. The official GitHub website mentions many ways to install Git, but I found the pre-compiled installer the quickest/easiest way - I pretty much ran the installer and it was done in about two seconds flat.

When Git is installed the first thing we’ll probably want to do is to tell it what our name and email is (this way, when working with a system - such as GitHub - and having multiple users working on your code base you can easily see which users made changes to the code - and tell them off if they added/removed code that broke your application).

To inform Git what your username and email address is, open the Terminal application on your Mac and enter:

    git config --global user.name "Joe Bloggs"
    git config --global user.email "joe@bloggs.com"

You can check these settings at any time within the Terminal by typing:

    git config user.name
    git config user.email

Next you want to make sure that Git ignores any ‘white space’ changes. Now this needs a little bit extra explanation: Git tracks file *content* and NOT individual files. So if you add an empty new line to a file (and Git is tracking that file) then Git will inform you that the file has been modified but you don’t really want a single empty line to be flagged up to you (not in web development really as white space is not that important). To have Git ignore whitespace changes you can enter the following into the Terminal:

`git config --global apply.whitespace nowarn`

Now that we’ve got Git set-up we need to look at how we can tell it to ‘track’ certain files & folders in your project.

Setting up a new Git repository
-------------------------------

Now, using Git does require a tiny bit of command line knowledge (such as navigating your computer via command line, making new directories and files, removing files etc). I’ll try and cover as much of this as I think is needed to use Git (you may need more commands than I cover but then I’ll leave that up to you to look into further). OK, so with the Terminal still open, navigate to your project folder (we do this by using the `cd` command - which stands for ‘change directory’)…

`cd ~/Dropbox/Project/`

…as you can see I have a folder called ‘Project’ within a private [Dropbox](http://www.dropbox.com/) folder. The tilda character `~` simply tells Terminal to go back to your home directory and start looking from there for the folder you need. You can also navigate your Mac using `../` to go up and down folders. If you want to check the contents of a particular folder then simply type `ls` which tells Terminal to ‘list’ the content of the current folder.

Once you’re inside the project folder you’ll need to create an empty Git ‘repository’, which means Git will create a hidden folder called `.git` where it will store all files and references it needs to be an effective version-control system for this project folder. To create a new Git repository type the following (make sure you’re definitely in your main project folder):

`git init`

If you’re Mac is set-up to not show ‘hidden’ folders then it wont look like much has happened (although you will now see a message in the Terminal telling you a new empty repository was created). If you really want to see hidden files and folders on your Mac then type the following into the Terminal…

    defaults write com.apple.Finder AppleShowAllFiles YES
    killall Finder

…the first line tells Finder to allow showing of hidden files, and the second line restarts Finder so you can see the changes take effect. Once you have a new Git repository set-up you need to tell Git what files/folders to start ‘tracking’.

Adding files for Git to track
-----------------------------

If you don’t already have any files in your project folder then we can at least add one file (via the Terminal as well) which will become useful later when you start to use GitHub as a code sharing platform. To create a file via the Terminal we must use the `touch` command like so…

`touch README.md`

…the above line creates a ‘README.md’ file (which is used by GitHub to give a project you create a visible description). Now as it happens I already had a few files in my project folder so along with the README.md file I just created I’m going to start adding these files to Git’s “Staging Area”.

The Staging Area is a place to add your files/folders before ‘commiting’ the changes you’ve made to these files and then (if you need to…) pushing the changed files onto a remote server (such as we’ll be doing later when we push our files up to our GitHub repository). To add a file/folder to the Staging Area (and thus have Git start ‘tracking’ the files and their changes) enter…

`git add README.md`

…this tells Git to start tracking the README.md file, but we could have specified multiple files at once using a space to separate the file names such as…

`git add file1 file2 file3`

…we could also tell Git to track ALL files/folders currently found within this folder…

`git add *`

…and lastly if you’re coming back to an existing project you can tell Git to track any new ‘un-tracked’ files using the line…

`git add .`

…the period character (.) simply means ‘any un-tracked files’ and saves you having to remember which new files are or aren’t being tracked already. The `add` command makes it easy to add as many files as you like to Git’s “Staging Area”.

You can add some files, go away and make a cup of tea, work on something else and then come back and add some more files to the Staging Area. But for any of these files to be ‘committed’ and thus have a ‘snap shot’ of the project (so we can - if we need to - revert back to an older version) we need to use the `commit` command to tell Git we want it to snap shot our project in its current state…

Commiting files to Git
----------------------

`git commit -m 'Add a README file to help explain the project'`

…the above command uses the `-m` ‘flag’ which means you’re about to add a comment to go along with this ‘commit’. You don’t have to use the `-m` flag, but if you don’t then Terminal will try and open the default text editor so you can enter your commit description and I find it quicker and easier just to use the `-m` command.

Now that we’ve been acquainted with Git, and created a new Git repository, added some files and committed them to Git we can start looking at using a remote server for collaborating on our project. To do this we’re going to use [https://github.com/](https://github.com/) the ‘Social Coding’ website.

Setting up a GitHub account
---------------------------

Note: if you need extra help (beyond what I’ve shared) then you can refer to the official GitHub help pages here: [http://learn.github.com/p/setup.html](http://learn.github.com/p/setup.html) So the first thing to do is to register for an account (you have to go through the ‘Pricing and Signup’ page to find the ‘free’ account set-up): [https://github.com/signup/free](https://github.com/signup/free) (as you’ll see on that page, there are paid for options available so your team of developers can collaborate with a version-controlled project using GitHub’s servers, rather than having to work out how to set-up their own ‘remote’ server to push code changes to).

Once you’ve signed up, I recommend the first thing you do is to create a SSH Public Key (you’re going to need it to be able to securely access remote files - and by ‘remote’ I mean access your project files on the GitHub server). GitHub have a useful page for explaining how to set-up the SSH Public Key: [http://help.github.com/mac-key-setup/](http://help.github.com/mac-key-setup/).

Most of you will find that you can skip the first part of the page and head straight down to the section titled ‘Generating a key’ (I mention how to set-up your SSH Public Key below, but do have a read over the provided link as it should help clarify what to do).

Generating an SSH Public Key
----------------------------

Basically, in the Terminal you type…

`ssh-keygen -t rsa -C "joe@bloggs.com"`

…obviously change the email address from `joe@bloggs.com` to whatever email address you used to sign-up to GitHub with. Terminal will then tell you…

    Generating public/private rsa key pair.
    Enter file in which to save the key (/Users/tekkub/.ssh/id_rsa):

…but your message will be slightly different in that the directory path in the brackets (e.g. `(/Users/tekkub/.ssh/id_rsa)`) will likely be different on your computer.

The next thing to do is to simply press `ENTER` and Terminal will generate a new SSH Public Key within the hidden ‘id_rsa’ file it mentions (if you didn’t hit `ENTER` then the Terminal would expect you to enter the name of a file to save the SSH Public Key into - so I didn’t bother messing around doing that and just saved the key into the file the Terminal suggested I use). Next Terminal will ask you to enter a pass phrase…

`Enter passphrase (empty for no passphrase):`

…I advise you enter a good password which you wont forget and just press `ENTER` again (Terminal will ask you to enter the same pass phrase again, so it’s sure you know what you typed).

Now Terminal will have generated your SSH Public Key, and you’ll need to provide this public key to GitHub so it knows it’s you connecting to its server and trying to change your project files. If you’ve never seen an SSH Public Key before then you may be mistaken that the gobble-dee-gook that the Terminal spit out at you after your last command was the SSH Public Key. Wrong!

You can’t see the key, but you can copy it to your clipboard (for pasting into the GitHub website). So now open your web browser and head back to your GitHub account page and look for the link in your account page for entering an SSH Public Key (direct link: [https://github.com/account#ssh_bucket](https://github.com/account#ssh_bucket)).

Now go back to the Terminal and enter the following command to copy your SSH Public Key to your clipboard…

`cat ~/.ssh/id_rsa.pub | pbcopy`

…once copied into your clipboard, go back to the relevant GitHub SSH page and paste your key in and save the changes to your account settings. This now leaves just one last thing for you to do while logged into the GitHub website and that is to create a new ‘repository’.

Creating a new GitHub repository
--------------------------------

To create a new repository you’ll need to go to this page [https://github.com/repositories/new](https://github.com/repositories/new) (you’ll find links all over the place for creating a new repository, but it’s easier if I just link to it for you).

Now enter the information GitHub asks for (I created a repository called ‘Project Template’ and it generated the following URL: https://github.com/Integralist/Project-Template).

Once you’ve created a new repository on GitHub, it will show you some Terminal commands you can run for setting up Git locally and pushing the project file to GitHub’s online repository.

Pushing your project up to GitHub’s repository
----------------------------------------------

In the Terminal, lets make sure we’re still in our project directory…

`cd ~/Dropbox/Project/`

…once we’re back in our project folder we can now set-up our remote server access…

`git remote add origin git@github.com:Integralist/Project-Template.git`

…OK, so what’s happening here? Well, we’re telling Git that we want to add a remote server and store it under the easy to remember name of ‘origin’. We could of called ‘origin’ anything we liked… E.g. ‘MyRemoteServer’

`git remote add MyRemoteServer git@github.com:Integralist/Project-Template.git`

…the line that follows the name we gave is the URL for the GitHub repository we created (and you’ll see this URL on your project page when you first set-up a new repository). Now that we have created an `origin` to hold our remote server information we can now ‘push’ our project online to GitHub…

`git push origin master`

…the above line uses the `push` command to tell your local Git to push your latest ‘commit’ to the specified remote server.

Now the word `master` which you can see at the end of the command simply refers to the default ‘branch’ that is set-up (I don’t go into the details of it here, but Git allows you to `branch` off your code.

So for example, if we decided we wanted to add some new features to our project, which could end up breaking things if not properly tested, we could create another branch called ‘new_feature’ which would be a complete copy of the current project for us to work from - and that way we leave the original ‘master’ branch untouched so we can continue to work on maintaining that copy of the project and allowing us to fix any bugs found in it while developing the new feature, then when the new feature is ready and tested we can then `merge` the new feature back into our ‘master’ branch - but these are all commands left for another time).

Now, back to the `push` command: As it is our first push, the Terminal will not be able to authenticate the host ‘github.com’ so we’ll just type ‘yes’ to confirm we trust it. For me, the push failed the first time I tried - this I found out straight away afterwards was because I needed to enter my pass phrase.

After it failed the first time I typed the `git push origin master` command again and this time the Terminal displayed a dialog window asking me to enter my passphrase for accessing my SSH Public Key. I entered my passphrase and then the push started to proceed successfully.

Now we can start pushing files any time we like to this particular project. I had a file called ‘robots.txt’ in my project.

Lets say I made an update to the file ‘robots.txt’ - I can push that on it’s own:

    git add robots.txt
    git commit -m 'Add some new robot configurations'
    git push origin master

Removing/Editing files
----------------------

To remove a file (e.g. lets remove the ‘robots.txt’ file) simply type…

`git rm robots.txt`

…then do the normal commit with message and push.

    git commit -m 'Remove the robots.txt file'
    git push origin master

To edit existing files is the same process as adding files. But beware, as you’ll get an error if the file hasn’t actually been changed!

Remember, Git tracks file *content*, NOT individual files. So it wont like you wasting its time by uploading files that haven’t changed at all.

Git Tips
--------

Any time you need to check the status of a `.git` repository:

`git status`

Show all Git commands:

`git`

Show where Git was installed:

`which git`

Show all recent activity:

`git log`

Show all activity (all branches):

`git log --all`

Show all activity (all branches) in a tree view (slightly more readable):

`git log --graph --all`

Show all activity (all branches) in a tree view (slightly more readable) BUT only shows the bare minimum details (i.e. the first seven characters of the hash and the commit message):

`git log --graph --all --online`

Shows all branches for this repository: NOTE: the Terminal will display all branches available with an asterisk (*) before the branch name that is currently activated…

`git branch`

Create a new branch called “my_new_branch” which is a complete copy of ‘master’ for you to mess around with:

`git branch my_new_branch`

Switch to a different branch:

`git checkout my_new_branch`

*IMPORTANT! When you switch between branches, the .git file (that contains all information about your repository) will show you a different set of files in Finder! So if you switch to the new branch you just created `my_new_branch` and then added a new file to your project’s directory (e.g. mynewfile.php), then this new file will be visible in Finder while you are still viewing the `my_new_branch` branch. The moment you switch back to the `master` branch the ‘mynewfile.php’ file will disappear from view.

Whatever changes were made on one branch will not be visible at all from the other branches. Since Git tracks contents, this works for files and whole directories down to lines and characters.*

The following command means you don’t need to use ‘git add’ you can just commit all tracked files

`git -a commit`

Git has a GUI tool for visualisation of commits/changes:

`gitk`

By default it shows only the branch you are currently viewing - if you want it to show both branches at once the use `gitk --all`.

If I wanted to merge a new branch ‘name_of_branch’ with my master branch then I would call:

`git merge name_of_branch`

*NOTE: It’s important to switch view back to the main branch you wish to merge other code with.*

Show all commits to this file (and who made them):

`git blame file_name`

To ‘tag’ a particular commit in your projects history - this could be a version number for a script update…

`git tag tag_name (e.g. git tag v0.5.6)`

*NOTE: tags must have no spaces (e.g. my_first_tag):*

An annotated tag:

`git tag -a tag_name`

An annotated tag with a comment:

`git tag -a tag_name -m "my first release candidate"`

To push your tags you need to do the following - because git push alone wont do it (thanks to [@mathias](http://twitter.com/mathias) for this information):

`git push --tags`

Show all tags in this project:

`git tag`

Show the last commit:

`git show`

Show the last commit that had this tag assigned after it (acts like a bookmark):

`git show tag_name`

The following command only works after looking at a tag:

`git describe`

*Note: it shows the name of the tag followed by how many commits have happened since its inception (as well as the first seven characters from the hash that references the commit, and the -g is not part of the hash; it’s a suffix that stands for “Git” - according to the Git documentation it “useful in an environment where people may use different SCMs”).

e.g. my_first_tag-1-gd8a7d27*

Tell Git to ignore certain files and formats
--------------------------------------------

`.gitignore` is a file that you can create to hold rules about what items Git should ignore.

A good example of using .gitignore is if you wanted a config script - e.g. Settings.php - that held your database username/password ignored so it wasn’t accidentally pushed to a public repository (such as GitHub):

You can use a `#` as a comment line and then specify the types of files or specific files to be ignored, such as:

    #Mac OS X files
    .DS_Store

    # VIM leave-behinds
    *.swp

You can also ignore all files of a certain type except one by using the bang (!) such as:

    *.log
    !errors.log

Best practices for Commit Messages
----------------------------------

As requested by [@Mathias](http://twitter.com/mathias) (on quite a few occasions…) there are a few best practices to consider when writing your commit messages.

One of the most obvious and useful is to split your commit message into a ‘summary’ and ‘body’ (this is done by including a blank line in-between the first and second lines of your commit message).

The ‘summary’ should (ideally) be no more than 50 characters. This is so that when other users are viewing your commit messages (via different means) then they can always understand what the commit is about, and then if they wish to read the full commit message (i.e. the ‘body’ part - which should include further information on the specifics of the commit) then they can do.

And there are quite a few places where the ‘summary’ part of your commit message would come in really helpful to other users…

The following bullet points are credited to [tbaggery.com](http://tbaggery.com/):

* `git log --pretty=oneline` - shows a terse history mapping containing the commit id and the summary
* `git rebase --interactive` - provides the summary for each commit in the editor it invokes
* if the config option `merge.summary` is set, the summaries from all merged commits will make their way into the merge commit message
* `git shortlog` uses summary lines in the changelog-like output it produces
* `git format-patch` git send-email, and related tools use it as the subject for emails
* reflogs, a local history accessible with git reflog intended to help you recover from stupid mistakes, get a copy of the summary
* `gitk` has a column for the summary
* GitHub uses the summary in various places in their user interface

When writing your commit message it’s probably a good idea to write in the ‘present tense’ (e.g. ‘fix’ rather than ‘fixed’) - this is to help keep consistency between other git commands such as `git merge` | `git revert`.

Also, when writing the ‘body’ part of your commit message, make sure to keep the character length to 72 per line. Feel free to read up on the details of why at [tbaggery.com](http://tbaggery.com/), but the principle is basically that using certain git commands on certain set-ups could cause the text to overflow off the edge of the screen. The previous link also includes some tips on how to achieve this via Vim or TextMate.

Conclusion
----------

And that’s about it for the “Git Basics”. There is so much more that you can learn and I can’t recommend the book “Getting Good with Git” enough! Go check that out here: [http://rockablepress.com/books/getting-good-with-git/](http://rockablepress.com/books/getting-good-with-git/).