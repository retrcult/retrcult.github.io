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


