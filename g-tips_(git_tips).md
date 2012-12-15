#G-Tips (Git tips)

I thought I would get down in a blog post the different [Git](http://git-scm.com) commands and tips that I find really useful, because every now and then it seems I need to refer back to these notes (which up until this point have been in a txt file in my Dropbox) if I've not used a particular command in a while. 

Hopefully you'll find them useful too…

1. Show where Git is installed
2. Show the Git version installed
3. Update your global user details
4. Set-up a global ignore file
5. Adding all files (inc. those marked as deleted)
6. Writing a long commit
7. Viewing file changes while writing your commit
8. Viewing what files have been committed
9. Improving `git log` with `git lg`
10. Shorter `git status`
11. Finding a commit that includes a specific phrase
12. Only merging the files you want
13. Stashing changes you're not ready to commit
14. Revert all changes back to last commit
15. Unstaging files
16. Untrack a file without deleting it
17. Amend your last commit
18. Show the files within a commit
19. See any changes between current working directory and last commit
20. See changes between two commits
21. Creating a branch and moving to it at the same time
22. Deleting a branch
23. Viewing all branches of a remote
24. Checkout a remote branch
25. Remove a remote
26. Revert a specfic file back to an earlier version
27. Viewing all commits for a file and who made those changes

##Show where Git is installed

`which git`

##Show the Git version installed

`git version`

##Update your global user details

```
git config --global user.name "Your Name"
git config --global user.email "Your Email"
git config --global apply.whitespace nowarn # ignore white space changes!
```

##Set-up a global ignore file

First create the global ignore file…

`touch ~/.gitignore_global`

Then add the following content to it (*this is a standard ignore file but I've added some Sass CSS pre-processor files to it*)…

```
# Compiled source #
###################
*.com
*.class
*.dll
*.exe
*.o
*.so
*.sass-cache
*.scssc

# Packages #
############
# it's better to unpack these files and commit the raw source
# git has its own built in compression methods
*.7z
*.dmg
*.gz
*.iso
*.jar
*.rar
*.tar
*.zip

# Logs and databases #
######################
*.log
*.sql
*.sqlite

# OS generated files #
######################
.DS_Store
.DS_Store?
._*
.Spotlight-V100
.Trashes
Icon?
ehthumbs.db
Thumbs.db
```

You can let Git know about your global ignore file by editing your global `.gitconfig` file…

`nano ~/.gitconfig`

…then adding the following to it… 

```
[core]
	excludesfile = /Users/<home-directory>/.gitignore_global
```

…or once the `.gitignore_global` file is created you can just tell git by using this short-hand command…

`git config --global core.excludesfile ~/.gitignore_global`

##Adding all files (inc. those marked as deleted)

`git add -A`

##Writing a long commit

A short git commit message would look like this…

`git commit -m "My short commit message"`

…but you should really be writing longer more descriptive commit messages which you do like so:

`git commit`

…what this does is open up the default editor for commit messages (which for most is Vim). Now Vim is a bizarre editor with all sorts of odd shortcuts for adding text. I've only used Vim to write commit messages (nothing else) so I have a very focused set of commands to write my commands…

Press `i` which puts Vim into 'insert' mode (meaning you can actually write)

```
This is my short description for this commit

- Here is a break down of my changes
- Another note about a particular change
```

After I've written my commit I just need to save the commit and exit Vim…

* Press `Esc`
* Press `:wq` (the colon means you can execute more commands, w = write, q = quit)

##Viewing file changes while writing your commit

`git commit -v`

##Viewing what files have been committed

`git ls-files`

##Improving `git log` with `git lg`

To get a better looking `git log` we need to write an alias called `git lg` that is just made up of standard Git commands/flags but when put together (along with specific colour settings) means we can have a short git command that provides us lots of useful information.

What we need to do is open the `~/.gitconfig` file and then add the following content… 

```
[alias]
	lg = log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative
```

##Shorter `git status`

As per the above tip, we can create two extra alias' which give us a shorter command to type (I don't know about you but when typing really fast I seem to always misspell the word 'status') and doesn't show us all the unnecessary crap that someone new to Git needs to see.

What we need to do is open the `~/.gitconfig` file and then add the following content… 

```
[alias] 
	st = status
    sts = status -sb
```

…you don't need to specify `[alias]` if it's already in the file (see previous tip).

Now typing `git st` will be the same as `git status`, and typing `git sts` will be the same as `git status -sb`.

##Finding a commit that includes a specific phrase

`git log --grep=<your-phrase-here>`

For example, `git log --grep=CSS` will display all commits that contain the word 'CSS' in the message.

##Only merging the files you want

`git checkout <branch-name> <file1> <file2> <file3>`

##Stashing changes you're not ready to commit

If you make changes to your branch and then want to quickly change branches without first having to commit your current 'dirty state' then run:

`git stash`

To apply a stashed state (git assumes the most recent stashed state if none specified) use: 

`git stash apply`

To see which stashes you've stored (on any branch) use:

`git stash list`

If you have multiple stashes under a branch (e.g. `stash@{1}` `stash@{2}` `stash@{3}`) then you can reference a particular stash using:

`git stash apply@{2}`

To view the contents of a stash use:

`git stash show -p stash@{n}`

…where 'n' is the numeric index of the stash

Applying the stash doesn't mean it's removed from your list of stashes though(!) so you need to run:

`git stash drop stash@{<index>}`

e.g. `git stash drop stash@{2}`

You can also apply and drop the stash at the same time:

`git stash pop`

If you stash some work, leave it there for a while, and continue on the branch from which you stashed the work, you may have a problem reapplying the work. If the apply tries to modify a file that you’ve since modified, you’ll get a merge conflict and will have to try to resolve it. If you want an easier way to test the stashed changes again, you can run `git stash <branch>` which creates a new branch for you, checks out the commit you were on when you stashed your work, reapplies your work there, and then drops the stash if it applies successfully.

##Revert all changes back to last commit

`git reset --hard`

##Unstaging files

To unstage files we've added to the staging area we need to run the command `reset HEAD` but that's a bit ugly and awkward to remember. What would be easier is if we could just say `git unstage`, so let's create an alias to help make that easier!

Open up the file `~/.gitconfig` and then add the following content… 

```
[alias]
	unstage = reset HEAD
```

Note: you don't need to specify `[alias]` if it's already in the `~/.gitconfig` file.

You can also unstage a single file using:

`git reset <file>`

If you've staged files before any commits have been set (e.g. right at the start of your project) then you'll find the above wont work because technically there are no commits to revert back to. So instead you'll need to remove the files like so…

`git rm --cached <file>`

##Untrack a file without deleting it

If you want to have Git stop tracking a file it's already tracking then you would think to run:

`git rm <file>`

…but the problem with that command is that Git will also delete the file altogether!? Something we usually don't want to have happen.

The work around to that issue is to use the `--cached` flag:

`git rm --cached <file>`

##Amend your last commit

If you make a commit and then realise that you want to amend the commit message then don't make any changes to the files and just run…

`git commit --amend`

…which will open up the default editor for handling commits (usually Vim) and will let you amend the commit message.

If on the other hand you decide that after you've written a commit that you want to amend the commit by adding some more files to it then just add the files as normal and run the same command as above and when Vim opens to let you edit the commit message you'll see the extra files you added as part of that commit.

##Show the files within a commit

`git show <hash> --name-only`

##See any changes between current working directory and last commit

`git diff`

To show specific changes use the `--word-diff` flag:

`git diff --word-diff`

##See changes between two commits

`git diff <more-recent-hash> <older-hash>`

##Creating a branch and moving to it at the same time

`git checkout -b <branch-name>`

##Deleting a branch

`git branch -D <branch-name>`

##Viewing all branches of a remote

`git branch -a`

##Checkout a remote branch

What normally happens is this: you clone down a repository from GitHub and this repo will have multiple branches, but if you run `git branch` locally all you see is the `master` branch.

If you run `git branch -a` you can see all the branches for that remote repository but you just can't access them or check them out?

So if you want to access the other branches within that repo then run the following command:

`git checkout -b <new-local-branch-name> origin/<remote-branch-name>`

…this will create a new branch named whatever you called it and contains the content of the remote branch you specified.

##Remove a remote

`git remove rm <remote>`

##Revert a specfic file back to an earlier version

`git checkout <hash> <file-name>`

##Viewing all commits for a file and who made those changes

`git blame <file>`