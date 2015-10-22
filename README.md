# JavsScript-Patterns


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

console.log("test", 1, {}, [1,2,3]); 
console.dir({one: 1, two: {three: 3}});

It is possible not to use console.log or console.dir when testing in console.

#Minimizing Globals
myglobal = "hello"; // antipattern 
console.log(myglobal); // "hello" 
console.log(window.myglobal); // "hello" 
console.log(window["myglobal"]); // "hello" 
console.log(this.myglobal); // "hello"

Use var each time you declare variable in the function.
Declare all the variables in the very begining of the function.

// antipattern, do not use 
// right-to-left evaluation
function foo() { <br />
	var a = b = 0;
	// ... 
}

#Access to the Global Object
var global = (function () { 
	return this;
}());

#Single var Pattern
function func() { 
	var a = 1,
	b = 2,
	sum = a + b, 
	myobject = {}, 
	i,
	j;
	// function body... 
}

