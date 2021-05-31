# Modules, Exports & Require

## Conceptual Aside: Modules

**Module**: A reusable block of code whose existence does not accidentally impact other code.

**CommonJS Modules**: An agreed upon standard for how code modules should be structured.

## JavaScript Aside: First-Class Functions and Function Expressions

**First-Class Functions**: Everything that you can do with other types you can do with functions.

You can use functions like strings, numbers, etc. (i.e pass them around, set variables equal to them, put them in arrays, and more.)

**An Expression**: A block of code that results in a value.

Function expressions are possible in JavaScript because functions are first-class.

```
// Function Statement
function greet() {
    console.log("Hi");
}

greet();

// Functions are first-class
// We can pass a function to a function
// because functions are first-class

function logGreeting(fn) {
    fn();
}
// the greet function is passed to the
// logGreeting function

logGreeting(greet);

// Function Expression

// A function can be assigned to a variable just
// like any other type.

const greetMe = function() {
    console.log("Hi Tony");
}

greetMe()

logGreeting(greetMe);


// Use a function expression on the fly.
// A pattern you will often see.
// There's no value in setting the function equal
// to a variable if it will only be used once.

logGreeting(function() {
    console.log("Hello Tony!");
})

// above function is created on the fly and passed to logGreeting()
```

## Let's Build A Module

```
// greet.js
// app.js does not have access to modify and
// change this function, it can only run it
// the module is self-contained.
var greet = function() {
    console.log("Hello");
};

// If we don't expose our function using module.exports
// we can run it from our file like below.
greet();

// By using module.exports you can expose certain
// parts of your code to thee access to modify and
// change this function, it can only run it
// the module is self-contained app.js file.
module.exports = greet;
```

// app.js
```
// importing a module
// ./ tells our app to look for the file in the same directory
// as our app file
// we import a file called greet.js so we can run
// its code from our app file
// by setting a avariable name we can access the
// exposed greet function
var greet = require("./greet.js")

// With our exposed function inside greet.js we can
// now call the greet function inside app.js
greet();
```
## JavaScript Aside: Objects and Object Literals

**Name/Value Pair**: A name which maps to a value.

The name may be defined more than once, but only can have one value in any given **context**.
That value may be more name/value pairs.

**Object**: A collection of name/value pairs.

**Object Literal**: Name/Value pairs seperated by commas and surrounded by curly braces.

This is just a quick, shorthand way to create JavaScript objects in code.

```
var person = {
    firstname: "John",
    lastname: "Doe",
    greet: function() {
        console.log("Hello, " + this.firstname + " " + this.lastname);
    }
}

// There are two ways to access the properties or methods of an object.
person.greet();

console.log(person["firstname"]);
```

## JavaScript Aside: Prototypal Inheritance and Function Constructors

**Inheritance**: One object gets access to the properties and methods of another object.

**Function Constructors**: A normal function that is used to construct objects.

The 'this' variable points to a new empty object, and that object is returned from the function automatically.

```
function Person(firstname, lastname) {
    this.firstname = firstname;
    this.lastname = lastname;
}

// Functions are special kinds of objects that also has
// properties and methods.
// We can attach other methods and properties to objects by using
// the prototype method.
Person.prototype.greet = function () {
    console.log("Hello, " + this.firstname + " " + this.lastname);
}

var john = new Person("John", "Doe");
// The JavaScript engine searches down the prototype chain
// and looks for the greet function.
john.greet();

// We can create a new Person and it will share the same prototype
var jane = new Person("Jane", "Doe");
jane.greet();

// The prototype object is the same for john and john because
// they were created from the same function constructor
console.log(john.__proto__ === jane.__proto__); // true
```

## JavaScript Aside: By Reference and By Value

**Primitive**: A type of data that represents a single value.

Like a number or a string. In other words, **not an object**.

```
// Pass By Value
function change(b) {
    b = 2;
}

var a = 1;
change(a);
console.log(a); // 1

// Pass By Reference
function changeObj(d) {
    d.prop1 = function() {};
    d.prop2 = {};
}

var c = {};
c.prop1 = {};
changeObj(c);
console.log(c);
```

## JavaScript Aside: Immediately Invoked Function Expressions (IIFEs)

**Scope**: Where in your code you have access to a particular variable or function.

```
var firstname = "Jane";

// Code inside the IIFE is scoped to the function
// and cannot be accessed on the outside (globally)
(function(lastname) {
    var firstname = "John";
    console.log(firstname);
    console.log(lastname);
}("Doe"));

console.log(firstname); // Jane
```

