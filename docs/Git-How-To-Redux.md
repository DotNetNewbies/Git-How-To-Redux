### What's covered in this document  
* Review of markdown, pick cheat sheet  
* .gitignore files: https://github.com/github/gitignore  
  * glob patterns: https://en.wikipedia.org/wiki/Glob_%28programming%29  
* branching revisited  
* workflow discussion  
* remote repositories  
* difference between `git pull` and `git fetch`  
* command refresher

### Markdown info  

This is the "official" specification for GitHub flavored markdown:  
https://github.github.com/gfm/  

This link, while terse, is a good example:  
https://github.com/krman009/Github-Flavoured-Markdown  

This link shows how to embed images:  
https://gist.github.com/apackeer/4159268#images  

### .gitignore files  

The basics are these:
* Blank lines are lines starting with a '#' are ignored  
* Standard "glob" patterns work.  
  * An asterisk (*) matches zero or more characters.  
  * Letters in brackets, e.g. [aeiou], match any single character inside the  
    brackets, e.g. [AaEeIiOoUu~]-log ignores all files that start with  
    A, a, E, e, I, i, O, o, U, u or a tilde (~) character that contain "-log".  
    The characters _are_ case-sensitive.  
  * A question mark (?) matches any single character.  
  * Characters enclosed in brackets, separated by a hyphen (-) specify a range  
    of characters e.g. [0-9] matches 0,1,2,3,4,5,6,7,8,9.  
  * Two asterisks (**) match nested directories, e.g. a/**/z matches a/z, a/b/z  
    a/b/c/z, etc.  
  * A leading forward slash specifies a single directory in the root to ignore  
    not some-sub-directory/directory.  
  * A trailing forward slash (/) ignores all files in the specified directory  
    e.g. build/ ignores all files in the build directory.  
  * Negation of a pattern is done by preceding the pattern with a bang (!).  

A good reference for additional information on .gitignore files:  
* https://github.com/github/gitignore  
  * You'll see a reference to "glob patterns" in the aforementioned reference,  
    a good reference for what those are is found here:  
      https://en.wikipedia.org/wiki/Glob_%28programming%29  

### Branching revisited  

The following process will result in a merge conflict:
* `git init Playground` <-- Create a new local repository    
* 'git status` <-- should show on branch "main"  
* `notepad test.txt` (yes, create the not-found file).  
* Insert the following text into the file and save it:  
    "This is from the main branch and only the main branch."  
* `git stage *` <-- Stage the changes  
* `git commit -m "Main branch: Initial commit"` <-- commit changes  
* `git branch feature` <-- Create the "feature" branch  
* `git checkout feature` <-- Change to the "feature" branch  
* `notepad test.txt` <-- re-open the file  
* Insert the following text and save the file:
    "...and this is from the "feature" branch."  
* `git stage *` <-- Stage the changes  
* `git commit -m "Feature branch: Initial commit"`  
* `git checkout main` <-- change back to the "main" branch  
* `notepad test.txt` <-- re-open the file  
* Insert the following text and save:  
    "...and this is a change from the main branch."  
* `git stage *` <-- Stage the changes
* `git commit -m "Main branch: More changes"`  
* `git merge feature`  
  ...and this generates a merge conflict. Open the file (`notepad test.txt`)  
  and examine the content. It should look something like this:
  
>  This is from the main branch and only the main branch.  

> `<<<<<<< HEAD`  

> `...and this is a change from the main branch.`  

> `=======`  

> `...and this is from the "feature" branch.`  

> `>>>>>>> feature`  

Why the conflict? Basically because the modifications on both the "main" branch  
and the "feature" branch occurred in exactly the same place in the file in  
question and git can't decide who is right so it throws up its collective  
hands and dumps the problem in the developers lap and says "fix this!".  

The error stems from the fact that both the "main" branch and the "feature"  
branch were modified _without merging with the other branch before continuing_  
_modifications_.  

The sequence here, in a nutshell was:
* create a file, make changes, save changes, commit changes to main.
* create and change to feature branch, modify file, save changes, commit  
  modifications to feature branch.
* change to main branch, modify file, save changes, commit changes to main  
  branch.  
* Attempt merge: **FAIL** because no merge took place before making  
  modifications to the branch.  

