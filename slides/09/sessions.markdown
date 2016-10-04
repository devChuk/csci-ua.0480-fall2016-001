---
layout: slides
title: "Sessions"
---
<section markdown="block" class="intro-slide">
# {{ page.title }}

### {{ site.course_number}}-{{ site.course_section }}

<p><small></small></p>
</section>

<section markdown="block" class="intro-slide">
## Maintaining State Between Requests

On their own, HTTP requests don't know anything about each other. HTTP is a stateless protocol. __How might we maintain state or  share data between requests?__ &rarr;

* {:.fragment} store data on the server
* {:.fragment} __how about linking that data to the requests from a particular client?__
* {:.fragment} cookies! - text files stored by your browser (Chrome actually stores cookis in a sqllite database, which is _essentailly_ just a text file)
* {:.fragment} cookies can store client side data (though there are better ways to do this)
* {:.fragment} ...and they can link to more data on the server
</section>

<section markdown="block">
## Um What? How Does That Work?


1. your browser has a cookie (tied to a domain that you've visited)
2. it contains some identifier
3. when your browser makes a request to the server, it sends along that identifier
4. the server finds the session associated with that identifier
5. the session store can be as simple as in-memory or file-based store... or it can be a database!
6. you can store data for that user's sessions in the session store

</section>

<section markdown="block">
## Sessions

Sessions allow you to:

* store data on a _per-session_ basis
* by maintaining a small piece of data on the client (via cookies)
* that matches with data on the server
* that means... different clients will have different sessions (and consequently different state)
* you can install session management for express by <code>npm install express-session --save</code>
</section>

<section markdown="block">
## Using Sessions

__Boilerplate setup.__
<pre><code data-trim contenteditable>
var express = require('express');
var handlebars = require('express-handlebars').create({'defaultLayout':'main'});
var bodyParser = require('body-parser');
</code></pre>

__Include the express-session module...__

<pre><code data-trim contenteditable>
var session = require('express-session');
</code></pre>

<pre><code data-trim contenteditable>
var app = express();

app.engine('handlebars', handlebars.engine);
app.set('view engine', 'handlebars');
app.use(bodyParser.urlencoded({ extended: false }));

</code></pre>

</section>

<section markdown="block">
## Storing Data in Your Session 

__Set up some session options (the secret should really be externalized and not in version control, but we'll keep it here for convenience).__

<pre><code data-trim contenteditable>
var sessionOptions = { 
	secret: 'secret for signing session id', 
	saveUninitialized: false, 
	resave: false 
};
app.use(session(sessionOptions));
</code></pre>


</section>

<section markdown="block">
## What's the Deal With These Options

Check out the [docs for details on all of the options](https://github.com/expressjs/session). The ones that we set explicitly are:

* secret - used to sign session the session id cookie to prevent tampring (and possibly to ensure length/complexity to make _unguessable_)
* saveUnitialized: false - don't save new empty session (to preserve space)
* resave: false - prevents session data from being resaved if session data is unmodified

Some others interesting ones that we don't explicitly set:

* store - where session data is stored, defaults to in memory storage
* genid - function that generates session id 

</section>
<section markdown="block">
## Saving Data in a Session

__Let's create a simple-form that:__ &rarr;

* allows a user to submit their name using a form
* the form page will have a heading that consists of the user's submitted name (so, before submitting data, the name will be blank, but afterwards, it will display the submitted data)
* the form is at /
* the form will post to itself (the same url that the form is on)
* the name submitted will be stored in the session
* it will redirect back to the form

</section>

<section markdown="block">
## Routes

__Our usual routes, but note the use of <code>req.session.</code>__

<pre><code data-trim contenteditable>
app.get('/', function(req, res) {
	res.render('simple-form', {'myName':req.session.myName});
});

app.post('/', function(req, res) {
	console.log(req.body);
	req.session.myName = req.body.myName;
	res.redirect('/');
});

app.listen(3000);
</code></pre>
{:.fragment}
</section>

<section markdown="block">
## And, In the Template

<pre><code data-trim contenteditable>
<h1>myName: {{myName}}</h1>

<form method="POST" action="">
my name: <input name="firstName" type="text">
<input type="submit">section
</form>
</code></pre>

</section>


<section markdown="block">

## Try Entering Data With Two Different Browsers Or Incognito Mode

__What do you think will happen?__ &rarr;

Session data will be unique to each browser session (so you can have foo for one name and bar for another name if you're using two different browsers)
{:.fragment}

</section>

<section markdown="block">
## Let's Prove That There's Some Data Stored on the Client Side

1. chrome://settings/cookies
2. find localhost:3000
3. check out the values of names __connect.sid__ and __session__ - both of those together identify the session

</section>

<section markdown="block">
## Copying Cookie Data, Stealing Sessions!

__What do you think will happen if we request the page with curl? Will the name be there?__ &rarr;

<pre><code data-trim contenteditable>
curl localhost:3000 
</code></pre>
{:.fragment}

Nooope... no info to identify the session, so name isn't there.
{:.fragment}

We can actually use curl to send cookies by using the --cookie flag. Let's copy over the cookie data...
{:.fragment}

<pre><code data-trim contenteditable>
curl localhost:3000 -v --cookie "session=..." --cookie "connect.sid=..."
</code></pre>

</section>

<section markdown="block">
## Shutting Down the Server

__What will happen if we restart the server? Will the session data still be present?__ &rarr;

We're using an in-memory session store, so, the session data will not be persisted.
{:.fragment}

</section>

