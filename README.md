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