Now, under normal circumstances, git does a pretty good job of figuring out if  
there are going to be conflicts by examining the locations of the changes. If  
the changes are in two different places, git will attempt to merge the two and  
not complain about it.  
Doesn't mean the resulting code will actually _work_, it just means that git  
was able to take the two text streams and bring them together without each  
stream stepping on the content of the other stream.  

The author is _not_ going to try to explain nor teach how to do merge conflict  
resolution. That is left as an exercise for the student. Go read the book,  
search on-line, make some disasters of your own and then walk through the  
possible solutions with a git master to gain a better understanding of what  
needs to take place here.  

### Workflow discussion  

A place to start:   
* https://datasift.github.io/gitflow/IntroducingGitFlow.html  

This site describes a pattern of work that may be used to direct the flow of  
project development from initial concept to delivered product.  
It is _certainly not_ the only way of doing business but it has become somewhat  
popular with industry at large, mainly because few folks actually understand  
git and the power it offers and were quite happy that someone took a stab at it  
and provided "an answer" that seemed to work. Made management happy, anyway.

One can see from the diagram that this particular solution relies on long-lived  
branches and merges which take place from said branches. Each branch has a  
designated purpose and developers need to be both cognizant of the use of those  
branches and take care to not put the "wrong thing" in the wrong branch.  

The keys to a "proper" work flow are these:
* Correct branch names and their intended uses.
  * Meaning that a branch called "main" should be created and used for a  
    specific purpose _and only that purpose_ e.g. generate production artifacts.  
* Decide on branch longevity. Do they live a long time or are they targeted for  
  a specific purpose and then merged and deleted.  
* A good "gatekeeper" strategy. Good workflow relies on the movement of code  
  updates through the git system based on acceptability of the changes and  
  what and where the are applied e.g. test code, hot fixes, etc.  
  Thus having a competent keeper of gates to control the flow of code updates  
  through the system is a must and they will arrange for code reviews at the  
  appropriate time, move code from one branch to the other via pull requests,  
  etc.  
* Everybody plays the same game! Once the workflow is established, _**EVERYONE**_  
  _**MUST**_ follow the work flow without exception and without modification  
  unless the entire affected development group agrees on the change.  
  Arbitrary change just for the sake of change or "because I said so" or  
  because "management doesn't like it" _is not acceptable and all such changes_  
  _must be rejected!_  

### Stashing your work  

The scenario: You are plugging along on your feature branch when the boss comes  
by and says "you gotta stop what you're doing and fix this, right now!". You  
don't want to commit your half-finished work, but you don't want to lost it  
either by checking out a branch and paving over your current work. So...what to  
do? Stash your work on a stack, switch branches, do the boss' fix and then go  
back to what you were doing before. How? Stash your work on a "stack" of  
stashes that can be recalled later.  
Think of a stash as that post-it note you stick on your monitor to remind you  
of something that needs doing but isn't worth a formal entry in your "TO-DO"  
list. How to do it? Like this:  

`git stash`

That's it. Now, you can switch branches, do your thing and then switch back to  
your branch and execute a `git stash list` to see how many stashes have been  
done (best idea is to not do more than one, but it's possible) or, if you are  
ready to begin work again, `git stash apply`. That will pull the latest stash  
off of the stack and apply it to the current working directory. As long as your  
current working directory is clean (no un-staged or un-committed files), you'll  
be good, otherwise, you'll get merge errors (remember, git makes it _very hard_
to screw things up).  
Since the stash mechanism is a stack, the entries are zero-based indexed,  
meaning that the first entry is index 0, the second entry is index 1 and so  
forth.  
You can delete a stash by executing a `git stash drop stash@[<index>]` where
`<index>` is the index number of the stash you wish to remove.  

The `git stash` command has a couple of tricks that make it even more fun to  
play with:

`git stash --keep-index` to not stash anything that is already staged. Another  
fun one is `git stash --include-untracked` or `git stash -u` for stashing new  
files that aren't staged yet but are part of the work you're doing. There are  
more options, please see the help for additional fun things you can do with  
stash.  

### Sub-modules  

What's a sub-module? Easy - it's a git repository that is stored as a branch  
within another git repository. Why would you do this? Let's say you have a  
library of code that your main project uses _and you know you might have to__  
__make changes to that library__, then you might want to include a reference  
to the library's repository so your changes aren't local, but reflected  
throughout whichever other projects use that library.  No sense in having  
multiple variants of the same library out in the world, better to ensure that  
changes to said library don't break anything anywhere else.  
So, how to create a sub-module? Like this:  

