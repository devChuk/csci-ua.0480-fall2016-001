---
layout: slides
title: "React and Webpack"
---

<section markdown="block" class="intro-slide">
# {{ page.title }}

### {{ site.course_number}}-{{ site.course_section }}

<p><small></small></p>
</section>

<section markdown="block">
## A Little Bit of Refresher

__What's react again?__ &rarr;

On its own __React__ is  a library for generating the user interface of an application. 
{:.fragment}

* {:.fragment} think of it as the __view__ in an MVC app
* {:.fragment} it provides an API for creating and rendering reusable view components
	* {:.fragment} including state management
	* {:.fragment} ...and event handling

</section>

<section markdown="block">
## Running a React App

__What did we use to demonstrate our small React examples?__ &rarr;

We used Codepen (jsbin is also an option). However... __we had to do a little bit of configuration first.__ &rarr;
{:.fragment}

* {:.fragment} for __codepen__: 
	* {:.fragment} set __Babel__ as the JavaScript preprocessor
	* {:.fragment} add the __react libaray__ as external JavaScript
* {:.fragment} for __jsbin__:
	* {:.fragment} use __Add Library__ to add react
	* {:.fragment} select __JSX (React)__ in the JavaScript drop down

</section>

<section markdown="block">
## Creating an Element

ReactElements are elements in a _virtual DOM_.  __Here's what creating a ReactElement looks like...__ &rarr;

<pre><code data-trim contenteditable>
React.createElement('h1', {className: 'hello'}, 'Hi There!'); 
</code></pre>
{:.fragment}

<code>createElement</code> has 3 parameters:
{:.fragment}

* {:.fragment} first parameter... element that you want to create as a string
* {:.fragment} second parameter... its attributes (note that __class is className__!)
* {:.fragment} third parameter... its text content
* {:.fragment}it'll return a __ReactElement__ object

</section>

<section markdown="block">
## Rendering

Changes in the virtual DOM are combined together and applied to the actual DOM in a way that minimizes DOM modification. __Here's what rendering a ReactElement looks like.__ &rarr;

<pre><code data-trim contenteditable>
ReactDOM.render(
    React.createElement('h1', {className: 'hello'}, 'Hi there!'), 
	document.body
);
</code></pre>
{:.fragment}

<code>render</code> has two arguments
{:.fragment}

* {:.fragment} first parameter... a <code>ReactElement</code>
* {:.fragment} second parameter... an insertion point (where to add element as a child - can be a regular <code>HTMLElement!</code>)

__Let's give it a try.__ &rarr;
{:.fragment}
</section>

<section markdown="block">
## Another Way

Sooo... there was another _unusual_ way of creating ReactElements. __What was it?__ &rarr;

__Using JSX, we could...__ &rarr;
{:.fragment}

<pre><code data-trim contenteditable>
React.render(
	<h1 className="hello">Hi there!</h1>, 
	document.body
);
</code></pre>
{:.fragment}
</section>

<section markdown="block">
## Another Way Explained....

__Why did that look so unusual?__ &rarr;

<pre><code data-trim contenteditable>
React.render(
	<h1 className="hello">Hi there!</h1>, 
	document.body
);
</code></pre>

Hey - there's markup in my JavaScript. It's an unquoted string! What!?
{:.fragment}
</section>

<section markdown="block">
## JSX

__So, what's JSX again?__ &rarr;


__JSX__ is an extension to JavaScript syntax that allows _XML-like_ syntax without

* {:.fragment} it's essentially a JavaScript preprocessor
	* {:.fragment} it takes in JavaScript with JSX syntax
	* {:.fragment} and _compiles_ JSX to plain vanilla JavaScript 
* {:.fragment} its usage is optional; you could just use plain JavaScript with react
* {:.fragment} also... __browsers don't quite understand JSX__ (maybe yet?)
	
</section>

<section markdown="block">
## JSX Continued

So that means... this JSX

<pre><code data-trim contenteditable>
<h1 className="hello">Hi there!</h1>
</code></pre>

