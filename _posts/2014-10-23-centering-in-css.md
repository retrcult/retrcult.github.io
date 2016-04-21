---
layout: post
title: Centering in CSS
tags: [CSS]
permalink: centering-in-css
---

[Orig][1]

[1]: http://css-tricks.com/centering-css-complete-guide/
     "Centering in CSS: A Complete Guide"

## Horizontally

### Is it inline or inline-* elements (like text or links)?

You can center inline elements horizontally, within a block-level parent element, with just:

{% highlight css %}
.center-children {
  text-align: center;
}
{% endhighlight %}

This will work for inline, inline-block, inline-table, inline-flex, etc.

### Is it a block level element?

You can center a block-level element by giving it `margin-left` and `margin-right` of *auto* (and it has a set `width`, otherwise it would be full width and wouldn't need centering). That's often done with shorthand like this:

{% highlight css %}
.center-me {
  margin: 0 auto;
}
{% endhighlight %}

This will work no matter what the width of the block level element you're centering, or the parent.

! Note that you can't `float` an element to the center. [There is a trick though](http://css-tricks.com/float-center/).

### Is there more than one block level element?

If you have two or more block-level elements that need to be centered horizontally in a row, chances are you'd be better served making them a different `display` type. Here's an example of making them *inline-block* and an example of *flexbox*:

{% highlight css %}
.inline-block-center {
  text-align: center;   /* ! */
}
.inline-block-center div {
  display: inline-block;
  text-align: left;
}
{% endhighlight %}

{% highlight css %}
.flex-center {
  display: flex;
  justify-content: center;
}
{% endhighlight %}

Unless you mean you have multiple block level elements stacked on top of each other, in which case the auto margin technique is still fine.

-----

## Vertically

### Is it inline or inline-* elements (like text or links)?

#### Is it a single line?

Sometimes inline / text elements can appear vertically centered, just because there is equal padding above and below them.

{% highlight css %}
.link {
  padding-top: 30px;
  padding-bottom: 30px;
}
{% endhighlight %}

If padding isn't an option for some reason, and you're trying to center some text that you know will not wrap, there is a trick were making the `line-height` equal to the `height` will center the text.

{% highlight css %}
.center-text-trick {
  height: 100px;
  line-height: 100px;
  white-space: nowrap;
}
{% endhighlight %}

#### Is it multiple lines?

Equal padding on top and bottom can give the centered effect for multiple lines of text too, but if that isn't going to work, perhaps the element the text is in can be a *table cell*, either literally (default is `vertical-align: middle;`) or made to behave like one with CSS. The `vertical-align` property handles this, in this case, unlike what it normally does which is handle the alignment of elements aligned on a row. ([More on that](http://css-tricks.com/what-is-vertical-align/).)

{% highlight css %}
.center-table {
  display: table;
  height: 250px;
  width: 240px;
}
.center-table p {
  display: table-cell;
  vertical-align: middle;
}
{% endhighlight %}

If something table-like is out, perhaps you could use *flexbox*? A single flex-child can be made to center in a flex-parent pretty easily.

{% highlight css %}
.flex-center-vertically {
  display: flex;
  justify-content: center;
  flex-direction: column;
  height: 400px;
}
{% endhighlight %}

If both of these techniques are out, you could employ the "ghost element" technique, in which a *full-height pseudo element is placed inside the container and the text is vertically aligned with that*.

{% highlight css %}
.ghost-center {
  position: relative;
}
.ghost-center::before {
  content: " ";
  display: inline-block;
  height: 100%;
  width: 1%;
  vertical-align: middle;
}
.ghost-center p {
  display: inline-block;
  vertical-align: middle;
}
{% endhighlight %}

### Is it a block-level element?

#### Do you know the height of the element?

If you do know the height, you can center vertically like:

{% highlight css %}
.parent {
  position: relative;
}
.child {
  position: absolute;
  top: 50%;
  height: 100px;
  margin-top: -50px; /* account for padding and border if not using box-sizing: border-box; */
}
{% endhighlight %}

#### Is the element of unknown height?

{% highlight css %}
.parent {
  position: relative;
}
.child {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
}
{% endhighlight %}

#### Can you use flexbox?

{% highlight css %}
.parent {
  display: flex;
  flex-direction: column;
  justify-content: center;
}
{% endhighlight %}

<!--more-->

-----

## Both Horizontally and Vertically

#### Is the element of fixed width and height?

Using negative margins equal to half of that width and height, after you've absolutely positioned it at 50% / 50% will center it with great cross browser support:

{% highlight css %}
.parent {
  position: relative;
}
.child {
  width: 300px;
  height: 100px;
  padding: 20px;
  position: absolute;
  top: 50%;
  left: 50%;
  margin: -70px 0 0 -170px;
}
{% endhighlight %}

#### Is the element of unknown width and height?

If you don't know the width or height, you can use the transform property and a negative translate of 50% in both directions (it is based on the current width/height of the element) to center:

{% highlight css %}
.parent {
  position: relative;
}
.child {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
{% endhighlight %}

#### Can you use flexbox?

To center in both directions with flexbox, you need to use two centering properties:

{% highlight css %}
.parent {
  display: flex;
  justify-content: center;
  align-items: center;
}
{% endhighlight %}

Or just:

{% highlight css %}
.parent {
  display: flex;
}
.child {
  margin: auto;
}
{% endhighlight %}

