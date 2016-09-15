---
layout: slides
title: "Objects and Prototypes Summary"
---

<section markdown="block" class="intro-slide">
# {{ page.title }}

### {{ site.course_number}}-{{ site.course_section }}

<p><small></small></p>
</section>

<section markdown="block">
## Objects 

In JavaScript, an __object__ is basically just a container of properties

* properties have names and values
* a property can be any string
* values can be any value, including functions (called methods)


</section>

<section markdown="block">
## Accessing Properties

__How can you get / retrieve a value that's associated with a property name from an object?__ &rarr;

* {:.fragment} use dot notation: <code>obj.propName</code>
* {:.fragment} use bracket notation: <code>obj[propName]</code>
* {:.fragment} (use bracket notation for property names that aren't valid variable names or for dynamic property names)

</section>

<section markdown="block">
## Working With Objects

__How do you add a new property and value pair to an object?__ &rarr;

* {:.fragment} simply use the dot operator or bracket notation, and assignment
* {:.fragment} <code>obj.newProp = newVal</code> or <code>obj[newProp] = newVal</code>

__How do you modify an existing property?__ &rarr;
{:.fragment}

* {:.fragment} same as adding... dot operator or bracket notation, and assignment
* {:.fragment} <code>obj.prop = newVal</code> or <code>obj[prop] = newVal</code>

__And finally, how do you remove a property?__ &rarr;
{:.fragment}

* {:.fragment} use <code>delete</code> before referencing a property name
* {:.fragment} <code>delete obj.prop</code> 

</section>


<section markdown="block">
## Prototype

JavaScript's objects are __prototype__ based rather than class based:

* an object can inherit properties from another object, its __prototype__ 
* an object's prototype can also have a prototype!
* this chain of prototypes usually ends with <code>Object.prototype</code>
* a property is found by:
	* first looking if the object itself contains the property
	* then the object's prototype
	* ...and the prototype's prototype
	* until it reaches an object with null as its prototype (such as <code>Object.prototype</code>)
</section>

<section markdown="block">
## Object.prototype

__What methods does Object.prototype contain (we definitely know two)?__ &rarr;

* {:.fragment} <code>toString</code>
* {:.fragment} <code>hasOwnProperty</code>
* {:.fragment} [See others listed in docs on MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/prototype)
</section>

<section markdown="block">
## Getting an Object's Prototype

__How do you retrieve an object's original prototype?__ &rarr;

* {:.fragment} Use <code>Object.getPrototypeOf</code> 	
* {:.fragment} (note that attempting to access a property named <code>.prototype</code> on your object does not _actually_ return your object's prototype!)
	* {:.fragment} for example...
	* {:.fragment} <code>var obj = {};</code>
	* {:.fragment} <code>console.log(Object.getPrototypeOf(obj));</code>
	* {:.fragment} <code>console.log(obj.prototype); // <-- don't do this, you'll get undefined</code>
</section>

<section markdown="block">
## Creating Objects

__Name 3 ways to create objects.__ &rarr;

* {:.fragment} object literals
* {:.fragment} <code>Object.create</code>
* {:.fragment} constructors

</section>

<section markdown="block">
## Object Literals

You can create objects simply by using curly braces!

* <code>{}</code> is an empty object
* <code>{name1:val1, name2:val2}</code> ... you can create property and value pairs
	* each property connected to a value with a colon
	* each pair separated by a comma

__What do you think the prototype of an object created with an object literal is?__ &rarr;

<pre><code data-trim contenteditable>
Object.getPrototypeOf({}) === Object.prototype
</code></pre>

</section>

<section markdown="block">
## Object.create

__How does Object.create work / what does it do?__ &rarr;

You use <code>Object.create(someOtherObject)</code> to create objects with a specified prototype. __The single argument passed in to <code>Object.create</code> becomes the new object's prototype.__ ...__Let's see an example.__ &rarr;
{:.fragment}

<pre><code data-trim contenteditable>
var cat = {cute: 'very'};
var kitten = Object.create(cat);
console.log(kitten.cute); // <-- kitten inherits cute from cat! 
</code></pre>
{:.fragment}

</section>

<section markdown="block">
## Constructor

__What's a constructor?__ &rarr;

A __constructor__ is a regular function called with <code>new</code> in front of it.
{:.fragment}

<pre><code data-trim contenteditable>
function Cat() { }
var c = new Cat();
</code></pre>
{:.fragment}

</section>

<section markdown="block">
## Constructors and Properties

__How can we set properties on all new objects created by a constructor?__ &rarr;

Use __this__ to refer to a fresh empty object that's returned by the constructor. Add properties to __this__ to set properties on the newly created object. __Let's see an example.__ &rarr;
{:.fragment}

<pre><code data-trim contenteditable>
function Cat() { this.cute = 'very'; }
var c = new Cat();
var d = new Cat();
console.log(c.cute, d.cute); // <-- properties on new objects are set!
</code></pre>
{:.fragment}

</section>

<section markdown="block">
## Using This Continued

Using __this__ creates a property name and value for every new object instantiated with the constructor. Each object has its own copy. These properties are considered "own" properties (they haven't been inherited).

<pre><code data-trim contenteditable>
function Cat() { this.nicknames = ['katy furry', 'kitty wap']; }
var c = new Cat();
var d = new Cat();
d.nicknames.push('tail-fur swift');
console.log(c.nicknames, d.nicknames); // <-- properties are different!
console.log(c.hasOwnProperty('nicknames')); // <-- yup, bar is an 'own' property
</code></pre>

</section>

<section markdown="block">
## Adding Properties Continued

You can also use the constructor's (the _actual_ function object) own __prototype__ (it's really called <code>prototype</code>!) property to set the properties on the prototype of all instances created by this constructor.

<pre><code data-trim contenteditable>
function Cat() { }
var c = new Cat();
Cat.prototype.cute = 'very';
console.log(c.cute); 
// ^-- such wow! c gained a new prop even though
// it was instantiated before setting a property
// on Cat's prototype object
</code></pre>

</section>

<section markdown="block">
## Constructor's Prototype Property

Note that since we're setting properties on the prototype object, __these properties are not _own_ properties__.

<pre><code data-trim contenteditable>
function Cat() { }
var c = new Cat();
Cat.prototype.cute = 'very';
console.log(c.cute); // <-- cute exists
console.log(c.hasOwnProperty(c.cute)); // <-- but it's inherited (not an own prop)
</code></pre>
</section>

<section markdown="block">
## Constructor's Prototype Property Continued

You can also just assign an entire object to the prototype property... but this doesn't change all instances like setting individual properties on the constructor's prototype property does.

<pre><code data-trim contenteditable>
function Cat() { this.cute = 'very' }
function Kitten() { }
Kitten.prototype = new Cat();
var k = new Kitten(); // <-- instantiated *after* setting prototype!
console.log(k.cute); // <-- cute exists!
</code></pre>

</section>
