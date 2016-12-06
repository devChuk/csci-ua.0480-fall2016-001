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
