---
layout: post
title: Eloquent JavaScript
tags: [JavaScript]
---

[orig.Link](http://eloquentjavascript.net/)

<!--more-->

(Part 1: Language)
========

Values, Types, and Operators
-------





-------

Program Structure
-------

#### Looping a triangle

{% highlight js %}
var total = 8, counter = 1;
var str = '';
for (var a = 1; a <= total; a++) {
  for (var i = 1; i <= counter; i++) {
    str += '#';
  }
  str += '\n';
  counter++;
}
console.log(str);
{% endhighlight %}

##### > answer

{% highlight js %}
for (var line = "#"; line.length < 8; line += "#")
console.log(line);
{% endhighlight %}


#### FizzBuzz

{% highlight js %}
for (var i = 1; i <= 100; i++) {
  if (i%3 === 0 && i%5 === 0) {
    console.log('FizzBuzz');
  } else if (i%3 === 0) {
    console.log('Fizz');
  } else if (i%5 === 0) {
    console.log('Buzz');
  } else {
    console.log(i);
  }
}
{% endhighlight %}

##### > answer

{% highlight js %}
for (var n = 1; n <= 100; n++) {
  var output = "";
  if (n % 3 == 0)
    output += "Fizz";
  if (n % 5 == 0)
    output += "Buzz";
  console.log(output || n);
}
{% endhighlight %}


#### Chess board

{% highlight js %}
var size = 8;
var str = '';

for (var i = 0; i < size*size; i++) {

  if (i%size === 0) str += '\n';

  if ((i+Math.floor(i/size))%2 === 1) str += '#';
  else str += 'O';
};
console.log(str);
{% endhighlight %}

##### > answer

{% highlight js %}
var size = 8;
var board = "";

for (var y = 0; y < size; y++) {
  for (var x = 0; x < size; x++) {
    if ((x + y) % 2 == 0)
      board += " ";
    else
      board += "#";
  }
  board += "\n";
}

console.log(board);
{% endhighlight %}





-------

Functions
-------

### Closure

{% highlight js %}
function multiplier(factor) {
  return function(number) {
    return number * factor;
  };
}

var twice = multiplier(2);
console.log(twice(5));
// 10
{% endhighlight %}


### Recursion

{% highlight js %}
function power(base, exponent) {
  if (exponent == 0)
    return 1;
  else
    return base * power(base, exponent - 1);
}

console.log(power(2, 3));
// 8
{% endhighlight %}

! Running through a simple loop is a lot cheaper than calling a function multiple times.

Consider this puzzle: by starting from the number 1 and repeatedly either adding 5 or multiplying by 3, an infinite amount of new numbers can be produced. How would you write a function that, given a number, tries to find a sequence of such additions and multiplications that produce that number? For example, the number 13 could be reached by first multiplying by 3 and then adding 5 twice, whereas the number 15 cannot be reached at all.

{% highlight js %}
function findSolution(target) {
  function find(start, history) {
    if (start == target)
      return history;
    else if (start > target)
      return null;
    else
      return find(start + 5, "(" + history + " + 5)") ||
             find(start * 3, "(" + history + " * 3)");
  }
  return find(1, "1");
}

console.log(findSolution(24));
// (((1 * 3) + 5) * 3)
console.log(findSolution(13));
console.log(findSolution(15));
{% endhighlight %}

Note that this program doesn’t necessarily find the shortest sequence of operations. It is satisfied when it finds any sequence at all.


#### Minimum

{% highlight js %}
function min(a, b) {
  return a > b ? b : a;
}
{% endhighlight %}

#### Recursion

{% highlight js %}
var isEven = function(num) {
  if (num < 0) num = -num;
  if (num == 0) return true;
  if (num == 1) return false;
  return isEven(num-2);
};
{% endhighlight %}

#### Bean counting

{% highlight js %}
function countBs(str) {
  var count = 0;
  for (var i = 0; i < str.length; i++) {
    if (str.charAt(i) == 'B') count++;
  };
  return count;
}

function countChar(str, chara) {
  var count = 0;
  for (var i = 0; i < str.length; i++) {
    if (str.charAt(i) == chara) count++;
  };
  return count;
}
{% endhighlight %}





-------

Data Structures: Objects and Arrays
-------

##### Properties

The two most common ways to access properties in JavaScript are with a dot and with square brackets. Both value.x and value[x] access a property on value—but not necessarily the same property. The difference is in how x is interpreted. When using a dot, *the part after the dot must be a valid variable name, and it directly names the property*. When using square brackets, *the expression between the brackets is evaluated to get the property name*. Whereas value.x fetches the property of value named “x”, value[x] tries to evaluate the expression x and uses the result as the property name.

##### Methods

Some methods that array objects have:

{% highlight js %}
var mack = [];
mack.push('Mack');
mack.push('the','Knife');
console.log(mack);
// ['Mack', 'the', 'Knife']
console.log(mack.join(' '));
// Mack the Knife
console.log(mack.pop());
// Knife
console.log(mack);
// ['Mack', 'the']

var todoList = [];
function rememberTo(task) {
  todoList.push(task);
}
function whatIsNext() {
  return todoList.shift();
}
function urgentlyRememberTo(task) {
  todoList.unshift(task);
}

function remove(array, index) {
  return array.slice(0, index)
    .concat(array.slice(index + 1));
}
console.log(remove(["a", "b", "c", "d", "e"], 2));
// ["a", "b", "d", "e"]
{% endhighlight %}


name: expression <br>
property: value <br>
key: value <br>


{% highlight js %}
var anObject = {left:1,right:2};
console.log(anObject.left);
delete anObject.left;
console.log(anObject.left);
// undefined
console.log('left' in anObject);
// false
console.log('right' in anObject);
// true

var object1 = {value:10};
var object2 = object1;
var object3 = {value:10};
console.log(object1 == object2);
// true
console.log(object1 == object3);
// false
object1.value = 15;
console.log(object2.value);
// 15
{% endhighlight %}

The binary `in` operator, when applied to a string and an object, returns a Boolean value that indicates whether that object has that property.


{% highlight js %}
function addEntry(squirrel) {
  var entry = {events: [], squirrel: squirrel};
  for (var i = 1; i < arguments.length; i++)
    entry.events.push(arguments[i]);
  journal.push(entry);
}
addEntry(true, "work", "touched tree", "pizza",
         "running", "television");

function hasEvent(event, entry) {
  return entry.events.indexOf(event) != -1;
}

function tableFor(event, journal) {
  var table = [0, 0, 0, 0];
  for (var i = 0; i < journal.length; i++) {
    var entry = journal[i], index = 0;
    if (hasEvent(event, entry)) index += 1;
    if (entry.squirrel) index += 2;
    table[index] += 1;
  }
  return table;
}
console.log(tableFor('pizza', JOURNAL));
// [76, 9, 4, 1]

// ϕ = (n11n00 - n10n01)/√(n1•n0•n•1n•0)
function phi(table) {
  return (table[3]*table[0] - table[2]*table[1]) /
         Math.sqrt((table[2] + table[3]) *
                   (table[0] + table[1]) *
                   (table[1] + table[3]) *
                   (table[0] + table[2]));
}
console.log(phi([76,9,4,1]));
// 0.068599434

// var map = {};
// function storePhi(event, phi) {
//   map[event] = phi;
// }
// storePhi('pizza', 0.069);
// storePhi('touched tree', -0.081);
// console.log('pizza' in map);
// // true
// console.log(map['touched tree']);
// // -0.081
// for (var event in map) {
//   console.log('The correlation for ' + event + ' is ' + map[event]);
// }
// // The correlation for 'pizza' is 0.069
// // The correlation for 'touched tree' is -0.081
function gatherCorrelations(journal) {
  var phis = {};
  for (var entry = 0; entry < journal.length; entry++) {
    var events = journal[entry].events;
    for (var i = 0; i < events.length; i++) {
      var event = events[i];
      if (!(event in phis))
        phis[event] = phi(tableFor(event, journal));
    }
  }
  return phis;
}

for (var event in correlations) {
  var correlation = correlations[event];
  if (correlation > 0.1 || correlation < -0.1)
    console.log(event + ": " + correlation);
}
// weekend:        0.1371988681
// brushed teeth: -0.3805211953
// peanuts:        0.5902679812
for (var i = 0; i < JOURNAL.length; i++) {
  var entry = JOURNAL[i];
  if (hasEvent("peanuts", entry) &&
     !hasEvent("brushed teeth", entry))
    entry.events.push("peanut teeth");
}
console.log(phi(tableFor("peanut teeth", JOURNAL)));
// 1
{% endhighlight %}

{% highlight js %}
function randomPointOnCircle(radius) {
  var angle = Math.random()*2*Math.PI;
  return {
    x: radius * Math.cos(angle),
    y: radius * Math.sin(angle)
  };
}
console.log(randomPointOnCircle(2));
{% endhighlight %}


#### The sum of a range

{% highlight js %}
function range(start, end, step) {
  if (step == undefined) {
    if (start < end) step = 1;
    else step = -1;
  }
  var arr = [];
  for (var i = start; (i-start)*(i-end) <= 0; i += step) {
    arr.push(i);
  }
  return arr;
}

function sum(numbers) {
  var sum = 0;
  for (var i in numbers) {
    sum += numbers[i];
  }
  return sum;
}
{% endhighlight %}


#### Reversing an array

{% highlight js %}
function reverseArray(arr) {
  var a = [];
  for (var i = 0; i < arr.length; i++) {
    a.unshift(arr[i]);
  }
  return a;
}
// alternative
function reverseArray(arr) {
  var a = [];
  for (var i = arr.length-1; i >= 0; i--) {
    a.push(arr[i]);
  }
  return a;
}

function reverseArrayInPlace(arr) {
  for (var i = 0, j = arr.length-1; i < j; i++, j--) {
    var t = arr[i];
    arr[i] = arr[j];
    arr[j] = t;
  }
}
{% endhighlight %}


#### A list

{% highlight js %}
function arrayToList(arr) {
  if (arr.length == 0) return null;
  var a = arr.slice(0);
  return {
    value: a.shift(),
    rest: arrayToList(a)
  };
}
// alternative
function arrayToList(arr) {
  var l = null;
  for (var i = arr.length-1; i >= 0; i--) {
    l = {
      value: arr[i],
      rest: l
    };
  }
  return l;
}

function listToArray(list) {
  var a = [];
  for (var node = list; node; node = node.rest) {
    a.push(node.value);
  }
  return a;
}

function prepend(ele, list) {
  return {
    value: ele,
    rest: list
  };
}

function nth(list, num) {
  for (var node = list, i = 0; node; node = node.rest, i++) {
    if (i == num) return node.value;
  }
}
{% endhighlight %}


#### Deep comparison

{% highlight js %}
function deepEqual(one, two) {
  if (one != null && two != null
   && typeof one == "object"
   && typeof two == "object") {
    if (Object.keys(one).length != Object.keys(two).length)
      return false;
    for (var i in one) {
      if(!deepEqual(one[i],two[i])) return false;
    }
    return true;
  } else {
    return one === two;
  }
}
{% endhighlight %}





-------

Higher-order Functions
-------

Higher-order functions that somehow apply a function to the elements of an array are widely used in JavaScript. The `forEach` method is the most primitive such function.

{% highlight js %}
function forEach(array, action) {
  for (var i = 0; i < array.length; i++)
    action(array[i]);
}
var numbers = [1, 2, 3, 4, 5], sum = 0;
forEach(numbers, function(number) {
  sum += number;
});
console.log(sum);
// 15
{% endhighlight %}

To illustrate how helpful this is, let’s look back at a function from the previous chapter. It contains two array-traversing loops. Working with forEach makes it slightly shorter and quite a bit cleaner.

In fact, we don’t need to write `forEach` ourselves. It is available as a standard method on arrays. Since the array is already provided as the thing the method acts on, `forEach` takes only one required argument: the function to be executed for each element.

{% highlight js %}
function gatherCorrelations(journal) {
  var phis = {};
  journal.forEach(function(entry) {
    entry.events.forEach(function(event) {
      if (!(event in phis))
        phis[event] = phi(tableFor(event, journal));
    });
  });
  return phis;
}
{% endhighlight %}

{% highlight js %}
function unless(test, then) {
  if (!test) then();
}
function repeat(times, body) {
  for (var i = 0; i < times; i++) body(i);
}
repeat(3, function(n) {
  unless(n % 2, function() {
    console.log(n, "is even");
  });
});
// 0 is even
// 2 is even
{% endhighlight %}


### JSON

JavaScript gives us functions, `JSON.stringify` and `JSON.parse`, that convert data from and to this format. The first takes a JavaScript value and returns a JSON-encoded string. The second takes such a string and converts it to the value it encodes.

Filtering an array

{% highlight js %}
function filter(array, test) {
  var passed = [];
  for (var i = 0; i < array.length; i++) {
    if (test(array[i]))
      passed.push(array[i]);
  }
  return passed;
}
console.log(filter(ancestry, function(person) {
  return person.born > 1900 && person.born < 1925;
}));
// [{name: "Philibert Haverbeke", …}, …]
{% endhighlight %}


#### Flattening

{% highlight js %}
arrays.reduce(function(a, b) {
  return a.concat(b);
}, []);
{% endhighlight %}


#### Mother-child age difference

{% highlight js %}
function average(array) {
  function plus(a, b) { return a + b; }
  return array.reduce(plus) / array.length;
}
var byName = {};
ancestry.forEach(function(person) {
  byName[person.name] = person;
});
function hasKnownMother(p) {
  if (byName[p.mother]) return true;
}
function ageDifference(p) {
  return (p.born - byName[p.mother].born);
}
console.log(average(ancestry.filter(hasKnownMother).map(ageDifference)));
// alternative
function ageDifference(p) {
  if (byName[p.mother])
    return (p.born - byName[p.mother].born);
  else
    return null;
}
function filterNull(n) {
  if (n != null) return true;
}
console.log(average(ancestry.map(ageDifference).filter(filterNull)));
{% endhighlight %}


#### Historical life expectancy

{% highlight js %}
function average(array) {
  function plus(a, b) { return a + b; }
  return array.reduce(plus) / array.length;
}
var le = {};
ancestry.forEach(function(person) {
  var age = person.died - person.born;
  var century = Math.ceil(person.died/100);
  if (!le[century]) le[century] = [];
  le[century].push(age);
});
for (var h in le) {
  console.log(h + ':', average(le[h]));
}
//alternative (X)
function groupBy(arra, func) {
  var obj = {};
  arra.forEach(func.bind(null, obj));
  return obj;
}
function groupByCentury(g, p) {
  var century = Math.ceil(p.died/100);
  var age = p.died - p.born;
  if (g[century]) {
    g[century].push(age);
  } else {
    g[century] = [];
    g[century].push(age);
  }
}
// alternative
function groupBy(arra, func) {
  var obj = {};
  arra.forEach(function(a) {
    var key = func(a);
    if (!obj[key]) obj[key] = [];
    obj[key].push(a);
  });
  return obj;
}
function century(p) {
  return Math.ceil(p.died/100);
}
var le = groupBy(ancestry, century);
for (var h in le) {
  console.log(h + ':', average(le[h].map(function(p) {
    return p.died - p.born;
  })));
}
{% endhighlight %}


#### Every and then some

{% highlight js %}
function every(arra, test) {
  for (var i = 0; i < arra.length; i++) {
    if (!test(arra[i]))
      return false;
  }
  return true;
}
function some(arra, test) {
  for (var i = 0; i < arra.length; i++) {
    if (test(arra[i]))
      return true;
  }
  return false;
}
// alternative (X)
function every(arra, test) {
  return arra.reduce(function(a, b) {
    return a && test(b);
  }, true);
}
function some(arra, test) {
  return arra.reduce(function(a, b) {
    return a || test(b);
  }, false);
}
{% endhighlight %}





-------

The Secret Life of Objects
-------

There is a method similar to apply, called call.







{% highlight js %}
{% endhighlight %}







-------

Project: Electronic Life
-------



-------

Bugs and Error Handling
-------



-------

Regular Expressions
-------



-------

Modules
-------



-------

Project: A Programming Language
-------


(Part 2: Browser)
========

JavaScript and the Browser
The Document Object Model
Handling Events
Project: A Platform Game
Drawing on Canvas
HTTP
Forms and Form Fields
Project: A Paint Program
(Part 3: Node)
Node.js
Project: Skill-Sharing Website
Other pages
