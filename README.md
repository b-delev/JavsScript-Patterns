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

[Functions](#Functions)

[Background](#Background)

[Disambiguation of Terminology](#Disambiguation of Terminology)

[Declarations Versus Expressions: Names and Hoisting](#Declarations Versus Expressions: Names and Hoisting)

[Function’s name Property](#Function’s name Property)

[Function Hoisting](#Function Hoisting)

[Callback Pattern](#Callback Pattern)

[A Callback Example](#A Callback Example)

[Callbacks and Scope](#Callbacks and Scope)

[Asynchronous Event Listeners](#Asynchronous Event Listeners)

[Timeouts](#Timeouts)

[Callbacks in Libraries](#Callbacks in Libraries)

[Returning Functions](#Returning Functions)

[Self-Defining Functions](#Self-Defining Functions)

[Immediate Functions](#Immediate Functions)

[Parameters of an Immediate Function](#Parameters of an Immediate Function)

[Returned Values from Immediate Functions](#Returned Values from Immediate Functions)

[Benefits and Usage](#Benefits and Usage)

[Immediate Object Initialization](#Immediate Object Initialization)

[Init-Time Branching](#Init-Time Branching)

[Function Properties—A Memoization Pattern](#Function Properties—A Memoization Pattern)

[Curry](#Curry)

[Function Application](#Function Application)

[Partial Application](#Partial Application)

[Currying](#Currying)

[When to Use Currying](#When to Use Currying)

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
When invoked with new, a constructor function always returns an object; by default it’s the object referred to by this.
```javascript
var Objectmaker = function () {
// this `name` property will be ignored
// because the constructor
// decides to return another object instead this.name = "This is it";
// creating and returning a new object
	var that = {};
	that.name = "And that's that";
	return that;
};
// test
var o = new Objectmaker(); 
console.log(o.name); // "And that's that"
```

<a name="Patterns for Enforcing new"></a>
## Patterns for Enforcing new
```javascript
// constructor function Waffle() {
this.tastes = "yummy"; }
// a new object
var good_morning = new Waffle(); 
console.log(typeof good_morning); // "object" 
console.log(good_morning.tastes); // "yummy"
// antipattern:
// forgotten `new`
var good_morning = Waffle();
console.log(typeof good_morning); // "undefined" 
console.log(window.tastes); // "yummy"
```

<a name="Using that"></a>
## Using that
Following a naming convention can certainly help, but it merely suggests and doesn’t enforce the correct behavior. 
```javascript
function Waffle() { 
	var that = {};
	that.tastes = "yummy";
	return that; 
}
```
For simpler objects, you don’t even need a local variable such as that; you can simply return an object from a literal like so:
```javascript
function Waffle() { 
	return {
		tastes: "yummy" 
	};
}
```

Note that the variable name that is just a convention; it’s not a part of the language. You can use any name, where other common variable names include self and me.


<a name="Self-Invoking Constructor"></a>
## Self-Invoking Constructor
To address the drawback of the previous pattern and have prototype properties avail- able to the instance objects, consider the following approach. 
```javascirpt
function Waffle() {
	if (!(this instanceof Waffle)) {
		return new Waffle(); 
	}
	this.tastes = "yummy";
}
Waffle.prototype.wantAnother = true;
// testing invocations 
var first = new Waffle(),
second = Waffle();

console.log(first.tastes); // "yummy" 
console.log(second.tastes); // "yummy"

console.log(first.wantAnother); // true 
console.log(second.wantAnother); // true
```
Another general-purpose way to check the instance is to compare with arguments.callee
```javascript
if (!(this instanceof arguments.callee)) {
	return new arguments.callee(); 
}
```

<a name="Array Literal"></a>
## Array Literal
Arrays in JavaScript, as most other things in the language, are objects. They can be created with the built-in constructor function Array().
```javascript
// array of three elements
// warning: antipattern
var a = new Array("itsy", "bitsy", "spider");
// the exact same array
var a = ["itsy", "bitsy", "spider"];
console.log(typeof a); // "object", because arrays are objects 
console.log(a.constructor === Array); // true
```

<a name="Array Literal Syntax"></a>
## Array Literal Syntax
There’s not much to the array literal notation: it’s just a comma-delimited list of ele- ments and the whole list is wrapped in square brackets.

<a name="Array Constructor Curiousness"></a>
## Array Constructor Curiousness
One more reason to stay away from new Array() is to avoid a possible trap that this constructor has in store for you.
```javascript
// an array of one element 
var a = [3]; 
console.log(a.length); // 1 
console.log(a[0]); // 3
// an array of three elements
var a = new Array(3); 
console.log(a.length); // 3 
console.log(typeof a[0]); // "undefined"


// using array literal
var a = [3.14]; 
console.log(a[0]); // 3.14
var a = new Array(3.14); // RangeError: invalid array length 
console.log(typeof a); // "undefined"
```

<a name="Check for Array-ness"></a>
## Check for Array-ness
```javascirpt
console.log(typeof [1, 2]); // "object"

Array.isArray([]); // true
// trying to fool the check 
// with an array-like object 
Array.isArray({
	length: 1,
	"0": 1,
	slice: function () {}
}); // false
```
If this new method is not available in your environment, you can make the check by calling the Object.prototype.toString() method. If you invoke the call() method of toString in the context of an array, it should return the string “[object Array]”. If the context is an object, it should return the string “[object Object]”. So you can do some- thing like this:

```javascript
if (typeof Array.isArray === "undefined") { 
	Array.isArray = function (arg) {
		return Object.prototype.toString.call(arg) === "[object Array]"; 
	};
}
```


<a name="JSON"></a>
## JSON
JSON is only a combination of the array and the object literal notation. 
```javascript
{"name": "value", "some": [1, 2, 3]}
```
The only syntax difference between JSON and the object literal is that property names need to be wrapped in quotes to be valid JSON. 
You can check JSON validity here: http://jsonlint.com/

<a name="Working with JSON"></a>
## Working with JSON
```javascript
// an input JSON string
var jstr = '{"mykey": "my value"}';
// antipattern
var data = eval('(' + jstr + ')');
// preferred
var data = JSON.parse(jstr);
console.log(data.mykey); // "my value"


// an input JSON string
var jstr = '{"mykey": "my value"}';
var data = jQuery.parseJSON(jstr); 
console.log(data.mykey); // "my value"


var dog = {
	name: "Fido",
	dob: new Date(),
	legs: [1, 2, 3, 4] 
};
var jsonstr = JSON.stringify(dog);
// jsonstr is now:
// {"name":"Fido","dob":"2010-04-11T22:36:22.436Z","legs":[1,2,3,4]}
```


<a name="Regular Expression Literal"></a>
## Regular Expression Literal
• Using the new RegExp() constructor 

• Using the regular expression literal

```javascript
// regular expression literal 
var re = /\\/gm;
// constructor
var re = new RegExp("\\\\", "gm");
```

<a name="Regular Expression Literal Syntax"></a>
## Regular Expression Literal Syntax
The regular expression literal notation uses forward slashes to wrap the regular expression pattern used for matching. Following the second slash, you can put the pattern modifiers in the form of unquoted letters:
• g—Global matching
• m—Multiline
• i—Case-insensitive matching

```javascript
var re = /pattern/gmi;

var no_letters = "abc123XYZ".replace(/[a-z]/gi, ""); 
console.log(no_letters); // 123
```

<a name="Primitive Wrappers"></a>
## Primitive Wrappers
JavaScript has five primitive value types: number, string, boolean, null, and undefined. 
Build in constructurs: Number(), String(), and Boolean()
```javascript
// a primitive number
var n = 100;
console.log(typeof n); // "number"
// a Number object
var nobj = new Number(100); 
console.log(typeof nobj); // "object"

// avoid these:
var s = new String("my string"); 
var n = new Number(101);
var b = new Boolean(true);
// better and simpler: 
var s = "my string"; 
var n = 101;
var b = true;
```

Number objects have methods such as toFixed() and toExponential(). 
String objects have substring(), charAt(), and toLowerCase() methods (among others) and a length property.

<a name="Error Objects"></a>
## Error Objects
JavaScript has a number of built-in error constructors, such as Error(), SyntaxError(), TypeError(), and others, which are used with the throw statement. 

```javascript
try {
// something bad happened, throw an error 
	throw {
		name: "MyErrorType", // custom error type
		message: "oops",
		extra: "This was rather embarrassing",
		remedy: genericErrorHandler // who should handle it
	};
} catch (e) {
	// inform the user 
	alert(e.message); // "oops"
	// gracefully handle the error
	e.remedy(); // calls genericErrorHandler() 

}
```



<a name="Functions"></a>
##Functions
<a name="Background"></a>
##Background
<a name="Disambiguation of Terminology"></a>
##Disambiguation of Terminology
<a name="Declarations Versus Expressions: Names and Hoisting"></a>
##Declarations Versus Expressions: Names and Hoisting
<a name="Function’s name Property"></a>
##Function’s name Property
<a name="Function Hoisting"></a>
##Function Hoisting
<a name="Callback Pattern"></a>
##Callback Pattern
<a name="A Callback Example"></a>
##A Callback Example
<a name="Callbacks and Scope"></a>
##Callbacks and Scope
<a name="Asynchronous Event Listeners"></a>
##Asynchronous Event Listeners
<a name="Timeouts"></a>
##Timeouts
<a name="Callbacks in Libraries"></a>
##Callbacks in Libraries
<a name="Returning Functions"></a>
##Returning Functions
<a name="Self-Defining Functions"></a>
##Self-Defining Functions
<a name="Immediate Functions"></a>
##Immediate Functions
<a name="Parameters of an Immediate Function"></a>
##Parameters of an Immediate Function
<a name="Returned Values from Immediate Functions"></a>
##Returned Values from Immediate Functions
<a name="Benefits and Usage"></a>
##Benefits and Usage
<a name="Immediate Object Initialization"></a>
##Immediate Object Initialization
<a name="Init-Time Branching"></a>
##Init-Time Branching
<a name="Function Properties—A Memoization Pattern"></a>
##Function Properties—A Memoization Pattern
<a name="Curry"></a>
##Curry
<a name="Function Application"></a>
##Function Application
<a name="Partial Application"></a>
##Partial Application
<a name="Currying"></a>
##Currying
<a name="When to Use Currying"></a>
##When to Use Currying
