---
layout: slides
title: "Managing Callbacks / Promises"
---
<section markdown="block" class="intro-slide">
# {{ page.title }}

### {{ site.course_number}}-{{ site.course_section }}

<p><small></small></p>
</section>

<section markdown="block">
## Asynchronous Tasks and Dependencies

Initially, dealing with asynchronous tasks is a bit tricky. A lot of times, you want to run code only __after a task__ completes.

__If you have a task, and you don't know it will finish, how do you ensure that it's done before running other code that's dependent on it?__ &rarr;

* {:.fragment} we've seen a common pattern for tasks that are asynchronous; they expect a callback
* {:.fragment} the callback is fired when the task is finished
* {:.fragment} so we put any code that's dependent on that task within the callback

</section>

<section markdown="block">
## An Example

Assuming we have a function called __get__ that retrieves a url... __we tend to want to do this__ &rarr;

<pre><code data-trim contenteditable>
var data = get(url);
parseResult(data);
</code></pre>

__But if our get is asynchronous, we can't guarantee that get finishes before parseResult is called__ (so callback functions it is) &rarr;

<pre><code data-trim contenteditable>
get(url, function(data) {
  parseResult(data);
});
</code></pre>

</section>
<section markdown="block">
## No Big Deal

Ok. We get asynchronous tasks... and we understand that:

* if code depends on an async task
* put in async task's callback
* and it'll get executed when the task is finished

</section>

<section markdown="block">
## Async Tasks All the Way Down

So... what happens if we have async tasks that are dependent on other async tasks? For example: 

* retrieving a url results in a second url
* which also has to be retrieved... 
* and maybe, in turn, the second url produces a third!

<br>
Let's assume that we have our get function:

* it takes two arguments, a url and a callback
* and the callback has a single parameter, the response data from the request

<br>
__Using our imaginary get function, what would this look like?__ &rarr;

A tiny pyramid. ▲ ▲ ▲ ▲ ▲
{:.fragment}



</section>
<section markdown="block">
## A Tiny Pyramid Made of HTTP Requests

We use a bunch of nested callbacks... (the pyramid is the white space to the left).

<pre><code data-trim contenteditable>
get(url, function(data) {
  var urlTwo = parseResult(data);
  get(urlTwo, function(data) {
    var urlThree = parseResult(data);
    get(urlThree, function(data) {
      console.log("Aaaand we're done");
    });
  });
});
</code></pre>
</section>

<section markdown="block">
## Let's Actually Try This

We'll create a bunch of json files that each have an object with a url property holding the url of another json file. We'll then retrieve these files one by one...

1. Create an express app to serve up our files...
2. Create a bunch of json files in a directory called <code>data</code> within <code>public</code>
	* <code>tango.json</code>: <code>{ "url":"http://localhost:3000/data/uniform.json" }</code>
	* <code>uniform.json</code>: <code>{ "url":"http://localhost:3000/data/victor.json" }</code>
	* <code>victor.json</code>: <code>{}</code>
3. Create a page that uses external JavaScript that...
4. Uses XMLHttpRequest to pull retrieve <code>tango.json</code>
5. Extract the url, and retrieve it... and do the same for the third url...

</section>

<section markdown="block">
## This is Going to be Ugly

Oh hello scrollbars. This won't even fit on this slide.

<pre><code data-trim contenteditable>
var url = 'http://localhost:3000/data/tango.json';
req1 = new XMLHttpRequest();
req1.open('GET', url, true);
req1.addEventListener('load', function() {
  console.log('loading req1');
  if(req1.status >= 200 && req1.status < 400) {
    console.log(req1.responseText);
    var data1 = JSON.parse(req1.responseText) 
    console.log(data1.url);
    req2 = new XMLHttpRequest();
    req2.open('GET', data1.url, true);
    req2.addEventListener('load', function() {
      console.log('loading req2');
      if(req2.status >= 200 && req2.status < 400) {
        console.log(req2.responseText);
        var data2 = JSON.parse(req2.responseText) 
        console.log(data2.url);
        req3 = new XMLHttpRequest();
        req3.open('GET', data2.url, true);
        req3.addEventListener('load', function() {
          console.log('loading req3');
          if(req3.status >= 200 && req3.status < 400) {
            console.log(req3.responseText);
            console.log('done');  
          }
        });
        req3.send();
      }
    });
    req2.send();
  }
});
req1.send();
</code></pre>
</section>


<section markdown="block">
## Obviously, That Was Terrible

Oof. Apologies for making your eyes bleed. 

* So much nesting. 
* Such repetion! 
* __What can we do to tame this a bit?__ &rarr;
	* {:.fragment} hey... maybe stop using so many anonymous functions (start naming _those things_)
	* {:.fragment} and/or wrap up URL retrieval and data extraction into separate functions
	* {:.fragment} <code>get(url, cb)</code>
	* {:.fragment} <code>extractURL(json)</code>
</section>

<section markdown="block">
## get

So... this function will retrieve a url, and when it gets a response, it'll call the callback with the response text.

<pre><code data-trim contenteditable>
function get(url, cb) {
  console.log('getting ', url);
  req = new XMLHttpRequest();
  req.open('GET', url, true);
  req.addEventListener('load', function() {
    console.log('loading req');
    if(req.status >= 200 && req.status < 400) {
      console.log(req.responseText);
      cb(req.responseText);
    }
  });
  req.send();
}
</code></pre>
{:.fragment}
</section>