`git submodule add <some url to the other repository>`

Now, by default, the command will do the equivalent of a `git init` in a  
sub-directory of your current project using the same name as the repository,  
e.g. if the repository is "AOC.Library", then the sub-module will be pulled  
down into a directory also named "AOC.Library".  

Now, let's say you are cloning a repository with sub-modules already attached.  
You'll issue a `git clone <url>` command just as always. How do you know that  
this repo has sub-modules? Look for a file named ".gitmodules". If the repo  
has one, then there are sub-modules.  
To initialize the sub-modules, you'll still need to initialize them as well.  
To do so, execute the following:  

`git submodule init`

will cause the git command to scan the .gitmodules file looking for the  
sub-modules and initialize them as separate git repositories in the appropriate  
directories. Once complete, issue a `git submodule update` command to fetch all  
of the repo information for all the sub-modules down and apply it to the local  
repositories. Or, you can cheat and issue your first `git clone` command like  
this:

`git clone --recursive <some url>`

and all that work will be done for you.  

There is much more to sub-modules that this simple introduction. Best to read  
chapter 7 in the referenced book for all the scenarios.  

### Remote Repositories  

Refer to page 61 of the book as well as chapter 5 for additional information  
on remote git servers.  

The single outstanding feature of a "remote" repository is that it is  
_**exactly the same**_ as the repository found on your local machine save for  
a single difference - there is _no_ working directory with a "remote".

Everything else is identical in all respects, especially the content of the  
.git folder - the file structure exactly mirrors what's on each machine that  
references the remote (as long as everyone has kept up their own copy, that is).  

To see what remotes are attached to your current repository (meaning that the  
repository is aware of the remote and will communicate with it), one may  
execute the following command:

`git remote -v`

which will display the short name of the remote e.g. "origin" and the url of  
said remote.  

Executing a `git fetch` will pull down all the branch information for the  
specified remote and store it as a copy in your local git file system.  
_However_, you do **not** have a read/write version of the branch to work on  
only a _copy_ of the branch as originally placed on the remote server. To get  
a working copy of the branch to work on yourself, you need to merge that copy  
of the remote branch that is now on your local machine, e.g. origin/feature,  
with your currently checked out branch.  
It's a good idea to keep from confusing yourself, to create a local branch with  
the same name as the remote branch to perform the merge with, e.g.  

`git checkout -b feature origin/feature`

will create a local branch, "feature", checkout that newly created branch and  
then merge the content of "origin/feature" with your new "feature". This will  
give you a starting place that is where "origin/feature" left off.  

Doing this operation automatically creates what is known as a "tracking branch".  
Tracking branches are local branches that have a direct relationship with a  
remote branch. Also, performing a `git pull` while on one of these branches  
will fetch all the remote references and then automatically merge the content  
of the remote branch with the current tracking branch.  

This is why `git pull` and `git push` (as written, no other arguments) "just  
work" out of the box. What was actually created was a tracking branch against  
"main" when you clone a repository. This is a common enough scenario that git  
even provides a shortcut command to perform the same action:

  `git checkout --track <remote server>/<remote branch>`

...and, obviously, you can change the name of the local branch vs the remote  
branch, however, the author _strongly_ discourages such activity, it just leads  
to confusion.  

To see what tracking branches you have set up, use:

`git branch -vv` (no, that's not a mis-type, it's actually "-vv")  

So, to summarize, if you set up a tracking branch, `git pull` and `git push`,  
as written, no arguments, will "just work" as long as your current working  
directory is pointing to (meaning you have checked out) a tracking branch.  

To get your locally committed changes up to the selected remote server, one  
executes the following:

`git push <remote> <branch>`  

where `<remote>` is the name of the remote server, e.g. "origin" and `<branch>`  
is the branch to be pushed e.g. `main`.  

### Difference between a `git pull` and a `git fetch`  

`git fetch` pulls all of the .git directory information to your local machine  
from the specified remove server.  
This is how one maintains a current copy of the working repository. Some even  
go so far as to create a cron job (background task) to execute a `git fetch`  
on a regular basis just to keep their repository fresh and current.  

### Command refresher

`git config --list --show-origin`: Check your configured settings and display  
which config file the setting comes from.  
