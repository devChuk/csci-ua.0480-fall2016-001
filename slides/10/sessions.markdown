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

__HTTP is a stateless protocol.__

* HTTP requests don't know anything about each other. 
* How might we maintain state or __share data between requests?__ &rarr;
    * {:.fragment} store data on the server
    * {:.fragment} __how about linking that data to the requests from a particular client?__
    * {:.fragment} cookies! - text files stored by your browser (Chrome actually stores cookies in a sqlite database, which is _essentailly_ just a text file)
    * {:.fragment} cookies can store client side data (though there are better ways to do this)
    * {:.fragment} ...and they can link to more data on the server
</section>

<section markdown="block">
## Um What? How Does That Work?

__Here's how state is maintained between requests.__ &rarr;

1. {:.fragment} your browser has a __cookie__ (tied to a domain that you've visited)
2. {:.fragment} it contains some identifier
3. {:.fragment} when your browser makes a request to the server, it sends along that identifier
4. {:.fragment} the server finds the __session__ associated with that identifier
5. {:.fragment} the __session store__ can be as simple as in-memory or file-based store... or it can be a database!
6. {:.fragment} you can store data for that user's sessions in the session store

</section>

<section markdown="block">
## Documentation

__First... check out the documentation on cookies__:

1. [Cookies on mdn](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies) 
2. [Cookies on nczonline](https://www.nczonline.net/blog/2009/05/05/http-cookies-explained/)
3. [Cookies and Security on nczonline](https://www.nczonline.net/blog/2009/05/12/cookies-and-security/)
</section>

<section markdown="block">
## Cookie Creation and "Persistence"

Cookies are stored by your browser, but __how do they get there?__ &rarr;

1. {:.fragment} the server __sets a header in an http response called `Set-Cookie`__ (multiple `Set-Cookie`'s in a single http response are allowed)
2. {:.fragment} this header instructs the __browser to create a cookie__
3. {:.fragment} `Set-Cookie` header values can specify (separated by `;`'s):
    * arbitrary __`name=value`__ pairs
    * __expiration__ / how long a cookie is valid for
    * various security options
4. {:.fragment} now, __every request that the browser makes to the domain that set the cookie will contain a `Cookie` header__ 
    * the value is all of the cookies that the browser has for that domain, separated by semicolons
    * (the original security options and expiration are not sent back)

</section>

<section markdown="block">
## Set-Cookie and Cookie examples

__An http response that sets a cookie.__ &rarr;

<pre><code data-trim contenteditable>
HTTP/1.0 200 OK
Content-type: text/html
Set-Cookie: foo=bar
Set-Cookie: session_id=5d6d473c-a370-44c8-bff0-4f28efd7c92a  
</code></pre>

__An http request that sends back cookies.__ &rarr;
<pre><code data-trim contenteditable>
GET / HTTP/1.1
Cookie: foo=bar; HttpOnly; Secure
</code></pre>

We'll discuss HttpOnly and secure in a moment
</section>

<section markdown="block">
## Session vs Permanent Cookies / Expiration

Cookies will be deleted when the client is shut down (for example, when you quit your browser). These cookies are called __session cookies__.

However, if you want cookies that last longer, you can create __permanent cookies__ that expire at a certain date or after a certain length of time. 

__You can do this by adding options to the `Set-Cookie` header__ &rarr;

* Expires (date)
    * `Set-Cookie: foo=bar; Expires=Thu, 29 Oct 2016 07:28:00 GMT;`
* Max-Age (number of seconds)
    * `Set-Cookie: foo=bar; Max-Age=300;`
    
<br>
__Note that a server cannot force the browser to delete cookies!__ ... but it can set an expiration date which will eventually cause the cookie to be deleted.

</section>

<section markdown="block">
## Important Security Options

* {:.fragment} `Domain` - cookies sent will only be valid for this domain (default is current domain)
* {:.fragment} `Path` - cookies sent will only be valid for this path (default is all paths)
* {:.fragment} `HttpOnly` - only allow reading of cookies via http, __don't allow JavaScript!!!!__ ...__why do this?__ &rarr; <span class="fragment">3rd party JavaScript included on your page is allowed to read cookies for that domain!?</span>
* {:.fragment} `Secure` - cookies will only be sent if the request is encrypted (using SSL/HTTPS)

<br>
Out of these, definitely use `HttpOnly` and `Secure`... though for most of class, we'll be omitting `Secure` until we get to SSL/HTTPS.
</section>



<section markdown="block">
## Sessions

Sessions allow you to:

* store data on a _per-session_ basis
* by maintaining a small piece of data on the client (via cookies)
* that matches with data on the server
* that means... different clients will have different sessions (and consequently different state)
</section>

<section markdown="block">
## Creating Your Own Session Management

__You can create your own session management by creating custom middleware that:__ &rarr;

1. {:.fragment} has an in-memory store of session ids (read: global variable)
2. {:.fragment} checks every request for a `Cookie` header 
3. {:.fragment} if there is no `Cookie` header that contains a session id, it'll generate a session id (using the `crypto` module)
4. {:.fragment} sends back the `Set-Cookie` header with that id
5. {:.fragment} if there is a `Cookie` header with a session id, it'll:
    * search for that id in the session store
    * retrieve that data
    * add it as a property on the request object so that it can be accessed programmatically

{:.fragment}
</section>

<section markdown="block">
## Creating Your Own Session Management Continued

__You'll try writing this session management middleware for your homework by using__ &rarr;

* `req.get` - to retrieve the `Cookie` header
* parsing that header into name/value pairs
* the `crypto` module to create a session id
* `res.set` - to set the `Set-Cookie` header

<br>
Of course, as you might expect, session middleware already exists...

</section>
<section markdown="block">
## Using Sessions Middleware

__For "production", instead of using a custom solution , you can use the `express-session` module!__ &rarr;


__Boilerplate setup.__
<pre><code data-trim contenteditable>
var express = require('express');
var bodyParser = require('body-parser');
</code></pre>

__Include the express-session module...__

<pre><code data-trim contenteditable>
var session = require('express-session');
</code></pre>

<pre><code data-trim contenteditable>
var app = express();

app.set('view engine', 'hbs');
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
{:.fragment}

</section>

<section markdown="block">
## Shutting Down the Server

__What will happen if we restart the server? Will the session data still be present?__ &rarr;

We're using an in-memory session store, so, the session data will not be persisted.
{:.fragment}

</section>

