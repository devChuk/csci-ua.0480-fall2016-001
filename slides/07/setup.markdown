---
layout: slides
title: "Project Setup"
---
<section markdown="block" class="intro-slide">
# {{ page.title }}

### {{ site.course_number}}-{{ site.course_section }}

<p><small></small></p>
</section>
<section markdown="block">
## Topics

* package.json
* --save and --save-dev
* linting
* .gitignore
</section>

<section markdown="block">
## package.json

__package.json__ is a file that contains metadata about your project. It tells <code>npm</code> 

* how to install your project
* how it's published (if it's a public project)
* how it's used as a module
* how to retrieve its dependencies
* etc.
<br>

<br>
There's a [very comprehensive page](https://www.npmjs.org/doc/files/package.json.html) on <code>package.json</code> on [npm's site](https://www.npmjs.org).
</section>

<section markdown="block">
## package.json Continued

### We're using it mainly for dependency management.

* a lot of the data in <code>package.json</code> is used for specifying how your code is imported into another file (what name to use, what the license is, etc.).
* we mostly care about the fact that it helps us download all of our project's dependencies along with their versions
* instead of installing dependencies manually, one-by-one, we can just <code>npm install .</code>

</section>
<section markdown="block">
## Required Fields in package.json

As the name implies, __package.json is _actually_ a json file__ (no surprise there). __How is that different from an object literal?__ &rarr; 

* {:.fragment}quote property names (they must be strings)
* {:.fragment}values can be a string, number, another object, an array, boolean or null

<br>
__There are two required properties__:

* the short, but descriptive __name__ of your project
* your project's __version__ in the format of MAJOR.MINOR.PATCH
</section>

<section markdown="block">
## Additional Fields in package.json

Some other data in <code>package.json</code> includes:

* __author__ - name of author
* __private__ - a _boolean_ specifying whether or not to publish publicly
* __dependencies__ - an object of all of the dependencies that your project has, along with their version numbers
* __devDependencies__ - and object of all of the dependencies that are necessary for _working on/developing_ your project (such as testing libraries, build tools)

</section>

<section markdown="block">
## Example package.json

__An example minimal <code>package.json</code>__:

<pre><code data-trim contenteditable>
{
	"name": "my-site",
	"version": "1.0.0",
	"author": "Joe Versoza",
	"private": true,
	"dependencies": {}
}
</code></pre>
</section>

<section markdown="block">
## Or You Can Generate ...

Of course, you don't have to write all of that by hand.  __You can use npm to create a new one for you!__  Just run:

<pre><code data-trim contenteditable>
npm init
</code></pre>

<br>
It'll ask you a bunch of questions.  

* the default answers are adequate for our purposes
* (so you can just press [ENTER] to go through it

__Let's give it a try__ &rarr;
</section>

<section markdown="block">
## Specifying Dependencies

You can manually specify dependencies. The __dependencies__ property is an object with:

* module names as keys 
* version specifiers as values

<br>
<pre><code data-trim contenteditable>
{
	"name": "foo",
	"version": "1.0.0",
	"dependencies": {
		"request": "^2.44.0"
	}
}
</code></pre>
</section>
<section markdown="block">
## Version Specifiers

A list of [example version specifiers is included npm's documentation](https://www.npmjs.org/doc/files/package.json.html#dependencies). __Some common ones include__:

* <code>version</code>- exact version
* <code>^version</code> - compatible with version
* <code>>version</code> - greater than version
* <code>1.2.x</code> - all 1.2.x versions
* <code>*</code> - matches any version

<!--* -->
</section>

<section markdown="block">
## Installing Packages

__How do I install the request module?__ &rarr;

<pre><code data-trim contenteditable>
npm install request
</code></pre>
{:.fragment}

We could then manually add that to our <code>package.json</code> if it's a dependency of our project... __just drop it into the <code>dependency</code> property.__
{:.fragment}


</section>

<section markdown="block">
## Installing Packages Continued

As we're developing, we may want to _keep track_ of all of the packages/modules that we've installed.

Using the <code>--save</code> flag will add it to your <code>package.json</code> automatically.

<pre><code data-trim contenteditable>
npm install request --save
</code></pre>

__Let's see how that works.__ &rarr;
</section>

<section markdown="block">
## Dependencies vs Dev Dependencies

There's also a <code>--save-dev</code> flag. This flag saves what you just installed to the devDependencies property.

* __dependencies__ - tracks required libraries and packages for you actual project to be installed and deployed
* __devDependencies__ - dependencies that are only necessary if you're __working on__ or __developing__ a project... some examples include:
	* unit testing library, like jasmine
	* linter, like JSHint
	* build tools, like grunt
	* etc.
</section>

<section markdown="block">
##  Speaking of Linters

A __linter__ is a program that performs static analysis on your code to determine if there are any suspicious or non-standard constructs. 

__Static analysis__ is the analysis of your program without actually executing it.

There are two commonly used linters in JavaScript:

* __JSLint__ - developed by Douglas Crockford
* __JSHint__ - community driven fork of JSLint

<br>
__We'll use JSHint.__

</section>
<section markdown="block">
## JSHint

__You can install JSHint through npm (of course).__

<pre><code data-trim contenteditable>
npm install jshint

# or globally
npm install jshint -g

# want to keep track of it in dev dependencies?
npm install jshint --save-dev
</code></pre>

[For integration with various editors, check out JSHint's install page](http://www.jshint.com/install/)

__For example, it works on the commandline, vim and [SublimeText 2 and 3](http://www.jshint.com/install/)__ &rarr;
</section>
