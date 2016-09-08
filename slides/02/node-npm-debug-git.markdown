---
layout: slides
title: Node, NPM, Debugging, Git, and GitHub
---

<section markdown="block" class="intro-slide">
# {{ page.title }}

### {{ site.course_number}}-{{ site.course_section }}
</section>


<section markdown="block">
# (Hopefully, This Will Help Contextualize the Homework)
</section>

<section markdown="block">
## Node?

__Hmmmm. For a class on Node, we haven't really talked about it too much yet. Does anyone know what Node.js is exactly?__ &rarr; 

* {:.fragment} a JavaScript server side and networking framework 
	* {:.fragment} like JavaScript on the browser, minus the DOM/HTML stuff
	* {:.fragment} but... with more I/O and networking support added in
* {:.fragment} designed to maximize throughput and efficiency through __non-blocking I/O and asynchronous events__
* {:.fragment} some technical details:
	* {:.fragment} it's built on top of V8 (Chrome's JavaScript engine)
	* {:.fragment} it's written in C, C++, and JavaScript
</section>

<section markdown="block">
# In Node, All I/O is Non-Blocking and Asynchonous 

</section>

<section markdown="block">
##  Hold on ... I/O?

__What exactly do we mean by I/O__ &rarr;

* {:.fragment} reading or writing to a database
* {:.fragment} requesting data from a web service
* {:.fragment} scanning through a file
* {:.fragment} waiting for _some network connection_
* {:.fragment} you know... any __input__ and __output__
</section>


<section markdown="block">
## What does blocking I/O look like? 

Using Python and the requests module, it may look something like this. __What do you think the following code would print out?__ &rarr;

<pre><code data-trim contenteditable>
import requests
print('Start')
response = requests.get('http://www.google.com')

# just print out the first 30 characters of the response body
print(response.text[0:30])
print('Done!')
</code></pre>

<pre><code data-trim contenteditable>
Start
<!doctype html><html itemscope
End
</code></pre>
{:.fragment}
</section>

<section markdown="block">
## And non-blocking I/O

This example uses a Node.js library called Request (as well!). __What does this output?__ &rarr;

<pre><code data-trim contenteditable>
var request = require('request');
console.log("Start");
request('http://www.google.com', function (error, response, body) {
    // just print out the first 30 characters of the response body
    console.log(body.slice(0, 30)) 
})
console.log("Done!");
</code></pre>

<pre><code data-trim contenteditable>
Start
Done!
<!doctype html><html itemscope
</code></pre>
{:.fragment}
</section>

<section markdown="block">
## Weird, Huh? 

__Why was "Done" output before the response body?__ &rarr;

* the function to print out the body was actually an asynchonous __callback__
* a __callback__ is a function passed as an argument to another function that is expected to be executed at some later time (perhaps when an operation is completed, or a specific event occurs)
* __the callback function is not executed immediately__
* instead, <code>console.log("Done!")</code> is executed next
* when the request to google is done, the callback function is executed, which is at the end of the program
{:.fragment}

