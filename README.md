# Notes on JavsScript Patterns


JavaScript: Concepts

Object-Oriented

Native
Described in the ECMAScript standard

built-in (for example, Array, Date)
user-defined (var o = {};).

Host
Defined by the host environment (for example, the browser environment)
window and all the DOM objects

No Classes

Prototypes

```javascript
console.log("test", 1, {}, [1,2,3]); 
console.dir({one: 1, two: {three: 3}});
```

It is possible not to use console.log or console.dir when testing in console.

##Minimizing Globals
```javascript
myglobal = "hello"; // antipattern 
console.log(myglobal); // "hello" 
console.log(window.myglobal); // "hello" 
console.log(window["myglobal"]); // "hello" 
console.log(this.myglobal); // "hello"
```

Use var each time you declare variable in the function.
Declare all the variables in the very begining of the function.

```javascript
// antipattern, do not use 
// right-to-left evaluation
function foo() {
	var a = b = 0;
	// ... 
}
```

##Access to the Global Object
```javascript
var global = (function () { 
	return this;
}());
```

##Single var Pattern
```javascript
function func() { 
	var a = 1,
	b = 2,
	sum = a + b, 
	myobject = {}, 
	i,
	j;
	// function body... 
}
```

DOM (Document Object Model) references. 
You can assign DOM references to local variables together with the single declaration.

```javascript
function updateElement() {
	var el = document.getElementById("result"),
	style = el.style;
	// do something with el and style...
}
```

##Hoisting: A Problem with Scattered vars

For JavaScript, as long as a variable is in the same scope (same function), it’s considered declared, even when it’s used before the var declaration. 

```javascript
myname = "global"; // global variable 
function func() {
    alert(myname); // "undefined" 
    var myname = "local"; 
    alert(myname); // "local"
} 
func();
```

The preceding code snippet will behave as if it were implemented like so:

```javascript
myname = "global"; // global variable function func() {
var myname; // same as -> var myname = undefined;
alert(myname); // "undefined"
myname = "local";
alert(myname); // "local" }
func();
```

##for Loops
In for loops you iterate over arrays or array-like objects such as arguments and HTMLCollection objects. The usual for loop pattern looks like the following:

```javascript
// sub-optimal loop
for (var i = 0; i < myarray.length; i++) {
	// do something with myarray[i] 
}
```

HTMLCollections are objects returned by DOM methods such as:
• document.getElementsByName()
• document.getElementsByClassName() 
• document.getElementsByTagName()

document.images
	All IMG elements on the page
document.links
	All A elements
document.forms
	All forms
document.forms[0].elements
	All fields in the first form on the page

DOM operations are expensive in general.
Cache the length and declare the length variable out of the loop.

```javascript
function looper() { 
	var i = 0,
	max,
	myarray = [];
	// ...
	for (i = 0, max = myarray.length; i < max; i++) {
		// do something with myarray[i] 
	}
}
```

The faster way:

```javascript
The first modified pattern is:
var i, myarray = [];
for (i = myarray.length; i--;) {
	// do something with myarray[i] 
}

And the second uses a while loop: 

var myarray = [],
i = myarray.length;

while (i--) {
	// do something with myarray[i] 
}
```


##for-in Loops (enumeration) 

Used for loop through objects.

It’s important to use the method hasOwnProperty() when iterating over object properties to filter out properties that come down the prototype chain.

```javascript
// the object 
var man = {
	hands: 2, legs: 2, heads: 1
};

// for-in loop
for (var i in man) {
	if (man.hasOwnProperty(i)) { // filter
		console.log(i, ":", man[i]);
	}
}
```

Also to avoid the long property lookups all the way to Object, you can use a local variable to “cache” it:
```javascript
var i,
hasOwn = Object.prototype.hasOwnProperty;
for (i in man) {
	if (hasOwn.call(man, i)) { 
		// filter
		console.log(i, ":", man[i]); 
	}
}
```

## (Not) Augmenting Built-in Prototypes
Augmenting the prototype property of constructor functions is a powerful way to add functionality.
```javascript
if (typeof Object.protoype.myMethod !== "function") { 
	Object.protoype.myMethod = function () {
	// implementation... 
	};
}
```

## switch Pattern
``` javascript
var inspect_me = 0, result = '';
switch (inspect_me) { 
case 0:
	result = "zero";
break; 
case 1:
	result = "one";
break; 
default:
	result = "unknown"; 
}
```

## Avoiding Implied Typecasting
To avoid confusion caused by the implied typecasting, always use the === and !== operators that check both the values and the type of the expressions you compare

``` javascript
var zero = 0;
if (zero === false) {
	// not executing because zero is 0, not false 
}

// antipattern
if (zero == false) {
	// this block is executed... 
}
```

## Avoiding eval()

If you spot the use of eval() in your code, remember the mantra “eval() is evil.” This function takes an arbitrary string and executes it as JavaScript code.

```javascript
// antipattern
var property = "name"; 
alert(eval("obj." + property));

// preferred
var property = "name"; 
alert(obj[property]);
```

It’s also important to remember that passing strings to setInterval(), setTimeout(), and the Function() constructor is, for the most part, similar to using eval() and there- fore should be avoided.

``` javascript 
// antipatterns 
setTimeout("myFunc()", 1000); 
setTimeout("myFunc(1, 2, 3)", 1000);

// preferred 
setTimeout(myFunc, 1000); 
setTimeout(function () {
	myFunc(1, 2, 3); 
}, 1000);
```


## Number Conversions with parseInt()
Using parseInt() you can get a numeric value from a string. 
``` javascript
var month = "06", year = "09";
month = parseInt(month, 10); 
year = parseInt(year, 10);
```
Alternative ways to convert a string to a number include:
``` javascript
+"08" // result is 8 
Number("08") // 8
```

## Indentation
Code without indentation is impossible to read.
``` javascript 
function outer(a, b) { 
		var c = 1,
		d = 2,
		inner; 
	if (a > b) {
		inner = function () { 
			return {
				r: c - d 
			};
		};
	} else {
		inner = function () { 
			return {
				r: c + d 
			};
		}; 
	}
		return inner; 
}
```

## Curly Braces
Curly braces should always be used, even in cases when they are optional. 
``` javascript 
// bad
if (true)
alert(1); 
else
alert(2);

// better 
if (true) {
	alert(1); 
} else {
	alert(2); 
}


```
