---
layout: post
title: CSS Typography - Kerning, Ligatures, Swashes & Small-Caps
tags: [CSS, Typography]
---

[orig.Link](https://www.viget.com/articles/css-typography-kerning-ligatures-swashes-small-caps)


<!--more-->


### Prepping Font Files for the Web

* Open up Font Squirrel’s [Webfont Generator](https://www.fontsquirrel.com/tools/webfont-generator)
* Upload the **.otf** version of your selected font
* Enter “Expert” font-optimization mode

(Optional) Limit font file types. Woff and Woff2 cover most of your bases these days.

Under **OpenType Features**, use the **Comma Feature List** to note all of the features you would like to keep in your font. You could use Keep All Features, but keep in mind that more features mean a larger font file.

Most of the other default settings are good to leave alone. Here’s a run-down of the full settings I exported my font files with:

![webfont-settings]({{ site.baseurl }}/images/2016/03/webfont-settings.jpg)

Under **OpenType Features**, there is also the setting **OpenType Flattening**. Should you like only one or two stylistic sets in a font, like the small-caps version for example, then go with OpenType Flattening instead. You can replace the standard glyphs of a font and just reduce it to the set(s) you desire instead of adding on the sets to the base font. This font file then ends up a good bit smaller than the version exported with OpenType Features.

You’ll notice that the text I entered into my “Comma Feature List” is a list of short-codes for the features. CSS Tricks has [a full list of these font feature values](https://css-tricks.com/almanac/properties/f/font-feature-settings/). These short-codes are also used in your CSS with the `font-feature-settings` property.



### text-rendering

Chrome, Safari, and Firefox all calculate kerning and ligatures automatically because these browsers support the CSS property `text-rendering` which is set to “auto” by default. At least they will calculate these font properties when the processing power to enable these features is within reason. This option doesn’t exist at all in IE or Edge. Here are all the options for text-rendering:

{% highlight css %}
body {
  /* The Browser makes its best guess with its own rendering engine */
  /* results will vary based on the browser */
  text-rendering: auto;
  /* The Browser chooses page speed over font calculations and legibility */
  text-rendering: optimizeSpeed;
  /* The Browser chooses legibility over font calculations and page speed */
  text-rendering: optimizeLegibility;
  /* The Browser chooses exact font calculations over page speed and legibility */
  text-rendering: geometricPrecision;
}
{% endhighlight %}

While "optimizeLegibility" or "geometricPrecision" might be tempting, they can really hurt your page load speed. I recommend sticking with "auto" which works surprisingly well.



### font-feature-settings

The support for `font-feature-settings` is a bit more promising than text-rendering, with Safari being the particularly odd outlier as of version 9.  Safari 9.1 and iOS Safari 9.3 (the next versions for each) should include this feature shortly. In regards to IE, this setting works in IE11 and IE10, but no lower. It works by taking a font feature short-code and then a 1 or 0. 1 turns the setting on and 0 turns it off.

We can enable (and later disable) kerning in IE and Edge with:

{% highlight css %}
/* Enable kerning */
/* Check caniuse.com for current browser prefixes */
body {
  font-feature-settings: "kern" 1;
}
/* Disable kerning */
.heading--no-kern {
  font-feature-settings: "kern" 0;
}
{% endhighlight %}

Add on ligatures and swashes just by making font-feature-settings a comma-separated list:

{% highlight css %}
/* Enable kerning, ligatures and swashes */
.heading {
  font-feature-settings: "kern" 1, "liga" 1, "swsh" 1;
}
{% endhighlight %}

Should you enable kerning and ligatures at all (after all, they’re enabled by default with text-rendering in most modern browsers), then you may want to apply the CSS to the body element to cover all text. *Swashes are best applied to large headings since they can hurt legibility at small sizes.*



### Small-Caps with font-variant

You can certainly enable small-caps with `font-feature-settings` (with “smcp” as the font feature short-code), but I prefer the older CSS2 property font-variant. This setting can specifically enable small-caps.

{% highlight css %}
/* Enable small-caps */
/* No browser prefixes needed for this property */
.heading-small {
  font-variant: small-caps;
}
/* The fallback renders as if you were to apply these CSS rules to the default font glyphs */
.heading-small {
  text-transform: uppercase;
  font-size: 0.8em; // estimated size difference
}
{% endhighlight %}

The reason I prefer this setting is the browser support. Font-variant will enable small-caps in Safari 9 and lower where font-feature-settings doesn’t work. Also, it has a great fallback for IE9 and down.


### Further Reading


[OpenType features in web browsers — Tests](https://www.typotheque.com/articles/opentype_features_in_web_browsers_-_tests) \\
[font-feature-settings - CSS >> MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/font-feature-settings) \\
[Syntax for OpenType features in CSS >> Typekit](https://helpx.adobe.com/typekit/using/open-type-syntax.html) \\
[CSS Fonts Module Level 3 >> W3C](https://drafts.csswg.org/css-fonts-3/#font-rend-props)

