---
layout: slides
title: ""
---
<section markdown="block">
## Where We Left Off

We last looked at a small react example that __used events and state to:__ &rarr;


* render an element with number in it
* the number starts at 0
* every time you click on the element, the number increases

<br />

<div markdown="block" class="img">
![number click](../../resources/img/number-click.gif)
</div>


</section>


<section markdown="block">
## A Solution

Start off with some boiler plate...

<pre><code data-trim contenteditable>
var MyComponent = React.createClass({
	// render, getInitialState and event handler
	// goes here...
});
</code></pre>

<pre><code data-trim contenteditable>
React.render(
	<MyComponent &#47;>, 
	document.body
);
</code></pre>

</section>

<section markdown="block">
## A Solution (Continued)

Within your component definition:


<pre><code data-trim contenteditable>
  getInitialState: function() {
    return {
      count: 0,
    }
  }
</code></pre>

<pre><code data-trim contenteditable>
  handleClick: function() {
    this.setState({count: this.state.count + 1});
  }
</code></pre>

<pre><code data-trim contenteditable>
  render: function() {
    return (
      <div className="number" 
	  onClick={this.handleClick}>{this.state.count}<&#47;div>
    )
  }
</code></pre>
</section>

<section markdown="block">
## Prop vs State?

__What's the difference between <code>props</code> and <code>state</code>?__ &rarr;

* {:.fragment} props are controlled _externally_ by whatever is rendering your component (via attributes in JSX!)
* {:.fragment} state is managed by the component itself

</section>

<section markdown="block">
## Integrating with Express (Out of Codepen / JSbin)

Sooo... how do you think we could serve up our small React client side examples through Express? __What would we minimally need to do to even start a React app?__ &rarr;

* {:.fragment} include the react library in our client side code
* {:.fragment} _serve_ any of the components that we create in...
</section>

<section markdown="block">
## Integrating With Express Continued

This would normally be a straightforward task:

1. just serve up our client side JavaScript from the public/javascripts directory using express static
2. do the same with the react library... 
	* download it or use some sort of client side package manager like bower... and, again, serve it from our public/javascripts folder 
	* or just include the file from someone else that's hosting it (like a [content delivery network / cdn](https://cdnjs.com/libraries/react)... [or from facebook](https://facebook.github.io/react/downloads.html))
</section>

<section markdown="block">
## Some Setup

