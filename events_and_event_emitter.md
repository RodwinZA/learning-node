# Events & The Event Emitter

## Conceptual Aside: Events

**Event**: Something that has happened in our app that we can respond to.

In Node we actually talk about two different kinds of events.
- ***System Events*** from the C++ Core thanks to a library called **libuv**
- ***Custom Events*** from the JavaScript Core. This is the **Event Emitter**

## JavaScript Aside: Object Properties, First Class Functions & Arrays

Two concepts in code that are fundamental to understanding the Event Emitter are:

***Object Properties & Methods***:
```
var obj = {
    greet: "Hello"
}

// Different ways to access a property
console.log(obj.greet);
console.log(obj["greet"]);

var prop = "greet";
console.log(obj[prop]);
```

***Functions & Arrays***:

```
var arr = [];

// Functions are first class in JavaScript so they
// cn be added to arrays the same way as primitives

arr.push(function() {
    console.log("Hello world 1");
});
arr.push(function() {
    console.log("Hello world 2");
});
arr.push(function() {
    console.log("Hello world 3");
});

// Loop through the array and envoke each function
arr.forEach(function(item) {
    item();
});
```

## The Node Event Emitter - Part 1

**Event Listener**: The code that responds to an event.

In JavaScript's case, the listener will be a function.

```
//app.js

var Emitter = require("./emitter");

var emtr = new Emitter();

emtr.on("greet", function() {
    console.log("Somewhere, someone said hello.");
});

emtr.on("greet", function() {
    console.log("A greeting occurred!");
});

console.log("Hello");
emtr.emit("greet");
```

```
// emitter.js

// Building our own emitter module

// Create object using a function constructor
function Emitter() {
    this.events = {}; // one property called events (empty)
}

// Create 'on' method on prototype
Emitter.prototype.on = function(type, listener) {
    // Access property on an object using an array
    // in this case stored inside a variable called
    // type
    this.events[type] = this.events[type] || [];
    // Push listener function into array
    this.events[type].push(listener);
}

// Create 'emit' method on prototype
// "Say that something happened"
Emitter.prototype.emit = function(type) {
    if (this.events[type]) {
        this.events[type].forEach(function(listener) {
            // envokes functions inside array
            listener();
        });
    }
}

module.exports = Emitter;
```

## The Node Event Emitter - Part 2

***Magic String***: A string that has some special meaning in our code.

This is bad because it makes it easy for a typo to cause a bug, and hard for tools to help us find it.

The following pattern works better. (We will use the built-in node event emitter)

```
// app.js

var Emitter = require("events");
var eventConfig = require("./config").events;

var emtr = new Emitter();

emtr.on(eventConfig.GREET, function() {
    console.log("Somewhere, someone said hello.");
});

emtr.on(eventConfig.GREET, function() {
    console.log("A greeting occurred!");
});

console.log("Hello");
// saying manually that the event happened
emtr.emit(eventConfig.GREET);
```

```
// config.js

module.exports = {
    events: {
        GREET: "greet",
        FILESAVED: "filesaved",
        FILEOPENED : "fileopened"
    }
}
```

## JavaScript Aside: Object.create & Prototypes

```
// app.js

var person = {
    firstname: "",
    lastname: "",
    greet: function() {
        return this.firstname + " " + this.lastname;
    }
}

// Just a different way to create an object
var john = Object.create(person);

// The JS engine will find firstname on the john
// object and won't go down the prototype chain to
// the person object.
john.firstname = "John";
john.lastname = "Doe"

// The john object does not have a greet method so the
// JS engine will go down the prototype chain and find it on
//  the person object
console.log(john.greet());
```

## Inheriting From The Event Emitter

```
// app.js

var EventEmitter = require("events");
var util = require("util");

function Greetr() {
    this.greeting = "Hello world!";
}

// Any objects created from Greetr constructor also
// has access to the methods and properties on the prototype
// property of the event emitter
util.inherits(Greetr, EventEmitter);

// We can still add our own extra methods and properties after the fact
Greetr.prototype.greet = function(data) {
    console.log(this.greeting + ": " + data);
    // We also have access to the event emitter now
    // the emit method is found down the prototype
    // chain on the event emitter
    this.emit("greet", data);
}

var greeter1 = new Greetr();

// Inheriting methods like 'on' from the event emitter
greeter1.on("greet", function(data) {
    console.log("Someone greeted!: " + data);
});


greeter1.greet("Rodwin"); // string passed to data parameter
```

