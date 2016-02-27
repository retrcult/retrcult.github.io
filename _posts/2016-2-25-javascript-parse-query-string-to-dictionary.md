---
layout: post
title: JavaScript Tips - Parse QueryString to Dictionary
tags: [JavaScript]
---

[orig.Link](http://pietschsoft.com/post/2015/09/25/JavaScript-Tips-Parse-QueryString-to-Dictionary)

The following code can be used to read what the full QueryString value is from the current pages URL:

{% highlight js %}
var queryString = document.location.search;
{% endhighlight %}

Although, the QueryString value can be accessed from JavaScript, it isn't parsed and separated out into any kind of Key/Value pairs automatically. This parsing must be done manually. Below is an example parsing function and it's example usage:

<!--more-->

{% highlight js %}

// get raw QueryString value
var queryString = document.location.search;
var dict = parseQueryStringToDictionary(queryString);

// Enumerate through all the Key/Value pairs in the Dictionary object
for(var item in dict) {
    var key = item;
    var value = dict[item];
    alert('Key: ' + key + '\nValue: ' + value);
}
// Get Value by Key directly
alert(dict['name']);


/* Parse QueryString using String Splitting */
function parseQueryStringToDictionary(queryString) {
    var dictionary = {};

    // remove the '?' from the beginning if it exists
    if (queryString.indexOf('?') === 0) {
        queryString = queryString.substr(1);
    }

    // Step 1: separate out each key/value pair
    var parts = queryString.split('&');
    for(var i = 0; i < parts.length; i++) {
        var p = parts[i];
        // Step 2: Split Key/Value pair
        var keyValuePair = p.split('=');
        // Step 3: Add Key/Value pair to Dictionary object
        var key = keyValuePair[0];
        var value = keyValuePair[1];
        // decode URI encoded string
        value = decodeURIComponent(value);
        value = value.replace(/\+/g, ' ');
        // Key/Value Pair
        dictionary[key] = value;
    }
    // Step 4: Return Dictionary Object
    return dictionary;
}

{% endhighlight %}




{% highlight js %}

/**
 * Assemble a full URL from its components.
 * @param {string} path.
 * @param {Object.<string, string>|null} queryDict Keys and Values for the query part.
 * @param {Object.<string, string>|null} fragDict Keys and Values for the fragment part.
 * @return {string} The final URL.
 */
function assembleURL(path, queryDict, fragDict) {
  var key,
  url = path,
  querys = [],
  frags = [];
  for (key in queryDict) {
    if (queryDict[key] == null) {
      queries.push(key + '');
    } else {
      queries.push(key + '=' + encodeURIComponent(queryDict[key]));
    }
  }
  for (key in fragDict) {
    if (fragDict[key] == null) {
      frags.push(key + '');
    } else {
      frags.push(key + '=' + encodeURIComponent(fragDict[key]));
    }
  }
  if (queries.length > 0) {
    url = url + '?' + queries.join('&');
  }
  if(frags.length > 0) {
    url = url + '#' + frags.join('&');
  }
  return url;
};

/**
 * Turn a full URL into components.
 * @param {string} url.
 * @return {Array} [path, dict of query string, dict of fragment].
 */
function parseURL(url) {
  var parseToDict = function(str) {
    var ret = {};
    var tokens = str.split('&');
    for (var i in tokens) {
      var pos = tokens[i].indexOf('=');
      if (pos >= 0) {
        ret[tokens[i].substring(0, pos)] = decodeURIComponent(tokens[i].substring(pos + 1));
      } else {
        ret[tokens[i]] = null;
      }
    }
    return ret;
  };

  var fragDict = {};
  var pos = url.indexOf('#');
  if (pos >= 0) {
    fragDict = parseToDict(url.substring(pos + 1));
    url = url.substring(0, pos);
  }
  var queryDict = {};
  pos = url.indexOf('?');
  if (pos >= 0) {
    queryDict = parseToDict(url.substring(pos + 1));
    url = url.substring(0, pos);
  }
  return [url, queryDict, fragDict];
};

{% endhighlight %}


------------

Basics: How to create a Dictionary with Key/Value pairs
-------

### Creating a Dictionary in JavaScript

While there's no "Dictionary" type in JavaScript, it's actually really easy to create and use a dictionary object. The following steps show how to go about creating and populating a dictionary with Key/Value pairs:


##### Step 1: Create a new Object

{% highlight js %}
var dict = new Object();
// or the shorthand way
var dict = {};
// You can also initialize the Dictionary with Key/Value pairs when creating it if you are using the shorthand method.
var dict = {
  FirstName: "Nicolas",
  "age": 29,
  1: true
};
{% endhighlight %}


##### Step 2: Populate Values on the Object

This can be done by either setting the values using the Object's Indexer property, or just calling it directly as if it were a standard property on the object. As you'll see below the Key values can be any object and are not limited to Strings.

{% highlight js %}
// using the Indexer
dict["age"] = 14;
dict[1] = "one";
// direct property by name
// because it's a dynamic language
dict.FirstName = "Chris";
{% endhighlight %}


### Iterating Key/Value Pairs

A simple JavaScript "for" loop can be used to iterate through your new dictionary.

{% highlight js %}
for(var key in dict) {
  var value = dict[key];
  // do something with "key" and "value" variables
}
{% endhighlight %}


### Access Key/Value Pairs Directly

{% highlight js %}
// using indexer
var name = dict["FirstName"];
// as property
var name = dict.FirstName;
{% endhighlight %}


### Functions can be Key or Value Too!

Here are some examples of using a Function in both Key and Value of a Dictionary:

{% highlight js %}
var dict = {};
var f = function() {
  // do something
};
// setup Function as Value
dict['method'] = f;
// setup Function as Key
dict[f] = 'some value';
// execute Function from Value
dict['method']();
// or
var method = dict.method;
method();
// get value for Key
var val = dict[f];
{% endhighlight %}


{% highlight js %}

{% endhighlight %}





