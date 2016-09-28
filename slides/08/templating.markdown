---
layout: slides
title: "Templating with HandleBars"
---
<section markdown="block" class="intro-slide">
# {{ page.title }}

### {{ site.course_number}}-{{ site.course_section }}

<p><small></small></p>
</section>


<section markdown="block">
## Templating

__What's a templating engine? Describe what it does...__ &rarr;

A templating engine is software/an application/a library that:
{:.fragment}

* merges one or more templates (a document with placeholders)
* ... with data
* ... to create a single complete document
* templating engines are usually built so that they are decoupled from the rest of the application that is using it
{:.fragment}
</section>

<section markdown="block">
## Templating Continued

In our case, we're using a templating library called __handlebars__:

* ...so that we could dynamically generate web pages 
* ...by combining variables/data with html layouts and templates.

<br>
__Why bother using templating? We were able to emit html directly by using <code>res.end("<html>...") or res.send("<html>...")</code>?__
</section>
<section markdown="block">
## Why Use Templating

__Using a templating engine that's decoupled from your application's logic is useful because:__ &rarr;

* constructing html manually is easy for small pages, but quickly gets complicated as more markup gets written
* it can be much more difficult to spot malformed html!
* changes to your applications logic can be tangled with changes to your presentation (and vice versa), having some separation gives you at least a chance of avoiding unwanted side-effects
* having separate templates allows isolation of work... a designer or front-end developer can work on the templates, while a backend developer can work on the application logic
* most templating engines are featureful... providing conveniences such as automatic character escaping (why is this important? __Let's check out a _bad_ form.__)
</section>


<section markdown="block">
## Installation

Following the book? (thumbs up!)

<pre><code data-trim contenteditable>
npm install express-handlebars --save
</code></pre>

However... the official __express generator__ (cli app that creates some scaffolding for you) uses another module:

<pre><code data-trim contenteditable>
npm install hbs --save
</code></pre>
</section>
<section markdown="block">
## Setup

Using hbs:

<pre><code data-trim contenteditable>
// super simple
app.set('view engine', 'hbs');
</code></pre>

Using Express handlebars

<pre><code data-trim contenteditable>
var handlebars = require('express-handlebars')
	.create({defaultLayout:'main'});

app.engine('handlebars', handlebars.engine);
app.set('view engine', 'handlebars');
</code></pre>
</section>

<section markdown="block">
## Locations and Names

__Note that the directory structure is slightly different depending on what library to use.__ &rarr;

hbs

<pre><code data-trim contenteditable>
views/layout.hbs
views/index.hbs
</code></pre>

express-handlebars

<pre><code data-trim contenteditable>
views/layout/main.handlebars
views/index.hbs
</code></pre>

</section>
<section markdown="block">
## Context Objects

When a template is rendered using <code>res.render</code>, there are two arguments. __What are they?__ &rarr;

The _view_ or _template_ to render... and the _context object_.
{:.fragment}

The __context object__'s properties are available to your template as variable names!
{:.fragment}

<pre><code data-trim contenteditable>
// the second argument is an object
res.render('myview', {'item':'pizza', 'description':'tasty'});
</code></pre>
{:.fragment}

<pre><code data-trim contenteditable>
<h3>{{"{{ description "}}}} {{"{{ item "}}}}</h3>
</code></pre>
{:.fragment}

<pre><code data-trim contenteditable>
<h3>tasty pizza</h3>
</code></pre>
{:.fragment}
</section>

<section markdown="block">
## Ok... Variables Make Sense

Just use double curly braces (no spaces) to drop a value in your context into your template!

__But what about _some_ logic. What if the value that I'm passing in is an array or object?__ &rarr;

Handlebars actually has some basic facilities for:

* looping
* conditionals
* ...and other structures
</section>

<section markdown="block">
## Block Expressions / Helpers

"Block expressions allow you to define helpers that will invoke a section of your template with a different context than the current"


* {:.fragment} ...errr ... basically, that means __you'll be able to add control structures to your templates, like conditionals and iteration.__
* {:.fragment} use double curly braces, but prefix your helper with a hash... and make sure you close it at the end
* {:.fragment}<code>{{"{{#helper"}}}}stuff{{"{{/helper"}}}}</code>
* {:.fragment} notice that there are no spaces!
</section>

<section markdown="block">
## Arrays Example

The <code>#each</code> helper:

* allows you iterate over a series of items
* within <code>#each</code>, you can use <code>{{"{{this"}}}}</code> or <code>{{"{{."}}}}</code> to output the current item

<br>
__In your application:__ &rarr;

<pre><code data-trim contenteditable>
app.get('/templating-arrays', function(req, res) {
	res.render('templating-arrays', {'luckyNumbers':[42, 7, 78, 3, 5]});
});
</code></pre>

__In your view:__ &rarr;

<pre><code data-trim contenteditable>
&lt;ul&gt;
{{"{{#each luckyNumbers"}}}}
	&lt;li&gt;{{"{{this"}}}}&lt;/li&gt;
{{"{{/each"}}}}
&lt;/ul&gt;
</code></pre>

</section>

<section markdown="block">
## Also Works With Objects

__In your application:__ &rarr;

<pre><code data-trim contenteditable>
app.get('/templating-objects', function(req, res) {
	res.render('templating-objects', {'obj':{'topping':'mushroom', 'size':'medium'}});
});
</code></pre>

__In your view:__ &rarr;

<pre><code data-trim contenteditable>
&lt;ul&gt;
{{"{{#each obj"}}}}
	&lt;li&gt;{{"{{this"}}}}&lt;/li&gt;
{{"{{/each"}}}}
&lt;/ul&gt;
</code></pre>
</section>

<section markdown="block">
## Conditionals

(Note - no equality operators 4 U) ...

<pre><code data-trim contenteditable>
{{"{{#if isActive"}}}}
  <img src="star.gif" alt="Active">
{{"{{else"}}}}
  <img src="cry.gif" alt="Inactive">
{{"{{/if"}}}}
</code></pre>
</section>

<section markdown="block">
## Template Error?

By the way, if you see something like:

<pre><code data-trim contenteditable>
Expecting 'ID', 'DATA', got 'INVALID'
</code></pre>

You probably have spaces in your <code>{{ #helper }}</code>.
</section>

<section markdown="block">
## More Haaalp Plese!

[Check out the docs for more block helpers!](http://handlebarsjs.com/block_helpers.html)
</section>

<!--
<section markdown="block">
## Oh Yes...

__We can use express-handlebars now (you might have noticed that the express3-handlebars version is deprecated)__ &rarr;
<pre><code data-trim contenteditable>
npm install express-handlebars
</code></pre>

<pre><code data-trim contenteditable>
var handlebars = require('express-handlebars')
	.create({defaultLayout:'main'});
</code></pre>
</section>
-->