## JavaScript Aside: Node, ES6 & Template Literals

***Template Literal***: A way to concatenate strings in JavaScript (ES6).

Easier to work with than a bunch of strings concatenated with "+".

```
// app.js

var name = "John Doe";

var greet = "Hello " + name;
var greet2 = `Hello ${name}` // ES6 Template Literal

console.log(greet);
console.log(greet2);
```

## JavaScript Aside: .call and .apply

// app.js

```
var obj = {
    name: "John Doe",
    greet: function() {
        console.log(`Hello ${ this.name }`);
    }
}

obj.greet();

// Using the call method we let the this keyword point
// to our new object while still being able to use our
// initial method.
// Uses comma seperated parameters
obj.greet.call({
    name: "Jane Doe"
});

// .apply works the same way as .call but parameters
// are applied as an array.
obj.greet.apply({
    name: "Jane Doe"
});
```

## Inheriting From The Event Emitter = Part 2

```
// app.js

var EventEmitter = require("events");
var util = require("util");

function Greetr() {
    // Pass the created object to the event emitter constructor.
    EventEmitter.call(this);
    this.greeting = "Hello world!";
}

// Any objects created from Greetr constructor also
// has access to the methods and properties on the prototype
// property of the event emitter
util.inherits(Greetr, EventEmitter);

// We can still add our own extra methods and properties after the fact
Greetr.prototype.greet = function(data) {
    console.log(this.greeting + ": " + data);
    // We also have access to the event emitter now
    // the emit method is found down the prototype
    // chain on the event emitter
    this.emit("greet", data);
}

var greeter1 = new Greetr();

// Inheriting methods like 'on' from the event emitter
greeter1.on("greet", function(data) {
    console.log("Someone greeted!: " + data);
});


greeter1.greet("Rodwin"); // string passed to data parameter
```

***A more simpler demonstration***:

```
// app2.js

var util = require("util");

function Person() {
    this.firstname = "John";
    this.lastname = "Doe";
}

Person.prototype.greet = function() {
    console.log("Hello " + this.firstname + " " + this.lastname);
}

function Policeman() {
    // Gives us access to the properties of
    // the Person object
    Person.call(this);
    this.badgenumber = "1234";
}

// Policeman inherits methods and properties
// from Person.
util.inherits(Policeman, Person);
var officer = new Policeman();
officer.greet();
```

## JavaScript Aside: ES6 Classes

**Syntactic Sugar**: A feature that only changes how you type something, but nothing changes under the hood.

ES6 Classes are an example of syntactic sugar.

```
// app.js

"use strict";

class Person {
    constructor(firstname, lastname) {
        this.firstname = firstname;
        this.lastname = lastname;
    }

    // This is the same as adding a method to the
    // object's prototype
    greet() {
        console.log("Hello " + this.firstname + " " + this.lastname);       
    }
}

// function Person(firstname, lastname) {
//     this.firstname = firstname;
//     this.lastname = lastname;
// }

// Person.prototype.greet = function() {
//     console.log("Hello " + this.firstname + " " + this.lastname);
// };

var john = new Person("John", "Doe");
john.greet();

var jane = new Person("Jane", "Doe");
jane.greet();
```

## Inheriting From The Event Emitter - Part 3

Rewriting using ES6 Classes

```
// app.js

"use strict";

var EventEmitter = require("events");

class Greetr extends EventEmitter {
    constructor() {
        super();
        this.greeting = "Hello world!";       
    }

    greet(data) {
        console.log(this.greeting + ": " + data);
        this.emit("greet", data);           
    }
}

var greeter1 = new Greetr();

// Inheriting methods like 'on' from the event emitter
greeter1.on("greet", function(data) {
    console.log("Someone greeted!: " + data);
});


greeter1.greet("Rodwin"); // string passed to data parameter
```