## How Do Node Modules Really Work? module.exports and require

**require** is a function, that you pass a 'path' too.
**module.exports** is what the require function ***returns***

This works because **your code is actually wrapped in a function** that is given these things as function parameters.

## JavaScript Aside: JSON

**JSON**: "JavaScript Object Notation" - A standard for structuring data that is inspired by JavaScript Object Literals.

JS engines are built to understand it.

```
{
    "firstname": "John",
    "lastname": "Doe",
    "address": {
        "street": "101 Main St.",
        "city": "New York",
        "state": "NY"
    }
}
```
You can take objects and convert them to JSON and vice versa.

## More on require

When Node cannot find a file if using ***require***, it looks for a folder with the given string inside ***require*** and for an index.js file inside of it.

```
// app.js

// Node will look for a folder called greet because
// there is no greet.js file and this folder has to contain
// an index.js file.
var greet = require("./greet");

greet.english();
greet.spanish();
```

```
// greet/index.js

var english = require("./english");
var spanish = require("./spanish");

// Requires an object with two methods
module.exports = { english, spanish }
```

```
// greet/english.js

var greetings = require("./greetings.json");

var greet = function() {
    // Grabs the containing information from the greetings.json file
    console.log(greetings.en);
}

module.exports = greet;
```

```
// greet/spanish.js

var greetings = require("./greetings.json");
var greet = function() {
    console.log(greetings.es);
}

module.exports = greet;
```

```
// greet/greetings.json

{
    "en": "Hello",
    "es": "Hola"
}
```

## Module Patterns

***First Pattern***:

```
// app.js


// The variable greet points to the function
// inside greet1.js and can therefore be called
// below using parenthesis.
var greet1 = require("./greet1");

greet1();
```

```
// greet1.js

// Overriding our empty exports object with
// a single function.
module.exports = function() {
    console.log("Hello world");
}
```

***Second Pattern***:

```
// app.js

// The function returns module.exports object and 
// we can request the greet property on the object.
var greet2 = require("./greet2").greet;

greet2();
```

```
// greet2.js

// Adding a method called greet to
// our module.exports object
module.exports.greet = function() {
    console.log("Hello world!");
};
```

***Third Pattern***:

```
// app.js

var greet3 = require("./greet3");

greet3.greet();
```

```
// greet3.js

// Create and onject using a function constructor
function Greetr(params) {
    this.greeting = "Hello world!!";
    this.greet = function() {
        console.log(this.greeting);
    }
}

// Replacing the empty module.exports object
// with our own Greetr object.
module.exports = new Greetr();
```

***Fourth Pattern***

```
// app.js

// Greet4 is now a function contsructor that can
// create new objects
var Greet4 = require("./greet4");

// Creates new Greet4 object
var grtr = new Greet4();
grtr.greet();
```

```
// greet4.js

// Create an object using a function constructor
function Greetr(params) {
    this.greeting = "Hello world!!!";
    this.greet = function() {
        console.log(this.greeting);
    }
}

// Instead of creating the new object using new Greetr()
// we just return the ability to create it (the function constructor)
module.exports = Greetr;
```

***Fifth Pattern***:

**Revealing Module Pattern**: Exposing only the properties and methods you want via a returned object.

A very common and clean way to structure and protect code within modules.

```
// app.js

var greet5 = require("./greet5");

greet5.greet();
```

```
// greet5.js

// The revealing module pattern

var greeting = "Hello world!!!!";

function greet() {
    console.log(greeting);
}

module.exports = {
    // exposes only the greet function and not
    // the greeting variable
    greet: greet
}
```

## Exports vs module.exports

***exports*** is a short-hand for ***module.exports***, it is two variables pointing to the same spot in memory.

***exports*** does not work for all the module patterns.

There is a quirk in JS in that when a variable is set equal to a value it creates a new spot in memory. This breaks the reference between ***module.exports*** and ***exports***.

```
// app.js

var greet = require("./greet");
```

```
exports = function() {
    console.log("Hello");
}

console.log(exports); // [Function: exports]
console.log(module.exports); // {}
```

## Requiring Native (Core) Modules

```
// app.js

// we don't need to use ./ to access core modules
var util = require("util");

var name = "Rodwin";
var greeting = util.format("Hello, %s", name);
util.log(greeting);

// logs string with timestamp
```