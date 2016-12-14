---
layout: slides
title: "Some ES6 Features!"
---
<section markdown="block" class="intro-slide">
# {{ page.title }}

### {{ site.course_number}}-{{ site.course_section }}

<p><small></small></p>
</section>

<section markdown="block">
## ES6

__Where is it available?__ &rarr;

* most features are supported by __node (server side)__
    * [see the compatibility table](http://kangax.github.io/compat-table/es6/)
    * there are some exceptions, notably `import`/modules
* same for most modern browsers (client side)
* feel free to use in your code!
</section>

<section markdown="block">
## Declaring Variables

__We can declare variables using these keywords:__ &rarr;

* `var` - (we've know this!) function level scope
* `let` - es6! ... block level scope
* `const` - es6! ... block level scope, but you can't reassign this name to another object
    * note - does not make value constant
    * you just can't use `=` again
        <pre><code data-trim contenteditable>
// AN ERROR OCCURS!
const foo = 'bar';
foo = 'baz';
</code></pre>

<br>

</section>

<section markdown="block">
## const, let, and var

__OK... so, which is the right one to use?__ &rarr;

1. __NEVER USE__ `var` again!
2. consider using `const` for all variable declarations
3. some common places to use `let`: ... perhaps in a for loop
    <pre><code data-trim contenteditable>
for(let i = 0; i < 5; i++) {
    console.log(i);
}
</code></pre>

<br>

Note that this will not work!

<pre><code data-trim contenteditable>
for(const i = 0; i < 5; i++) {
    console.log(i);
}
</code></pre>
</section>

<section markdown="block">
## Block Level Scope?

Using `let` and `const` gives you block level scope, so, now, __JavaScript may behave more _normally_:__ &rarr;

<pre><code data-trim contenteditable>
for(let i = 0; i < 5; i++) {
    console.log(i);
    let greeting = "hello " + i;
}
// these console.logs  will give an error!
console.log(i); 
console.log(greeting); 
</code></pre>

`greeting` and `i` are only available within curly braces, such as within:

* within a function definition
* a loop / other control structure
* etc.


</section>

<section markdown="block">
## Temporal Dead Zone

ALSO.. __`let` and `const` now act _normally_ vs `var`__ &rarr;

<pre><code data-trim contenteditable>
console.log(foo);
var foo = 'bar';
// works fine
</code></pre>
<pre><code data-trim contenteditable>
console.log(foo);
let foo = 'bar';
// does not work
</code></pre>

Temporal because... it's __when__ let is declared, not where it is in actual code.

</section>

<section markdown="block">
## Arrow Functions

__We use function expression__ &rarr;

* as anonymous functions (when we need a callback, but we don't want to define a separate named function)
* for creating functions as values to assign to a variable or property name

<pre><code data-trim contenteditable>
function(arg1, arg2) {
    // body
}
</code></pre>

ES6 allows new syntax and semantics for doing this, using __arrow function__ &rarr;

<pre><code data-trim contenteditable>
(arg1, arg2) => { // body goes here }
</code></pre>

* `this` in arrow function is this of outer scope
* works the way you expect (it won't _just be global_ for most cases)
    * maybe no more `bind` needed!
</section>

<section markdown="block">
## String Interpolation

Create a string with backticks:

<pre><code data-trim contenteditable>
const target = 'world';
console.log(`hello ${target}`)
</code></pre>


</section>

<section markdown="block">
##  Destructuring

Think of it as multiple assignment:

* works with Arrays
* works with objects (but you use curly braces instead)

<br>

<pre><code data-trim contenteditable>
const coord = [1, 2];
let [x, y] = coord;
console.log(x); // 1
console.log(y); // 2
</code></pre>

</section>

<section markdown="block">
# More ES6!
</section>


<section markdown="block">
## Arrow Functions Again

Arrow functions are already pretty concise. In ES5, you might find code that looks like this:

<pre><code data-trim contenteditable>
var numbers = [1, 2, 3, 4];
var result = numbers.map(function(num) { return num * 2});
</code></pre>
{:.fragment}

With arrow functions, that becomes:
{:.fragment}

<pre><code data-trim contenteditable>
const numbers = [1, 2, 3, 4];
const result = numbers.map((num) => {return num * 2});
</code></pre>
{:.fragment}

In fact, we can drop the parentheses, curly braces and rely on the fact that arrow functions will implicitly return the last expression to drop the `return` to get this:
{:.fragment}

<pre><code data-trim contenteditable>
const numbers = [1, 2, 3, 4];
const result = numbers.map(num => num * 2);
</code></pre>
{:.fragment}
</section>

<section markdown="block">
## Where _Not_ to Use Arrow Functions

That was pretty great, so __why don't we use arrow functions all of the time?__ &rarr;

__There are some places where they don't work quite right.__ 
{:.fragment}

* {:.fragment} creating addEventListener callbacks where you want `this` to refer to the element that generated the event
* {:.fragment} creating constructors
* {:.fragment} creating methods
    * {:.fragment} either on object literals
    * {:.fragment} or on prototypes

<br>
__But why not?__ &rarr;
{:.fragment}

Remember, arrow functions do not bind this to a new value, and instead gets its this from the enclosing scope
{:.fragment}


</section>

<section markdown="block">
## Arrow Functions and `addEventListener`

__Be careful when using arrow functions and `addEventListener`__ &rarr;

Starting with this code:
<pre><code data-trim contenteditable>
const button = document.createElement('button');
document.body.appendChild(button).textContent = 'Click Me';
</code></pre>

The following alerts different messages!
{:.fragment}

<pre><code data-trim contenteditable>
// alerts window object (essentially global)
button.addEventListener('click', () => {alert(this)});
</code></pre>
{:.fragment} 

<pre><code data-trim contenteditable>
// alerts button element
button.addEventListener('click', function()  {alert(this)});
</code></pre>
{:.fragment}

* {:.fragment} if you want `this` in your event handler to reference the element event's target element, then use function expressions
* {:.fragment} ...because arrow functions don't create their own `this`, and instead use the this from the surrounding context

</section>

<section markdown="block">
## Don't Use Arrow Functions to Create Methods

__What's the output of this code?__ &rarr;

<pre><code data-trim contenteditable>const cat = {
    sound: 'meow',
    meow: () => {console.log(this.sound);}
};
cat.meow();
</code></pre>

* {:.fragment} `undefined`
* {:.fragment} ...because arrow functions do not bind a new value to `this`
* {:.fragment} again, `this` remains the same as the `this` in the containing context / scope
</section>

{% comment %}
<section markdown="block">
## 

</section>

<section markdown="block">
## 

<pre><code data-trim contenteditable>
const obj ={
    f(){ console.log('f');},
    g(){ console.log('g');}
};
obj.f();
obj.g();
</code></pre>
</section>
<section markdown="block">
## 


* arrow functions
    * do not use as methods, why?
        * this works normally in arrow functions
        * but that means you can't use them as methods
        * this will be bound to global
    * dropping parens, curly braces and return to get last expression back (implicit return)
* speaking of methods
    * shorthand methods in objects
* classes
    * classes and inheritance look like classical inheritance (it looks normal now)
    * behind the scenes it's still prototype
    * constructor
    * methods
    * no commas
    * class syntax
    * extending classes




</section>


{% endcomment %}









