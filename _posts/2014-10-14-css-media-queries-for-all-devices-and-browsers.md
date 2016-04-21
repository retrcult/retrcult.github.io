---
layout: post
title: CSS Media Queries
tags: [CSS, Sass, MediaQueries]
permalink: css-media-queries
---

[Orig][1]

[1]: http://blog.templatemonster.com/2014/10/14/css-media-queries-for-all-devices-and-browsers-including-ie7-and-ie8/
     "CSS Media Queries for All Devices and Browsers (Including IE7 and IE8)"

{% highlight css %}
/* Smartphones (portrait and landscape) ----------- */
@media only screen
and (min-device-width : 320px)
and (max-device-width : 480px) {
/* STYLES GO HERE */
}

/* Smartphones (landscape) ----------- */
@media only screen
and (min-width : 321px) {
/* STYLES GO HERE */
}

/* Smartphones (portrait) ----------- */
@media only screen
and (max-width : 320px) {
/* STYLES GO HERE */
}

/* iPads (portrait and landscape) ----------- */
@media only screen
and (min-device-width : 768px)
and (max-device-width : 1024px) {
/* STYLES GO HERE */
}

/* iPads (landscape) ----------- */
@media only screen
and (min-device-width : 768px)
and (max-device-width : 1024px)
and (orientation : landscape) {
/* STYLES GO HERE */
}

/* iPads (portrait) ----------- */
@media only screen
and (min-device-width : 768px)
and (max-device-width : 1024px)
and (orientation : portrait) {
/* STYLES GO HERE */
}

/* Desktops and laptops ----------- */
@media only screen
and (min-width : 1224px) {
/* STYLES GO HERE */
}

/* Large screens ----------- */
@media only screen
and (min-width : 1824px) {
/* STYLES GO HERE */
}

/* iPhone 5 (portrait &amp; landscape)----------- */
@media only screen
and (min-device-width : 320px)
and (max-device-width : 568px) {
/* STYLES GO HERE */
}

/* iPhone 5 (landscape)----------- */
@media only screen
and (min-device-width : 320px)
and (max-device-width : 568px)
and (orientation : landscape) {
/* STYLES GO HERE */
}

/* iPhone 5 (portrait)----------- */
@media only screen
and (min-device-width : 320px)
and (max-device-width : 568px)
and (orientation : portrait) {
/* STYLES GO HERE */
}
{% endhighlight %}


<!--more-->


#### Media Queries Mixins for Sass

{% highlight scss %}
$break-points: (
  xs: 320px,
  sm: 568px,
  md: 768px,
  lg: 1024px,
  xl: 1224px,
  xx: 1824px
  );

// media queries
// @include mq(gt-sm) {  }

@function bkpt($breakpoint) {
  @return map-get($break-points, $breakpoint);
}

@mixin mq($point) {

  @if $point == gt-sm {
    @media only screen and ( min-width: bkpt(sm) + 1px ) { @content; }
  }
  @else if $point == gt-md {
    @media only screen and ( min-width: bkpt(md) + 1px ) { @content; }
  }
  @else if $point == gt-lg {
    @media only screen and ( min-width: bkpt(lg) + 1px ) { @content; }
  }

  @else if $point == lt-sm {
    @media only screen and ( max-width: bkpt(sm) ) { @content; }
  }
  @else if $point == lt-md {
    @media only screen and ( max-width: bkpt(md) ) { @content; }
  }
  @else if $point == lt-lg {
    @media only screen and ( max-width: bkpt(lg) ) { @content; }
  }

  @else if $point == sm-md {
    @media only screen and ( min-width: bkpt(sm) + 1px ) and ( max-width: bkpt(md) ) { @content; }
  }
  @else if $point == sm-lg {
    @media only screen and ( min-width: bkpt(sm) + 1px ) and ( max-width: bkpt(lg) ) { @content; }
  }

  @else {
    @media only screen and ( min-width: $point + 1px ) { @content; }
  }
}
{% endhighlight %}

[Conditional Media Query Mixin | Codepen](http://codepen.io/dsheiko/pen/KeLGy)<br>
[sass-mediaqueries | Github](https://raw.githubusercontent.com/paranoida/sass-mediaqueries/master/_media-queries.scss)<br>
[Media Queries with superpowers | Github](https://github.com/sass-mq/sass-mq)<br>



### Media Queries Support

CSS Media queries are supported in Internet Explorer (IE) 9+, Firefox 3.5+, Safari 3+, Opera 7+, as well as on smartphones and other screen-based devices.

Normally IE5 to IE8 do not support CSS3 Media Query. But there's a great [jQuery plugin](https://code.google.com/p/css3-mediaqueries-js/) called css3-mediaqueries. You can use the following way to include the script.

{% highlight html %}
<!-- css3-mediaqueries.js for IE less than 9 -->
<!-- [if lt IE 9]>
<script src="http://css3-mediaqueries-js.googlecode.com/svn/trunk/css3-mediaqueries.js"></script>
<![endif]-->
{% endhighlight %}

Then write media query in style sheet and check it in IE. It will work nicely with IE8, IE7, even in older versions IE6 or IE5.

*Notes: It doesn't work on @import'ed stylesheets*

{% highlight css %}
@media (min-width: 481px) { /* ... */ } will not work, it must be
@media screen and (min-width: 980px)
/* Use keyword 'and' in query ('or' is not supported) */
{
/* CSS Document */
}
{% endhighlight %}



### Further Reading

[Media Queries (W3C Recommendation)](http://www.w3.org/TR/css3-mediaqueries)<br>
[What is the difference between "screen" and "only screen" in media queries?](http://stackoverflow.com/questions/8549529/what-is-the-difference-between-screen-and-only-screen-in-media-queries)<br>