...is equivalent to this vanilla JavaScript

<pre><code data-trim contenteditable>
React.createElement('h1', {className: 'hello'}, 'Hi there!'), 
</code></pre>

They both produce a ReactElement!
</section>

<section markdown="block">
## Components

You can bundle elements together into a single component. __Here's an example.__ &rarr;

<pre><code data-trim contenteditable>
var MyComponent = React.createClass({
  render: function() {
    return (
      &lt;div&gt; &lt;h1&gt;A Message&lt;&#47;h1&gt;{this.props.message}&lt;&#47;div&gt;
    );
  }
});

</code></pre>

<pre><code data-trim contenteditable>
React.render(
  <MyComponent message="Hi there!" &#47;>,
  document.body
);
</code></pre>
</section>

<section markdown="block">
## Components and Props

To make a component, use <code>React.createClass</code>, which takes an object as a parameter.

* the object must have a function called __render__ (it'll generate elements)
* note that a __component variable must start with uppercase__
* once you have a component, you can pass it to render using JSX, with the variable name as the tag name
* you can access attributes defined in JSX via __this.props__ in your component
</section>

<section markdown="block">
## Say Hi or Bye!

__Let's try to create a component that...__ &rarr;

* displays "hi" if the attribute, <code>greet</code> is true
* otherwise, it'll display "bye" instead

<br>
For example... rendering...

<pre><code data-trim contenteditable>
<MyComponent greet={true} &#47;>,
</code></pre>

Gives us

<pre><code data-trim contenteditable>
<div>hi</div>
</code></pre>

Changing <code>greet</code> to false would give us <code>bye</code> instead.
</section>

<section markdown="block">
## Say Hi or Bye!

<pre><code data-trim contenteditable>
var MyComponent = React.createClass({
  render: function() {
    var msg = this.props.greet ? 'hi' : 'bye';
    return (
      &lt;h1&gt;{msg}&lt;&#47;h1&gt;
    )
  }
});
</code></pre>

<pre><code data-trim contenteditable>
React.render(
	<MyComponent greet={true} &#47;>, 
	document.body
);
</code></pre>

</section>

<section markdown="block">
## Events

To add an event handler in JSX... add an inline attribute (wait, what!?). For example, click events would be represented by <code>onClick</code>:

<pre><code data-trim contenteditable>
var MyButton = React.createClass({
  onButtonClick: function(evt) {
    alert("Clicked!");
  },

  render: function() {
    return <div onClick={this.onButtonClick}>Press This Button<&#47;div>;
  }
});

React.render(
  <MyButton &#47;>,
  document.body
)
</code></pre>
</section>

<section markdown="block">
## State

__state__ is internal data controlled by the component (contrast this with __props__, which are controlled by whatever renders the component.

To initialize state properties when your component is create, __define a <code>getInitialState</code> within your component definition... and return the desired state properties as property value pairs within an object.__ &rarr;

<pre><code data-trim contenteditable>
{ // within a component definition
  getInitialState: function() {
    return {
      prop: val,
    }
  }
}
</code></pre>
{:.fragment}

__To read state....__ &rarr;
{:.fragment}

<pre><code data-trim contenteditable>
this.state.propertyName
</code></pre>
{:.fragment}
</section>

<section markdown="block">
## Using State... and Example

__Let's define a couple of state variables, and put their values in a list when the component is rendered.__ &rarr;

<pre><code data-trim contenteditable>
var MyComponent = React.createClass({
  render: function() {
    return (
      <ul>
      <li>{this.state.var1}</li>
      <li>{this.state.var2}</li>
      </ul>
    )
  },
  getInitialState: function() {
    return {
      var1: 'this is the first variable',
      var2: 'and a second variable'
    }
  }
});
</code></pre>
</section>

<section markdown="block">
## A Challenge

Using events and state, create a component that:

* renders a div with a class of number
* the number starts at 0
* every time you click on the div, the number increases

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

