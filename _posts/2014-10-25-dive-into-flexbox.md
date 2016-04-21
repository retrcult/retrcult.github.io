---
layout: post
title: Dive into Flexbox
tags: [CSS, flexbox]
permalink: dive-into-flexbox
---

Examples:<br>
[CSS Flexbox Please!](http://demo.agektmr.com/flexbox/)<br>
[Solved By Flexbox — Cleaner, hack-free CSS](https://github.com/philipwalton/solved-by-flexbox)

Intro Guide:<br>
[Dive into Flexbox](http://bocoup.com/weblog/dive-into-flexbox/)<br>
[Using the CSS3 flexbox layout](http://helephant.com/2013/03/23/css3-flexbox-layout/)<br>
[A Guide to Flexbox](http://css-tricks.com/snippets/css/a-guide-to-flexbox/)<br>
[弹性框布局](http://msdn.microsoft.com/zh-cn/library/ie/bg124109(v=vs.85).aspx)

Further Reading:<br>
[W3C Last Call Working Draft, 25 September 2014](http://www.w3.org/TR/css3-flexbox/)

Browser Compatibility & Some Outdated Stuff:<br>
[Flexbox / Flexible Box Layout Browser Support](http://ptb2.me/flexbox/)<br>
[Working with flexbox: The new specification](http://www.adobe.com/devnet/html5/articles/working-with-flexbox-the-new-spec.html)<br>
[Advanced Cross-Browser Flexbox](https://dev.opera.com/articles/advanced-cross-browser-flexbox/)<br>
[IE 10 中弹性框布局](http://msdn.microsoft.com/zh-cn/library/ie/hh673531(v=vs.85).aspx)


<!--more-->


<style>
.property {

}
.propdef, .propdef-sub {
  display: -webkit-box;
  display: -webkit-flex;
  display: -ms-flexbox;
  display: flex;
  margin: 1.618em 0 1em;
  padding: 0.618em 0;
  border: 1px solid #e5e6e4;
}
.propdef-sub {
  margin: .382em 0;
  margin-left: 3.82%;
  padding: .382em 0;
  border-width: 0 0 0 2px;
  border-color: #e5e6e4;
}
.prop-nm {
  -webkit-flex: 236; /* Safari 6.1+ */
      -ms-flex: 236; /* IE 10 */
          flex: 236;
  padding-right: .763em;
  text-align: center;
  position: relative;
}
.prop-nm:after {
  content: ':';
  position: absolute;
  right: 1.618%;
}
.prop-val {
  -webkit-flex: 763;
      -ms-flex: 763;
          flex: 763;
  padding-left: .763em;
}
.propdesc {
  margin-left: 10%;
}
</style>

### Properties for the Parent (flex container)

<div class="propdef">
  <div class="prop-nm">display</div>
  <div class="prop-val">flex | inline-flex</div>
</div>

{% highlight css %}
.flexbox {
  display: -webkit-box;  /* Old Safari */
  display: -webkit-flex; /* Safari 6.1+ */
  display: -ms-flexbox;  /* IE 10 */
  display: flex;
 }
{% endhighlight %}

<div class="propdef">
  <div class="prop-nm">flex-flow</div>
  <div class="prop-val">&lt;flex-direction&gt; || &lt;flex-wrap&gt;</div>
</div>

<div class="propdef-sub">
  <div class="prop-nm">flex-direction</div>
  <div class="prop-val">row | row-reverse | column | column-reverse</div>
</div>

{% highlight css %}
  flex-direction: row; /*Initial*/
{% endhighlight %}

<div class="propdef-sub">
  <div class="prop-nm">flex-wrap</div>
  <div class="prop-val">nowrap | wrap | wrap-reverse</div>
</div>

{% highlight css %}
  flex-wrap: nowrap; /*Initial*/
{% endhighlight %}


<div class="propdef">
  <div class="prop-nm">justify-content</div>
  <div class="prop-val">flex-start | flex-end | center | space-between | space-around</div>
</div>

{% highlight css %}
  justify-content: flex-start; /*Initial*/
{% endhighlight %}

{% highlight css %}
.flexbox {
  -webkit-justify-content: space-between; /* Safari 6.1+ */
  -ms-flex-pack: justify; /* IE 10 */
  justify-content: space-between;
 }
{% endhighlight %}


<div class="propdef">
  <div class="prop-nm">align-items</div>
  <div class="prop-val">flex-start | flex-end | center | baseline | stretch</div>
</div>

{% highlight css %}
  align-items: stretch; /*Initial*/
{% endhighlight %}

{% highlight css %}
.flexbox {
	-webkit-align-items: center; /* Safari 7.0+ */
	-ms-flex-align: center; /* IE 10 */
	align-items: center;
 }
{% endhighlight %}


<div class="propdef">
  <div class="prop-nm">align-content</div>
  <div class="prop-val">flex-start | flex-end | center | space-between | space-around | stretch</div>
</div>

{% highlight css %}
  align-content: stretch; /*Initial*/
{% endhighlight %}


### Properties for the Children (flex items)

Note that `float`, `clear` and `vertical-align` have no effect on a flex item.

<div class="propdef">
  <div class="prop-nm">order</div>
  <div class="prop-val">&lt;integer&gt;</div>
</div>

{% highlight css %}
  order: 0; /*Initial*/
{% endhighlight %}

<div class="propdef">
  <div class="prop-nm">flex</div>
  <div class="prop-val">none | [&lt;flex-grow&gt; &lt;flex-shrink&gt;? || &lt;flex-basis&gt;]</div>
</div>

{% highlight css %}
  flex: 0 1 main-size; /*Initial*/
{% endhighlight %}

{% highlight css %}
  flex: 0 main-size;
  flex: initial;
/* Equivalent to `flex: 0 1 main-size` (Initial). Sizes the item based on the 'width'/'height' properties. (If the item's main size property computes to auto, this will size the flex item based on its contents.) Makes the flex item inflexible when there is positive free space, but allows it to shrink to its min-size when there is insufficient space. The alignment abilities or auto margins can be used to align flex items along the main axis. */

  flex: auto;
/* Equivalent to `flex: 1 1 main-size`. Sizes the item based on the 'width'/'height' properties, but makes them fully flexible, so that they absorb any free space along the main axis. If all items are either `flex: auto`, `flex: initial`, or `flex: none`, any positive free space after the items have been sized will be distributed evenly to the items with `flex: auto`. */

  flex: none;
/* Equivalent to `flex: 0 0 main-size`. This value sizes the item according to the 'width'/'height' properties, but makes the flex item fully inflexible. This is similar to `initial`, except that flex items are not allowed to shrink, even in overflow situations. */

  flex: <positive-number>;
/* Equivalent to `flex: <positive-number> 1 0%`. Makes the flex item flexible and sets the flex basis to zero, resulting in an item that receives the specified proportion of the free space in the flex container. If all items in the flex container use this pattern, their sizes will be proportional to the specified flex factor. */
{% endhighlight %}
</div>

<div class="propdef">
  <div class="prop-nm">align-self</div>
  <div class="prop-val">auto | flex-start | flex-end | center | baseline | stretch</div>
</div>

{% highlight css %}
  align-self: auto; /*Initial*/
{% endhighlight %}

