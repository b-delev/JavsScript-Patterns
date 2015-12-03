# Notes on JavsScript Patterns

###Table of contents
<hr>
[Minimizing Globals](#Minimizing Globals)

[Access to the Global Object](#Access to the Global Object)

[Single var Pattern](#Single var Pattern)

[Hoisting\: A Problem with Scattered vars](#Hoisting\: A Problem with Scattered vars)

[for Loops](#for Loops)

[for-in Loops (enumeration)](#for-in Loops (enumeration))

[(Not) Augmenting Built-in Prototypes](#(Not) Augmenting Built-in Prototypes)

[switch Pattern](#switch Pattern)

[Avoiding Implied Typecasting](#Avoiding Implied Typecasting)

[Avoiding eval()](#Avoiding eval())

[Number Conversions with parseInt()](#Number Conversions with parseInt())

[Indentation](#Indentation)

[Curly Braces](#Curly Braces)

[Opening Brace Location](#Opening Brace Location)

[White Space](#White Space)

[Capitalizing Constructors](#Capitalizing Constructors)

[Separating Words](#Separating Words)

[Writing Comments](#Writing Comments)

[Writing API Docs](#Writing API Docs)

[YUIDoc Example](#YUIDoc Example)

[Writing to Be Read](#Writing to Be Read)

[Minify...In Production](#Minify...In Production)

[Run JSLint](#Run JSLint)

[Object Literal](#Object Literal)

[The Object Literal Syntax](#The Object Literal Syntax)

[Objects from a Constructor](#Objects from a Constructor)

[Object Constructor Catch](#Object Constructor Catch)

[Custom Constructor Functions](# Custom Constructor Functions)

[Constructor’s Return Values](#Constructor’s Return Values)

[Patterns for Enforcing new](#Patterns for Enforcing new)

[Using that](#Using that)

[Self-Invoking Constructor](#Self-Invoking Constructor)

[Array Literal](#Array Literal)

[Array Literal Syntax](#Array Literal Syntax)

[Array Constructor Curiousness](#Array Constructor Curiousness)

[Check for Array-ness](#Check for Array-ness)

[JSON](#JSON)

[Working with JSON](#Working with JSON)

[Regular Expression Literal](#Regular Expression Literal)

[Regular Expression Literal Syntax](#Regular Expression Literal Syntax)

[Primitive Wrappers](#Primitive Wrappers)

[Error Objects](#Error Objects)
<hr>

##JavaScript: Concepts

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

<a name="Minimizing Globals"></a>
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

<a name="ess to the Global Object"></a>
##Access to the Global Object
```javascript
var global = (function () { 
	return this;
}());
```

<a name="ingle var Pattern"></a>
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

<a name="oisting: A Problem with Scattered vars"></a>
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

<a name="or Loops"></a>
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


<a name="or-in Loops (enumeration) "></a>
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

<a name="(Not) Augmenting Built-in Prototypes"></a>
## (Not) Augmenting Built-in Prototypes
Augmenting the prototype property of constructor functions is a powerful way to add functionality.
```javascript
if (typeof Object.protoype.myMethod !== "function") { 
	Object.protoype.myMethod = function () {
	// implementation... 
	};
}
```

<a name="switch Pattern"></a>
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

<a name="Avoiding Implied Typecasting"></a>
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

<a name="Avoiding eval()"></a>
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


<a name="Number Conversions with parseInt()"></a>
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

<a name="Indentation"></a>
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

<a name="Curly Braces"></a>
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
<a name="Opening Brace Location"></a>
## Opening Brace Location
Best practice is to have opening brace on the same row.

``` javasccript

function func() { 
	return {
		name: "Batman" 
		};
}
```

<a name="White Space"></a>
## White Space

Good places to use a white space include:
• After the semicolons that separate the parts of a for loop: for example, for (var i
    = 0; i < 10; i += 1) {...}
    
• Initializing multiple variables (i and max) in a for loop: for (var i = 0, max = 10; i < max; i += 1) {...}

• After the commas that delimit array items: var a = [1, 2, 3];

• After commas in object properties and after colons that divide property names and
their values: var o = {a: 1, b: 2};

• Delimiting function arguments: myFunc(a, b, c)

• Before the curly braces in function declarations: function myFunc() {}

• After function in anonymous function expressions: var myFunc = function () {};

``` javascript
var d = 0,
a = b + 1;
if (a && b && c) { 
	d = a % c;
	a += d; 
}
```

<a name="Capitalizing Constructors"></a>
## Capitalizing Constructors
JavaScript doesn’t have classes but has constructor functions invoked with new:
```javascript
var adam = new Person();
```

<a name="Separating Words"></a>
## Separating Words
For your constructors, you can use upper camel case, as in MyConstructor(), and for function and method names, you can use lower camel case, as in myFunction(), calculateArea() and getFirstName().

Convention of using all-caps for naming variables that shouldn’t change values during the life of the program.
Capital letters (all-caps) for names of global variables.

getName() is meant to be a public method, part of the stable API, whereas _getFirst() and _getLast() are meant to be private.

Following are some varieties to the _private convention:

• Using a trailing underscore to mean private, as in name_ and getElements_()

• Using one underscore prefix for _protected properties and two for __private properties

• In Firefox some internal properties not technically part of the language are available, and they are named with a two underscores prefix and a two underscore suffix, such as __proto__ and __parent__

<a name="Writing Comments"></a>
## Writing Comments

You have to comment your code, even if it’s unlikely that someone other than you will ever touch it. Often when you’re deep into a problem you think it’s obvious what the code does, but when you come back to the code after a week, you have a hard time remembering how it worked exactly.

The most important habit, yet hardest to follow, is to keep the com- ments up to date, because outdated comments can mislead and be much worse than no comments at all.

<a name="Writing API Docs"></a>
## Writing API Docs

For JavaScript there are two excellent tools, both free and open source: 
the JSDoc Toolkit (http://code.google.com/p/jsdoc-toolkit/) and 
YUIDoc (http://yuilibrary.com/projects/yuidoc).

• Writing specially formatted code blocks

• Running a tool to parse the code and the comments

• Publishing the results of the tool, which are most often HTML pages


<a name="YUIDoc Example"></a>
## YUIDoc Example
The contents of app.js starts like this:

``` javascript
/**
* My JavaScript application 
*
* @module myapp
*/
```
Then you define a blank object to use as a namespace:
``` javascript
var MYAPP = {};
```
And then you define an object math_stuff that has two methods: sum() and multi():
``` javascript
/**
* A math utility
* @namespace MYAPP 
* @class math_stuff 
*/
MYAPP.math_stuff = {
/**
* Sums two numbers
*
* @method sum
* @param {Number} a First number
* @param {Number} b The second number
* @return {Number} The sum of the two inputs 
*/
sum: function (a, b) {
	return a + b; 
	},
	/**
	* Multiplies two numbers 
	*
	* @method multi
	* @param {Number} a First number
	* @param {Number} b The second number
	* @return {Number} The two inputs multiplied 
	*/
	multi: function (a, b) {
		return a * b; 
	}
};
```
And that completes the first “class.” Note the highlighted tags:

@namespace
The global reference that contains your object.

@class
A misnomer (no classes in JavaScript) that is used to mean either an object or a constructor function.

@method
Defines a method in an object and specifies the method name.

@param
Lists the arguments that a function takes. The types of the parameters are in curly braces, followed by the parameter name and its description.

@return
Like @param, only it describes the value returned by the method and has no name.

<a name="Writing to Be Read"></a>
## Writing to Be Read
Writing the comments for the API doc blocks is not only a lazy way to provide reference documentation, but it also serves another purpose—improving code quality by making you revisit your code.

<a name="Minify...In Production"></a>
## Minify...In Production
Minification is the process of eliminating white space, comments, and other nonessential parts of the JavaScript code to decrease the size of the JavaScript files that need to be transferred from the server to the browser. 

<a name="Run JSLint"></a>
## Run JSLint
• Unreachable code

• Using variables before they are defined

• Unsafe UTF characters

• Using void, with, or eval

• Improperly escaped characters in regular expressions


# Literals and Constructors

<a name="Object Literal"></a>
## Object Literal
When you think about objects in JavaScript, simply think about hash tables of key- value pairs (similar to what are called “associative arrays” in other languages).

```javascript 
// start with an empty object 
var dog = {};
// add one property 
dog.name = "Benji";
// now add a method 
dog.getName = function () {
	return dog.name; 
};
//Change the values of properties and methods, for example:
dog.getName = function () {
	// redefine the method to return 
	// a hardcoded value
	return "Fido";
};
//Remove properties/methods completely:
delete dog.name;
//Add more properties and methods:
dog.say = function () {
	return "Woof!"; 
};
dog.fleas = true;

//The object literal pattern enables you to add functionality to the object at the time of creation
var dog = {
	name: "Benji",
	getName: function () {
		return this.name; 
		}
};

```

You'll see “blank object” and “empty object” in a few places throughout this book. It’s important to understand that this is for simplicity and there’s no such thing as an empty object in JavaScript. Even the simplest {} object already has properties and methods inherited from Object.prototype. By “empty” we’ll understand an object that has no own properties other than the inherited ones.

<a name="The Object Literal Syntax"></a>
## The Object Literal Syntax
• Wrap the object in curly braces ({ and }). 

• Comma-delimit the properties and methods inside the object. At railing comma
after the last name-value pair is allowed but produces errors in IE, so don’t use it.

• Separate property names and property values with a colon.

• When you assign the object to a variable, don’t forget the semicolon after the
closing }.



<a name="Objects from a Constructor"></a>
## Objects from a Constructor
You can create objects using your own constructor functions or using some of the built- in constructors such as Object(), Date(), String() and so on.
```javascript
// one way -- using a literal 
var car = {goes: "far"};
// another way -- using a built-in constructor 
// warning: this is an antipattern
var car = new Object();
car.goes = "far";
```
<a name="Object Constructor Catch"></a>
## Object Constructor Catch
Following are a few examples of passing a number, a string, and a boolean value to new Object(); the result is that you get objects created with a different constructor:
```javascript
// Warning: antipatterns ahead
// an empty object 
var o = new Object();
console.log(o.constructor === Object); // true

// a number object
var o = new Object(1); 
console.log(o.constructor === Number); // true 
console.log(o.toFixed(2)); // "1.00"

// a string object
var o = new Object("I am a string"); 
console.log(o.constructor === String); // true 
// normal objects don't have a substring()
// method but string objects do 
console.log(typeof o.substring); // "function"

// a boolean object
var o = new Object(true); 
console.log(o.constructor === Boolean); // true
```

<a name=" Custom Constructor Functions"></a>
##  Custom Constructor Functions

```javascript
//Person constructor function could be defined:
var Person = function (name) { 
	this.name = name;
	this.say = function () {
		return "I am " + this.name; 
	};
};

var adam = new Person("Adam"); 
adam.say(); // "I am Adam"
```

<a name="Constructor’s Return Values"></a>
## Constructor’s Return Values

<a name="Patterns for Enforcing new"></a>
## Patterns for Enforcing new

<a name="Using that"></a>
## Using that

<a name="Self-Invoking Constructor"></a>
## Self-Invoking Constructor

<a name="Array Literal"></a>
## Array Literal

<a name="Array Literal Syntax"></a>
## Array Literal Syntax

<a name="Array Constructor Curiousness"></a>
## Array Constructor Curiousness

<a name="Check for Array-ness"></a>
## Check for Array-ness

<a name="JSON"></a>
## JSON

<a name="Working with JSON"></a>
## Working with JSON

<a name="Regular Expression Literal"></a>
## Regular Expression Literal

<a name="Regular Expression Literal Syntax"></a>
## Regular Expression Literal Syntax

<a name="Primitive Wrappers"></a>
## Primitive Wrappers

<a name="Error Objects"></a>
## Error Objects