<section markdown="block">
## extractURL...

This one's simple
<pre><code data-trim contenteditable>
function extractURL(json) {
  var data = JSON.parse(json) 
  console.log(data.url);
  return data.url;
}
</code></pre>
</section>

<section markdown="block">
## Voila (Using get and extractURL)

Ah. Much nicer. 

<pre><code data-trim contenteditable>
var url = 'http://localhost:3000/data/tango.json';

get(url, function(responseText) {
  var url2 = extractURL(responseText); 
  get(url2, function(responseText) {
    var url3 = extractURL(responseText); 
    get(url3, function(responseText) {
      console.log('done'); 
    });
  });
});
</code></pre>

We still get a tiny pyramid, though. To get around that, we can:

* for this example only (since it's doing the same thing for each), encapsulate each level of nesting in a single function
* use _Promises_
</section>
<section markdown="block">
## One More Function

Getting and extracting were repeated 3 times. Why don't we just __wrap this in another function__? &rarr;

(this only works because we're doing the __same exact thing__ in each level of callback nesting).
{:.fragment}

<pre><code data-trim contenteditable>
function getAndExtract(url) {
  get(url, function(responseText) {
    var url = extractURL(responseText); 
    if(url) {
      getAndExtract(url);
    } else {
      console.log('done'); 
    }
  });
}
getAndExtract(url);
</code></pre>
{:.fragment}

</section>

<section markdown="block">
## Promises

So, an alternate way to deal with this is to __use an API that allows to code as if we were dealing with simple, sequential operations__.

One of these APIs, __Promise__, is in ES6 and is actually [already available on a lot of browsers](http://caniuse.com/#feat=promises)

A __Promise__ is an object that __represents an asynchronous action__ - some operation that hasn't completed yet.

For example, a Promise may represent:

* retrieving data from a database can be a promise
* writing to a file
* making an http request
* etc.
</section>

<section markdown="block">
## Creating a Promise

To create a <code>Promise</code> use the <code>Promise</code> constructor: 

* which takes one argument (a callback function, of course)
* this callback function is going to some asynchronous stuff
* it has a couple of parameters passed to it:
	* a function to call if the task succeeded
	* a function to call if the task failed
	* both of these functions take a value as an argument 

<pre><code data-trim contenteditable>
// note that you can call the parameters anything you want
// resolve, reject ... whatever
var p = new Promise(function(succeed, fail) {
  // do something async
  if(goodStuffHappened) {
	succeed('Success!');
  } else {
	fail('Failure!');
  }
});
</code></pre>
</section>

<section markdown="block">
## Then and Success

So... Promises also have a method called <code>then</code>. 

* __then__ represents the next step to execute
* it accepts a callback as its first parameter... the thing to do if our Promise was _resolved_ or _successful_
* this callback has also has a single parameter...
	* then execute its callback with
	* the value that was passed into the succeed function call in the original Promise

<pre><code data-trim contenteditable>
var p = new Promise(function(succeed, fail) {
  succeed('Success!');
});
p.then(function(val) {
  console.log(val);
})
</code></pre>
</section>

<section markdown="block">
## Then Continued

The result of calling <code>then</code> is always another Promise. However, what happens with that Promise depends on what then's callback returns.

* if it returns a Promise, the result is a Promise that could succeed or fail depending on the async action
* if the callback's return is not a Promise, then the result is yet another Promise that immediately succeeds (and consequently the next then's callback is called with the result of the previous then's callback's return. whew!) 

<pre><code data-trim contenteditable>
var p = new Promise(function(succeed, fail) {
  succeed('Success!');
});
var anotherPromise = p.then(function(val) {
  console.log(val);
  return 'More success to you!';
});
anotherPromise.then(function(val) {
  console.log(val);
});
</code></pre>
</section>

<section markdown="block">
## Promises with Our Example

So maybe our version of get will now just give back a Promise to wrap the async code.
<pre><code data-trim contenteditable>
function get(url) {
  return new Promise(function(succeed, fail) { 
    console.log('getting ', url);
    req = new XMLHttpRequest();
    req.open('GET', url, true);
    req.addEventListener('load', function() {
      console.log('loading req');
      if(req.status >= 200 && req.status < 400) {
        console.log(req.responseText);
        succeed(req.responseText);
      }
    });
    req.send();
  });
}
</code></pre>
</section>

<section markdown="block">
## Keeping Our Extract Function...

<pre><code data-trim contenteditable>
function extractURL(json) {
  var data = JSON.parse(json) 
  console.log(data.url);
  return data.url;
}
</code></pre>
</section>

<section markdown="block">
## We Can Make Async Look Sequential

<pre><code data-trim contenteditable>
var url = 'http://localhost:3000/data/tango.json';

get(url)
  .then(extractURL)
  .then(get)
  .then(extractURL)
  .then(get)
  .then(extractURL)
  .then(function(val){
    console.log(val);
    console.log('done');
  });
</code></pre>
</section>

<section markdown="block">
## Things We Pretended Didn't Exist

So, promises are kind of complicated, but in the end, they do simplify things. Some things that we didn't cover that further show the power of using the Promise API are:

* error handling
* having code trigger only when to async tasks are finished (rather than just one)
</section>