1. create an Express app
2. create an html file in <code>public</code>
3. include the appropriate scripts... using the examples for setting up a dev version of a react app [from facebook](https://facebook.github.io/react/downloads.html):

<pre><code data-trim contenteditable>
<script src="https://fb.me/react-0.14.3.js"></script>
<script src="https://fb.me/react-dom-0.14.3.js"></script>
</code></pre>


</section>

<section markdown="block">
## Create and Mount a Test Component

Markup

<pre><code data-trim contenteditable>
&#x9;&#x3C;div id=&#x22;app&#x22;&#x3E; &#x3C;/div&#x3E;
</code></pre>

Component

<pre><code data-trim contenteditable>
var MyComponent = React.createClass({
&#x9;render: function() {
&#x9;&#x9;return React.createElement(&#x27;h1&#x27;, {}, &#x27;hello&#x27;);
&#x9;}
});

ReactDOM.render(
&#x9;React.createElement(MyComponent),
&#x9;document.getElementById(&#x27;app&#x27;)
);
</code></pre>

</section>

<section markdown="block">
## Easy, Right?

Hey... wait, I thought we used JSX with react. Let's change our code...

<pre><code data-trim contenteditable>
var MyComponent = React.createClass({
&#x9;render: function() {
&#x9;&#x9;return (
&#x9;&#x9;&#x9;&#x3C;h1&#x3E;Hello&#x3C;/h1&#x3E;
&#x9;&#x9;);
&#x9;}
});

ReactDOM.render(
&#x9;&#x3C;MyComponent /&#x3E;
&#x9;document.getElementById(&#x27;app&#x27;)
);
</code></pre>

__Let's see what happens__ &rarr;
</section>

<section markdown="block">
## Sad Face

<pre><code data-trim contenteditable>
Uncaught SyntaxError: Unexpected token &lt;
</code></pre>

This _is_ valid JSX, right? It works on Codepen... __so what do you think happened here__? &rarr;

* {:.fragment} Hmmm... looks like our browser doesn't understand JSX!
* {:.fragment} We need to somehow compile it...
</section>

<section markdown="block">
## To the Docs!

[Facebook says that we can do an in browser transform](https://facebook.github.io/react/docs/tooling-integration.html#in-browser-jsx-transform)... 

* our client side JavaScript will include a JSX transformer script / library
* which means that compilation of JSX into JavaScript will happen in the user's browser
* so... it seems like we just include this __babel library__ thing, and magically, JSX will work in our browser, right?

<div markdown="block" class="img">
![jsx](../../resources/img/jsx.png)
</div>
</section>

<section markdown="block">
## babel?

[Babel](https://babeljs.io/) is a set of tools that allows you to use __new JavaScript__ syntax, now, __even if browsers don't support it yet__!!! YES. THE FUTURE IS NOW!

* it transform new syntax from ES6/ES2015 so that you can write ES6 without having to wait for full browser support
* ...and, of course, it'll compile JSX to plain JavaScript
</section>

<section markdown="block">
## Ok. Sign Me Up! Buuuuut....

Clicking on the link from the [facebook docs](https://facebook.github.io/react/docs/tooling-integration.html#in-browser-jsx-transform), takes us to this [page on babel's site](http://babeljs.io/docs/usage/browser/).

<div markdown="block" class="img">
![babel](../../resources/img/babel.png)
</div>

Looks like babel removed support for in-browser transforms. __Why?__ &rarr;

* {:.fragment} there was note in the facebook docs that said "The in-browser JSX transformer is fairly large and results in extraneous computation client-side that can be avoided - do not use it in production"
* {:.fragment} why transform on the client for every user (slowing down the user experience), when we can just transform once on the server by precompiling before deploy!?
</section>

<section markdown="block">
## Ohhh Kaaay. So Now What?


So here's where things get kind of complicated. We'll need:

* __babel__ to do the processing
* __and some tool to find the files that need processing__, and apply the transformation to those files
* __why do you think we need a separate tool for this - why can't we just compile our one js file and call it a day?__ &rarr;
* {:.fragment} your file may have dependencies which may also need to be compiled
* {:.fragment} dependencies can get complicated really fast, even for small projects

</section>

<section markdown="block">
## Enter Webpack

This is where __webpack__ comes in. Webpack will do two things for us:

1. it'll investigate our app using some __entry point__ JavaScript file that we specify... and figure out all of that file's dependencies
	* for example, we may have a client side JavaScript file, client.js, that depends on React and socket.io
2. then, it'll use additional libraries (__loaders__ to __run some processes__) based on the extension of each dependency
	* for example, for all of our js files, it may compile any jsx syntax into regular JavaScript
	* there's a __loader__ that integrates __babel__ into the webpack workflow

<br />
Note that there's some intersection in functionality among __webpack__, __grunt__, and __gulp__ (for example, they can all be used to concatenate files)
</section>


<section markdown="block">
## webpack vs grunt and gulp

So __grunt__ and __gulp__ are build tools. That is, you specify some tasks, and they'll run them automatically. For example, you want your build tools to:

* minify your client side JavaScript and CSS
* concatenate all of those files
* even run your JSX transformations!

<br>

And... this will all be done without having to run separate commands. However, grunt and gulp are focused on files, while webpack is focused on a _project_. __What does that exactly mean...__ &rarr; 
</section>


<section markdown="block">
## Webpack and Dependencies

__Webpack requires that you specify a single entry point to your application.__

* typically, this is a single js file like _main.js_ or _client.js_
* then, it'll figure out what that file depends on by <code>require</code> statements (or import statements for ES6 syntax!)... 
* this will also work for assets like urls in CSS or hrefs in image tags!

<br>
It'll take all of these dependencies and output them as static assets based on your configuration.

</section>
<section markdown="block">
## Setting up Express with Webpack

Assuming we have a barebones Express app, the [webpack docs]() recommend installing it both globally and locally in your project:

Globally (since you'll likely use for multiple projects)

<pre><code data-trim contenteditable>
npm install webpack -g
</code></pre>

Then... install webpack as a development dependency in case you want to use a _specific_ version.

<pre><code data-trim contenteditable>
npm install webpack --save-dev
</code></pre>

This gives us a commandline tool, __webpack__ (of course), that we'll use to bundle our JavaScript...

<pre><code data-trim contenteditable>
webpack --help
</code></pre>
</section>

<section markdown="block">
## babel and webpack

Of course, we'll also need to integrate babel into the mix - that was the whole point. We'll want to install the following libraries:

* {:.fragment} <code>babel-core</code> -  babel itself
* {:.fragment} <code>babel-loader</code> - webpack plugin for babel
* {:.fragment} [<code>babel-preset-react</code>](https://babeljs.io/docs/plugins/preset-react/) - a bunch of plugins for babel that are useful for react apps (most notably, JSX transform)

<br>
Install all of these with <code>--save-dev</code> since they're for building our project (not actual libraries that the app depends on).
{:.fragment}
</section>

<section markdown="block">
## webpack Everything

Since we're using webpack (which relies on <code>require</code>s), we'll download local versions of React rather than relying on a hosted / CDN version.

We can install with npm... remember to use <code>--save</code>)

<pre><code data-trim contenteditable>
react
react-dom
</code></pre>

</section>

<section markdown="block">
## Setting up our App for webpack

We'll also have to modify where we place our code so that we can use webpack.

1. Move our code to external JavaScript 
2. Perhaps separate out our component, and the initial mounting of the component
3. Designate one of those files as the entry point...
4. Add requires
</section>

<section markdown="block">
## Our Bare Bones Page

So... let's get rid of that inline JavaScript... __and assume that we'll have a single js file available to us once webpack is done generating assets for us.__ &rarr;

<pre><code data-trim contenteditable>
&#x3C;div id=&#x22;app&#x22;&#x3E;
&#x3C;/div&#x3E;
&#x3C;script src=&#x22;javascripts/bundle.js&#x22;&#x3E;&#x3C;/script&#x3E;
</code></pre>

Of course, we'll have to remember to __tell webpack that our output should be <code>javascripts/bundle.js</code>__.
</section>

<section markdown="block">
## Move the Component into a Separate File

Create a directory that will contain all our components. Drop our <code>MyComponent</code> code there. Remember to export it... to make it available when using <code>require</code>.

In <code>components/MyComponent.js</code>:

<pre><code data-trim contenteditable>
var React = require(&#x27;react&#x27;);
var MyComponent = React.createClass({
&#x9;render: function() {
&#x9;&#x9;return (
&#x9;&#x9;&#x9;&#x3C;h1&#x3E;Hello&#x3C;/h1&#x3E;
&#x9;&#x9;);
&#x9;}
});
module.exports = MyComponent;
</code></pre>

</section>
<section markdown="block">
## Our Entry Point

Let's mount the component that we created to our DOM.
* require the React libraries
* require our component
<pre><code data-trim contenteditable>
var React = require(&#x27;react&#x27;);
var ReactDOM = require(&#x27;react-dom&#x27;);
var MyComponent = require(&#x27;./components/MyComponent&#x27;);
ReactDOM.render(&#x3C;MyComponent /&#x3E;, document.getElementById(&#x27;app&#x27;));

</code></pre>

</section>
<section markdown="block">
## Webpack Configuration

The webpack configuration file is called <code>webpack.config.js</code>. This will be in the root of your project directory.

<pre><code data-trim contenteditable>
var path = require('path');
module.exports = {
  // configure the entry point
  // where to output the bundle of static assets
  // and configure the plugins / modules that we'll use
};
</code></pre>

This is the _entry point_ into our client side web app... (anything that this file requires will be bundled by webpack)

<pre><code data-trim contenteditable>
  entry: './client.js',
</code></pre>
</section>

<section markdown="block">
## webpack Configuration Continued

Specify where the resulting JavaScript file should go... (<code>javascripts/bundle.js</code>)

<pre><code data-trim contenteditable>
  output: {
    path: path.join(__dirname, '/public'),
    filename: 'javascripts/bundle.js',
    publicPath: '/'
  },
</code></pre>

Specify our loaders (the transformations we'll be using...)

<pre><code data-trim contenteditable>
  module: {
    loaders: [ {
      // we don't need to run babel on app and node_modules (and probably others!)
      exclude: /node_modules|app.js|routes/,
      loader: 'babel',
      query: { presets:['react'] }
    }] 
  }
</code></pre>
</section>
<section markdown="block">
## Whew. Finally

In the root of your project folder, just run webpack... aaand hopefully we'll find a new jst file in <code>public/javascripts</code>

<pre><code data-trim contenteditable>
webpack
# magic
</code></pre>

<pre><code data-trim contenteditable>
ls public/javascripts
# hopefully bundle.js
</code></pre>

</section>

<section markdown="block">
## Let's Try Changing Our Component

Ok... instead of hello, we'll change the text in our component. __Let's see the output. What happened?__ &rarr;

* {:.fragment} nothing changed! __why__ &rarr;
* {:.fragment} probably because <code>bundle.js</code> is still the same... __what do we have to do?__ &rarr;
	* {:.fragment} ugh... we have to recompile with <code>webpack</code>
	* {:.fragment} (wait, what? are we working with C or Java, here... I thought this was JavaScript!?)

</section>

<section markdown="block">
## Automatically Rebuilding on Change

Soooo... there are a lot of ways to automatically refresh the contents of <code>bundle.js</code>. We're going to use one that:

* skips the _actual_ compilation to a file on disk
* and... instead, creates an in-memory version on file change
* and intercepts requests to the file using middleware (so that it picks up the in memory version rather than the one on the file system)

</section>

<section markdown="block">
## Webpack Dev Middleware

We'll use webpack-dev-middleware to do this.

<pre><code data-trim contenteditable>
npm install --save-dev webpack-dev-middleware
</code></pre>

In <code>app.js</code> add the code below to activate the middleware. Notice that:

* we read in our regular configuration file to configure our webpack-dev-middleware
* we should probably use environment variables to switch this on and off... since this isn't recommended for production

<pre><code data-trim contenteditable>
// should probably check environment variable to see if we're in DEV first...

var webpackDevMiddleware = require("webpack-dev-middleware");
var webpackConfig = require('./webpack.config.js')
var webpack = require("webpack");
var compiler = webpack(webpackConfig);
app.use(webpackDevMiddleware(compiler, {
}));
</code></pre>

We can even delete <code>bundle.js</code>, and we'll see that it all works out fine (since the in-memory version is being used)!
</section>
