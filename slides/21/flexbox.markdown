---
layout: slides
title: "Flexbox"
---

<section markdown="block" class="intro-slide">
# {{ page.title }}

### {{ site.course_number}}-{{ site.course_section }}

<p><small></small></p>
</section>


<section markdown="block">
## Different Screen Sizes

With so many different devices that can be used to view a page, coming up with a design for different resolutions is pretty challenging...

__What are some layout/visual strategies that you can use to deal with different screen resolutions?__ &rarr;


</section>

<section markdown="block">
## Different Screen Sizes

__To cope with varying screen dimensions:__ &rarr;

* {:.fragment} we may consider resizing the actual elements on the page as the screen size changes
	* {:.fragment} for example, text and images could shrink or grow
	* {:.fragment} (but, of course, until a certain point)
* {:.fragment} we may also consider re-flowing the elements, or laying elements out differently
	* {:.fragment} for example, for a narrower display, we may stack elements rather than have them adjacent to each other
* {:.fragment} we could add or remove elements 
	* {:.fragment} for example, remove non-essential elements from a page on small resolutions
	* {:.fragment} or replace user interface elements that are more appropriate for the resolution (such as a _hamburger_ menu on a phone)

</section>

<section markdown="block">
## Layout

So... working with the display properties of __inline__ and __block__ is a bit limiting, especially when it comes to accommodating different screen sizes.

* {:.fragment} we don't have much control of how things flow/resize/are added or removed when a screen resolution changes
* {:.fragment} for example, block level elements don't really reflow in anyway
* {:.fragment} span elements just wrap, but don't have an explicitly _settable_ width and height
* {:.fragment} block and inline are rigid in the direction that elements are laid out (either biased vertically or horizontally)

</section>
<section markdown="block">
## Flexbox

One way to deal with these issues is to use __flexbox__.

* __flexbox__ is a layout mode that allows for more control over how elements are laid out, aligned, and sized to fill up available space
* flexbox gives the developer the ability to specify an elements' dimensions, arrangement, alignment, and surrounding white space:
	* when the layout must accommodate different screen sizes
	* ...even when the sizes of elements are unknown or dynamic
* use it for:
	* _simple_ layouts
	* or layouts for components of a larger application

</section>
<section markdown="block">
## Flexbox Examples

The following slides will build off of this html, css and JavaScript:

* check out the [examples repository for class 23](https://github.com/nyu-csci-ua-0480-002-fall-2015/examples/tree/master/class22) (you'll need to be logged in to github)
* [or jsbin](https://jsbin.com/zapuhezowi/1/edit?css,output)

</section>

<section markdown="block">
## Flex Container and Items

__Flexbox__ isn't a single property. Instead, it's a set of values and properties that are set on:

* __the flex container__ ... the element that contains items to be arranged using flexbox
* __flex items__ ... child elements within the flex container

</section>
<section markdown="block">
## Activating Flexbox

Set the __display__ property of the containing element, the flex container, to __flex__:

<pre><code data-trim contenteditable>
#container {
	display: flex; 	
}
</code></pre>

<br>
The items within the flex container will be laid out according to additional flexbox related properties.

__Let's try this.__ &rarr;
</section>

<section markdown="block">
## Flex Flow

Because flexbox isn't tied to a horizontal/inline layout or a vertical/block layout, its layout is defined by flex flow directions, the main axis and the cross axis.

* the __main axis__ of a flex container is the primary axis in which a container's items are laid out
	* it's the axis along which items _follow_ each other
	* it's not always horizontal
* the __cross axis__ of a flex container is perpendicular to the main axis
	
</section>

<section markdown="block">
## Main and Cross Axis Start and End

The items are placed in a flex container on the __main axis__:

* __main start and main end__ specify the start and end of where elements can be placed on the __main axis__
* __cross start and cross end__ specify the start and end of the perpendicular axis on which elements are placed 
	* for example, imagine that wrapping is enabled...
	* and that the main axis is horizontal
	* the cross axis would then be vertical...
	* so cross start would be the first line of horizontal items
	* and cross end would be the last line of horizontal items
</section>

<section markdown="block">
## Um. Pics or it Didn't Happen.

Let's take a look at the [flexbox diagram on mozilla developer network](https://developer.mozilla.org/files/3739/flex_terms.png):

* {:.fragment} flex container and flex items
* {:.fragment} main and cross axis
* {:.fragment} main and cross start and end

</section>
<section markdown="block">
## flex-direction

The _actual_ direction of the main and cross axis are specified by the __flex-direction__ property of the containing element. The possible values are:

* <code>row</code> - (default) lays out elements horizontally
* <code>row-reverse</code> - horizontally in reverse order of the source
* <code>column</code> - lays out elements vertically
* <code>column-reverse</code> - vertically in reverse order of the srouce


</section>

<section markdown="block">
## flex-direction example

__Let's try some of these values.__ &rarr;

<pre><code data-trim contenteditable>
#container {
	flex-direction: column;
}
</code></pre>

</section>

<section markdown="block">
## order

As mentioned in the flex-direction examples, items within the flex container are arranged in order that they appear in the markup (_source order_).

However, you can change the visual rendering of these items by using the __order__ property on specific __item__ elements. The possible values of order re integers

__Let's try making the first element last in a group of 5 elements.__ &rarr;

<pre><code data-trim contenteditable>
#item1 { 
	order: 5;
}
</code></pre>
</section>

<section markdown="block">
## flex-wrap

By default, flexbox will:

* attempt to fit everything on the same main-axis...
* even when resized:
	* reducing the dimensions results in the container and items shrinking
	* when the items can't shrink anymore, items will overflow
	* the content may fall outside of the viewable area of the window 

<br>
__Let's try resizing our window full of flex elements__ &rarr;

</section>

<section markdown="block">
## flex-wrap continued

__flex-wrap__ can specify what to do with flex items if the main axis no longer has space. This property is set on the flex container, and these are its possible values:

* <code>nowrap</code> - default, as mentioned in previous slides... items will fit on one line of main axis
* <code>wrap</code> - wrap to next line
* <code>wrap-reverse</code> - wrap to next line, opposite direction
</section>

<section markdown="block">
## flex-wrap example

__Let's try flex-wrap...__ &rarr;

<pre><code data-trim contenteditable>

</code></pre>
#container {
	flex-wrap: wrap;
}
</section>

<section markdown="block">
## Justifying Content

You can justify content on the main axis using the __justify-content__ property. It's possible values are:

* flex-start - at the beginning of main axis
* flex-end - at end of main axis
* center - centered
* space-between - space distributed evenly between elements, but elements start and end at main-start and main-end
* space-around - space distributed evenly... including before and after first and last element

<br>

__Let's try this.__ &rarr;
</section>

<section markdown="block">
## Aligning on the Cross Axis

You can align items on the cross axis using the __algin-items__ property.

* flex-start - at the beginning of cross axis
* flex-end - at end of cross axis
* center - centered along cross axis
* baseline 
* stretch - fill cross axis
<br>

__Let's try this.__ &rarr;
</section>

<section markdown="block">
## Resources

* [flexbox mdn docs](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flexible_Box_Layout/Using_CSS_flexible_boxes)
* [flexbox guide from css tricks](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)
* [flexbox in 5 minutes](https://cvan.io/flexboxin5/)
* [flexbox decision tree](http://jonibologna.com/content/images/flexboxsheet.pdf)

</section>
{% comment %}
## What is it?

* allows for control of layouts on varying screen resolutions

## Diagram
## Definitions

container:
display: flex
flex-direct: ... default is row, the direction flex items are placed in container 
{% endcomment %}