<br>
(Other languages have asynchronous, event-driven frameworks as well, including [Twisted](http://en.wikipedia.org/wiki/Twisted_(software)) for Python and [Eventmachine](http://en.wikipedia.org/wiki/EventMachine) for Ruby)
{:.fragment}
</section>

<section markdown="block">
## Some More Details

* Node applications (your actual JavaScript code) are actually a __single process__ 
* but all I/O operations are non-blocking and happen in the background
* this is done through an __event loop__
	* Node just continues to listen for events
	* you specify what code to execute whenever an event happens
	* if something requires I/O, that's spun off in the background, so the event loop can handle other callbacks
* this is all based on the assumption that I/O is the most expensive operation, so why wait for it when you can move on to other code to execute?
* your node app can still block, though (even if I/O is asynchronous)... __how?__ &rarr;
* {:.fragment} if your code is computationally heavy!
</section>

<section markdown="block">

# "Everything runs in parallel, except your code"

</section>
	
<section markdown="block">
## Aaaand More Details

[Even more about single-threaded, but non-blocking in Node](http://stackoverflow.com/questions/14795145/how-the-single-threaded-non-blocking-io-model-works-in-node-js)

A challenge with callbacks is that it may be difficult to acquire the context of the original function that spawned the callback. With JavaScript, though, it's easy! __Why?__

* quoted from the SO post above
* "Every function that requests IO has a signature like function (... parameters ..., callback)" and needs to be given a callback that will be invoked when the requested operation is completed 
* "Javascript's support for __closures__ allows you to use variables you've defined in the outer (calling) function inside the body of the callback - this allows to keep state between different functions that will be invoked by the node runtime independently"
{:.fragment}
</section>

<section markdown="block">
# Node Works Because of JavaScript's Language Features!

</section>
<section markdown="block">
## OK, Got It... 

So... When should you used Node?

* I/O bound workloads (not so much for CPU bound!)
* when you don't want to deal with the complexities of concurrent programming, and your application can fit into an event-driven framework

<br>
Some actual examples

* for web stuff... 
	* like high traffic APIs
	* _soft real time apps_
</section>
<section markdown="block">
## A Bunch of Parentheses

* (not sure if a console-game was a great use-case...)
* (waiting for user input can be considered an I/O bound app, though!)
* (but we used a synchronous prompt library to gather input)
* (you can rewrite with another lib if you want!)
</section>

<section markdown="block">
## Using Node

__(This is obvs, I know, but just for completeness)__. After you've installed via your apt/homebrew/download, you can run node in two ways:

* as a REPL / interactive shell,  which is just: <code>node</code> (CTRL-D exits)
* or to execute a program: <code>node filename.js</code>

<br>
Of course, you can write vanilla Node programs using whatever's built in, but you'll probably some libraries
</section>

<section markdown="block">
### NPM

__NPM__ is Node's official package manager.  __Does anyone know of any other package managers for other languages?__  &rarr;

* {:.fragment} __gem__ for Ruby
* {:.fragment} or __pip__, __easy_install__ for Python
* {:.fragment} or __CPAN__ for Perl
* {:.fragment} or __composer__ for PHP
</section>

<section markdown="block">
## Installing Packages

<code>npm install packagename</code>

* by default, NPM installs packages in a directory called <code>node_modules</code> in the directory that you run it in
	* __let's see that in action__ &rarr;
	* which is why you should <code>.gitignore</code> <code>node_modules</code> (you don't really want all of the dependencies in your repository)
* that means NPM doesn't install globally by default (nice... unlike _other_ package management systems)

</section>

<section markdown="block">
## Local vs Global Installation

You can use the <code>-g</code> flag to install globally if you need to (and it will be necessary for some things): <code>npm install packagename -g</code>

* some libraries are actually commandline tools that you want to use throughout your system - install these globally
* while others are libraries for specific apps that you're developing (anything that you'd <code>require</code> in your program) should be installed locally

</section>

<section markdown="block">
## More About Global Package Installation

__Why do we want to avoid installing modules globally?__ &rarr;

* {:.fragment} multiple apps, different dependencies
* {:.fragment} maybe even OS level dependencies! 

<br>
__BTW, what are some analogous tools that we'd use to avoid installing packages globally for python and ruby?__ &rarr;
{:.fragment}

* {:.fragment} __rvm__
* {:.fragment} __virtualenv__
</section>


<section markdown="block">
### package.json

Lastly __NPM__ can use a file called __package.json__ to store dependencies

It's like: 

* gemfile - ruby
* requirements.txt - python

Soooo... if you're program depends on specific modules (maybe like a module that allows prompting a user for input)

* it may be a good idea to put that module in package.json
* ... so that you don't have to remember all of the requirements!
</section>


<section markdown="block">
### Node.js - require

Node's require is analogous to:

* PHP's __include__
* Ruby's __require__
* Python's __import__
* Java's __import__

<br>
It returns an object... and that object most likely has some useful methods and properties. From our request example earlier:

<pre><code data-trim contenteditable>
var request = require('request');
</code></pre>
</section>
<section markdown="block">
# Debugging

</section>
<section markdown="block">
## Let's Write a Quick Function

__Create a function that determines if a set of parentheses is balanced (what does that mean?):__ &rarr;

* __name__: isBalanced
* __signature__: isBalanced(string) &rarr; boolean
* __parameters__: a string (assume that it only consists of "(" and ")")
* __returns__: true if the parentheses are balanced, false if they aren't
* {:.fragment} a set of parentheses are balanced if there's exactly one closing parentheses for each open parentheses
* {:.fragment} __why wouldn't comparing counts work?__ &rarr;
* {:.fragment} <code>)(</code>
* {:.fragment} one algorithm is to push open parentheses when you see them, but pop them when you see closed parentheses

</section>

<section markdown="block">
## Here's an Implementation

<pre><code data-trim contenteditable>
var isBalanced = function(s) {
    var stack = [], balanced = true;

    for(var i = 0; i < s.length; i++) {
        var ch = s.charAt(i);    
        if (ch === '(') {
            stack.push(ch);
        } else if (ch === ')') {
            if (stack.length === 0) {
                balanced = false;
                break;
            } 
            stack.pop();
        }
    }

    if (stack.length !== 0) {
        balanced = false;
    }

    return balanced;
};
</code></pre>

</section>

<section markdown="block">
## Testing It

<pre><code data-trim contenteditable>
console.log(isBalanced('()'));
console.log(isBalanced(')('));
console.log(isBalanced('()()'));
console.log(isBalanced('()())'));
</code></pre>

</section>
<section markdown="block">
## Let's Check Out Some Debugging Tools...

Let's intentionally create a logical error in our code (perhaps return immediately after popping). __To figure out what went wrong, we can...__ &rarr;

* {:.fragment} just print stuff out with console.log (the old fashioned way!)
* {:.fragment} use a debugger
</section>

<section markdown="block">
## The Commandline Debugger

<code>node debug myscript.js</code>

Let's give this a whirl...

Wait... what? [The commandline is hard.](http://www.nytimes.com/1992/10/21/business/company-news-mattel-says-it-erred-teen-talk-barbie-turns-silent-on-math.html)  [Let's go shopping](http://itre.cis.upenn.edu/~myl/languagelog/archives/002892.html)
{:.fragment}

_Terrible_. Yay 90's. Though the __"elaborate nationwide publicity stunt designed to ridicule sexual stereotyping in children's toys"__ was pretty neat!
{:.fragment}
</section>

<section markdown="block">
## Don't Worry, There Are Other Debugging Tools Out There

* [Sublime Web Inspector](http://sokolovstas.github.io/SublimeWebInspector/) (for Sublime, of Course)
* [WebStorm](http://www.jetbrains.com/webstorm/) a commercial JavaScript IDE <span class="fragment">(what a spectacular name, though)</span>
* [Node Inspector](https://github.com/node-inspector/node-inspector) - just like Chrome's Web Inspector!

<br>
I'm partial to __Web Inspector__ because it's IDE agnostic, and as you know, as <strike>an old person</strike> vintage editor enthusiast, I use vim.
{:.fragment}
</section>

<section markdown="block">
# <strike>Fail</strike> Live Demo Time
</section>

<section markdown="block">
## Node Inspector

* install node-inspector: <code>sudo npm install -g node-inspector</code>
* (hey did you notice that it's global... __why__ &rarr;)
* run node-inspector: <code>node-inspector</code>
* open another terminal window...
* start your app, the --debug-brk option will pause on the first line: <code>node --debug-brk app.js</code>
* open Chrome and go to http://localhost:8080/debug?port=5858

</section>

<section markdown="block">
## Node Inspector Continued

Same as Chrome Web Inspector!  [See the docs!](https://developer.chrome.com/devtools/docs/javascript-debugging)

Like any other debugger, you can:

* continue
* step over
* step in
* step out
* watch (check out the variables in the closure even!)
* set breakpoints
* have. lots. of. fun.
</section>

<section markdown="block">
# Great. Wrote Some Code. Let's Put it in Version Control.
</section>

<section markdown="block">
## Version Control (With Git)

The material in these slides was sourced from:

* [gitref.org](http://gitref.org/)
* [pro git](http://git-scm.com/book/en/Getting-Started-About-Version-Control)
* [git - the simple guide](http://rogerdudler.github.io/git-guide/)
</section>

<section markdown="block">
## Um.  First... Archiving?

__What are some ways you've used to keep / save different versions of files?__ &rarr;

* adding a date to a file name?
* adding extensions to files, like .bak?
* organizing copies by folders?
* ...perhaps folders with timestamps / dates
* ummm... etc.
{:.fragment}

<br>
__What are some drawbacks of these methods of saving versions of files?__ &rarr;
{:.fragment}

* it's all manual 
* ... and, consequently, tedious and error prone
{:.fragment}
</section>

<section markdown="block">
## Sharing / Collaborating

Have you ever worked on a programming project with more than one person?  __How did you share your code?__ &rarr;

* email?
* usb?
* dropbox?
{:.fragment}

<br>
__What are some issues with these methods (well, except for dropbox)?__
{:.fragment}

* hard to find a specific version
* which one is the latest?
* how do you deal with conflicting changes?
{:.fragment}
</section>

<section markdown="block">
# Enter: Version Control
</section>

<section markdown="block">
## What's Version Control?

__Version control software__ allows you to record changes to a file or set of files over time so that you can inspect or even revert to specific versions.

* can be applied to any kind of file, but we're mostly using text files
* with version control, you can:
	* leave __comments__ on changes that you've made
	* __revert__ files to a previous state
	* __review__ changes made over time, and track __who__ made them
	* __you can easily recover from accidentally breaking or deleting code__
	* automatically __merge__ changes to the same file
</section>

<section markdown="block">
## So... Um.  Why?

<aside>What does this all mean?</aside>  

Version control can help us:

* stop using .bak files!
* collaborate with others 
	* share our code
	* merge changes from different sources
* document our work
* group related changes
</section>

<section markdown="block">
# Oh.  And it's kind of _expected_ that you know this as a professional programmer.
</section>

<section markdown="block">
##  We’re Using git!

* __git__ is the version control system that we're using
* it’s a __modern distributed version control__ system
* it has emerged as __the standard__ version control system to use
* (some others are...)
	* mercurial
	* subversion (svn)
	* cvs
</section>

<section markdown="block">
## A Bit About Git

It was developed by Linus Torvalds... __who?__ &rarr;

<div markdown="block" class="img-container">
![Linux](http://cdn.arstechnica.net/wp-content/uploads/2013/02/linus-eff-you-640x363.png)
</div>

<div class="fragment" markdown="block">
(the guy who made [Linux](http://en.wikipedia.org/wiki/Linux))
</div>

What a nice person!
{:.fragment}
</section>

<section markdown="block">
## Github vs git

__GIT AND GITHUB ARE DIFFERENT!__ 

* __github is a website__ that hosts git repositories
* on it's own, __git is just version control__

</section>

<section markdown="block">
## Who Uses Git and Github?

Git is used to maintain a variety of projects, like:

* the [Processing IDE](https://github.com/processing/processing)
* or [Twitter's Bootstrap](https://github.com/twbs/bootstrap)
* or [Ruby on Rails](https://github.com/rails/rails)

Some people use github to distribute open source code... for example:

* __id software__ has a bunch of [stuff hosted on git](https://github.com/id-Software)
</section>

<section markdown="block">
## Local Version Control

[See the diagram on pro git](http://git-scm.com/book/en/Getting-Started-About-Version-Control)

* equivalent to what we may have done manually:
	* save files in folder with locally as a _snapshot_ of current state of code
	* recover by going through folders on computer
	* see versions by the timestamped folder name
* all of this is automated through software
	* stores changes to your files in a local database
	* an example of local version control is RCS

</section>

<section markdown="block">
## Centralized Version Control

[See the diagram on pro git](http://git-scm.com/book/en/Getting-Started-About-Version-Control)

* promoted collaboration; everyone got code from the same place
* single server that has all of the versioned files
* everyone working on it had a _working copy_, but not the full repository
* an example is subversion (SVN)

</section>

<section markdown="block">
## Distributed Version Control

[See the diagram on pro git](http://git-scm.com/book/en/Getting-Started-About-Version-Control)

* everyone has full repository
* can connect to multiple __remote__ repositories 
* can push and pull to individuals, not just _shared_ or _centralized_ servers
* single server that has all of the versioned files
* everyone working on it had a _working copy_

</section>

<section markdown="block">
# We're Using Git, a Distributed Version Control System

</section>

<section markdown="block">
# (But We're Really Going to Use a Central Repository Anyway)

</section>

<section markdown="block">
### A Quote...

From a co-worker of mine, a software engineer that builds web apps:

__"Git is the hardest thing we do here"__

(it's a little complicated, but not for what we're using it for)



</section>


<section markdown="block">
## Some Terminology

__repository__ - the place where your version control system stores the snapshots that you _save_

* think of it as the place where you store all previous/saved versions of your files
* this could be:
	* __local__ - on your computer
	* __remote__ - a copy of versions of your files on another computer
</section>

<section markdown="block">
## Some More Terminology

* __git__ - the distributed version control system that we're using
* __github__ - a website that can serve as a remote _repository_ for your project

__What's a remote repository again?__ &rarr;

<div class="fragment" markdown="block">
A copy of versions of your files on another computer/server
</div>
</section>

<section markdown="block">
## Where Are My Files

In your __local repository__, git _stores_ your files and versions of your files in a few different _conceptual_ places:

* __the working directory / working copy__ - stores the version of the files that you're currently modifying / working on
* __index__ - the staging area where you put stuff that you want to _save_ (or... that you're about to _commit_)
* __HEAD__ - the most recent saved version of your files (or... the last _commit_ that you made)
</section>

<section markdown="block">
## Another Way to Look at It

* __the working directory / working copy__ - stuff you've changed but haven't saved
* __index__ - stuff that you're about to save
* __HEAD__ - stuff that you've saved
</section>

<section markdown="block">
## Aaaaand.  More Terminology.

* __commit__ - save a snapshot of your work
* __diff__ - the line-by-line difference between two files or sets of files
</section>

<section markdown="block">
## Two Basic Workflows

1. Creating and setting up local and remote repositories
2. Making, saving, and _sharing_ changes
</section>

<section markdown="block">
## Creating Repositories

* create a local repository
* configure it to use your name and email (for tracking purposes)
* create a remote repository
* _link_ the two
</section>

<section markdown="block">
## Making, Saving, and Sharing Changes

* make changes
* put them aside so they can staged for saving / committing
* save / commit
* send changes from local repository to remote repository
</section>

<section markdown="block">
## Ok.  Great!

__Remind me again, what's github?__ &rarr;

* __github is a website__ 
* it can serve as a __remote git repository__
	* that means it can store all versions of your files
	* (after you've sent changes to it)
</section>

<section markdown="block">
# We can now submit assignments using the commandline

### (Um. Yay?)
</section>


<section markdown="block">

# Creating and Setting Up Repositories 

</section>


<section markdown="block">
## Commands for Creation and Set Up of Repositories

We'll be using this for most of our work...

* <code>git clone REPO_URL</code>

<br>
This stuff, not so much, but you should know them too...

* <code>git init</code>
* <code>git config ...</code>
	* <code>git config user.name  "__your user name__"</code>
	* <code>git config user.email __your@email.address__</code>
* <code>git remote add REMOTE_NAME REMOTE_URL</code>
</section>

<section markdown="block">
## git clone

Again, for most of our work, you'll just be cloning an existing repository (creating a local repository from a remote one).

<pre><code data-trim contenteditable>
git clone REPOSITORY_URL
</code></pre>

__REPOSITORY_URL__ is usually going to be something that you copy from github.
</section>

<section markdown="block">
## git init

__git init__ - creates a new _local_ repository (using the files in the existing directory)

* you can tell a repository is created by running ls -l ... it creates a .git directory
* again, this creates a new repository - a place to archive / save all versions of your files

<pre><code data-trim contenteditable>
# in the directory of your repository
git init
</code></pre>
</section>


<section markdown="block">
## git config

__git config__ - configure your user name and email for your commits 

* this has nothing to do with your computer's account or your account on github
* this information helps track changes

<pre><code data-trim contenteditable>
# in the directory of your repository
git config user.name  "foo bar baz"
git config user.email foo@bar.baz
</code></pre>
</section>


<section markdown="block">
## git remote add 

__git remote add__ - add a remote repository so that you can synchronize changes between it and your local repository

<pre><code data-trim contenteditable>
git remote add REPOSITORY_NAME REPOSITORY_URL
</code></pre>

</section>

<section markdown="block">
## Typical Workflow for Making Changes

1. make changes
2. git status (to see what changes there are)
3. git add --all (to stage your changes for committing)
4. git status (to see your staged changes)
5. git commit -m 'my message' (to save your changes)
6. git push origin master (optionally send/share your changes to a remote repository)

Check out a workflow chart here: [http://rogerdudler.github.io/git-guide/img/trees.png](http://rogerdudler.github.io/git-guide/img/trees.png)
</section>

<section markdown="block">
## git status

__git status__ - show what changes are ready to be committed as well as changes that you are working on in your working directory that haven't been staged yet

<pre><code data-trim contenteditable>
git status
</code></pre>
</section>

<section markdown="block">
## git add

__git add__ - mark a change to be staged

<pre><code data-trim contenteditable>
# in the directory of your repository

# add all
git add --all 

# add specific file
git add myfile.txt
</code></pre>
</section>

<section markdown="block">
## git commit

__git commit__ - take a snapshot of your work

<pre><code data-trim contenteditable>
# in the directory of your repository
# don't forget the commit message

git commit -m 'commit message goes here'
</code></pre>
</section>

<section markdown="block">
## git log

__git log__ - show commit history of your repository or file

<pre><code data-trim contenteditable>
# in the directory of your repository

git log

#you can also colorize the output:

git log --color
</code></pre>

</section>

<section markdown="block">
## git diff

__git diff__ - show the line-by-line differences between your last commit and your working directory

<pre><code data-trim contenteditable>
# in the directory of your repository
# use --color for syntax highlighting

git diff --color

</code></pre>
</section>

<section markdown="block">
## git reset

__git reset__ - revert last commit... or unstage changes

<pre><code data-trim contenteditable>
# unstage changes
git reset filename.txt

# revert last commit
git reset HEAD^

</code></pre>
</section>

<section markdown="block">
## git push

__git push__ - send your code to a remote repository

<pre><code data-trim contenteditable>
git push

# or to specify... push master branch to remote 
# repository called origin
git push origin master
</code></pre>
</section>

<section markdown="block">
# OK... how about getting all of this set up for our assignment?

</section>
{% comment %}
<section markdown="block">
## Getting Private Repositories

In order to set up private repositories with consistent names, we'll have to do a few things:

1. {:.fragment} __Send me your GitHub username [through this form](https://docs.google.com/a/nyu.edu/forms/d/1mgHQ2NupHDAlirAcbYjeSShFeAWNyPH1sqCqa7zTe2M/viewform)__ (not via email)
2. {:.fragment} I'll add you to the class _organization_ on GitHub (basically, a mechanism to group a bunch of users together)
3. {:.fragment} You'll receive an email to confirm that you're going to be part of the _organization_
4. {:.fragment} __Click on the link in the email...__
5. {:.fragment} I'll create a private repository for you
	* {:.fragment} (someone mentioned that you can actually create your own private repos in the organization)
	* {:.fragment} (but I'd rather create repositories for everyone to keep the names consistent)
    * {:.fragment} (also, depending on your client, you may need to init first instead of clone)
</section>

<section markdown="block">
## Speaking Of

### I have close to 90% of your github usernames

* that's great (!)
* but there are a few people that didn't send me their username (!?)

<br>

### For those of you that have your repository all set up...

* Your repository __should be private__; you're the only user that can see your repository (__let me know if isn't the case!__) &rarr;
* You should try pushing code to your repository to make sure that your git installation works fine...

</section>

<section markdown="block">
## Please Try Your Homework ASAP

Mainly because a lot of things could go wrong, and __I'll be more likely to be able to help you if you let me know about your issues early__. 

* {:.fragment} maybe you can't install __Node.js__ (for example, someone had permissions issues with homebrew and installation on OSX)
* {:.fragment} or you can't install a module...  :
* {:.fragment} or perhaps __git__ _doesn't work_

</section>
<section markdown="block">
## Don't Wait Last Minute!

This will help uncover the common problems in the previous slide.

* {:.fragment} install __Node.js__ and __NPM__ 
* {:.fragment} try installing __readline-sync__ with __NPM__
* {:.fragment} check that you can use git to push to your private repository

<br>
In fact, you don't have to wait for your private repository to be setup.
{:.fragment}

* {:.fragment} you can just start coding
* {:.fragment} ... when you have your private repo, clone it so that an entirely new directory is created (don't clone into your existing work)...
* {:.fragment} ... and add the files that you had been working on 
</section>
{% endcomment %}

<section markdown="block">
## Regarding Submission

Again, I will clone all of the repositories at the homework's deadline. __That means...__ &rarr;

* any commits made after the deadline...
* won't be seen by the graders

</section>
<section markdown="block">
## Details Again...

(they may not have made sense at the beginning of class... but maybe now?)

* if git clone doesn't work, use init workflow
* name the files and function exactly as specified
* commandline arguments: <code>process.argv[2]</code>

</section>

