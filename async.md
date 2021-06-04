# Asynchronous Code, libuv, The Event Loop, Streams, & more...

## JavaScript Aside: JavaScript is Synchronous

**Asynchronous**: More than one process running simultaneously.

Node does things asynchronously, V8 does not.

**Synchronous** One process executing at a time.

JavaScript is synchronous. Node.js is asynchronous.

## Conceptual Aside: Callbacks

**Callback**: A function passed to some other function, which we assume will be invoked at some point.

The function "calls back" invoking the function you give it when it is done doing its work.

## libuv, The Event Loop & Non-Blocking Asynchronous Execution

**Non-Blocking**: Doing other things without stopping your programming from running.

This is made possible by Node's doing things asynchronously.

## Conceptual Aside: Streams & Buffers

**Buffer**: A temporary holding spot for data being moved from one place to another.

Intentionally limited in size.

**Stream**: A sequence of data made available over time.

Pieces of data that eventually combine into a whole.

## Conceptual Aside: Binary Data, Character Sets & Encoding

**Binary Data**: Data stored in binary (sets of 1s and 0s).

The core of the math that computers are based on. Each one or zero is called a ***'bit'*** or ***'binary digit'***.

**Character Set**: A representation of characters as numbers.

Each character gets a number. ***Unicode*** and ***ASCII*** are character sets.

**Character Encoding**: How characters are stored in binary.

The numbers (or ***code points***) are converted and stored in binary.

## Buffers

The buffer holds raw binary data - 1s and 0s.

```
// app.js

// We don't need to require buffer beforehand as Node.js
// makes it available globally.
var buf = new Buffer("Hello", "utf8"); // utf8 is the default encoding
console.log(buf);
console.log(buf.toString());
console.log(buf.toJSON());

// The buffer acts like an array
console.log(buf[2]);

// We can also write data to the buffer
// Buffers are finite, we only have space for
// five characters so the first two will be overwritten
buf.write("wo");  // wollo
console.log(buf.toString());
```

## JavaScript Aside - ES6 Typed Arrays

**byte**: 8 bits (eg. 00010010)

Newer versions of JavaScript do have ways of dealing with raw binary data, where this was note case previously.

```
// app.js

var buffer = new ArrayBuffer(8);
var view = new Int32Array(buffer);

view[0] = 5;
view[1] = 15;
console.log(view);
```

## JavaScript Aside: Callbacks

```
// app.js

function greet(callback) {
    console.log("Hello");
    var data = {
        name: "John Doe",
    }
    // passing a parameter to the callback
    callback(data);
}

greet(function(data) {
    console.log("The callback was invoked!");
    console.log(data.name);
});
```

## Files & fs

**Error-First Callback**: Callbacks that take an error object as their first parameter.

***null if no error, otherise will contain an object defining the error.***This is a standard so we know in what order to place our parameters for our callbacks.

```
// greet.txt

Hello world!
```

```
// app.js

var fs = require("fs");

// This is a synchronous approach to reading a file.
var greet = fs.readFileSync(__dirname + "/greet.txt", "utf8");
console.log(greet); // Hello world

// Asynchronous callback
var greet2 = fs.readFile(__dirname + "/greet.txt", "utf8",
// Using error-first callbacks is a popular pattern in Node.
// The data is returned as a buffer by default, we specify encoding above
// to convert to string
function(err, data) {
    console.log(data);
});

console.log("Done");
```

## Streams

**Chunk**: A piece of data being sent through a stream.

Data is split in 'chunks' and streamed.

Streams are event emitters and has access to methods like 'on' and 'emit'.

**Abstract (Base) Class**: A type of constructor you never work directly with, but inherit from.

We create new custom objects which inherit from the abstract base class.

```
// app.js

var fs = require("fs");

// highWaterMark breaks the chunks into sizes that will fit the buffer
var readable = fs.createReadStream(__dirname + "/greet.txt", { encoding: "utf8", highWaterMark: 4 * 1024 });

// creates a writable stream
var writable = fs.createWriteStream(__dirname + "/greetcopy.txt");

readable.on("data", function(chunk) {
    console.log(chunk.length);
    writable.write(chunk);
});var fs = require("fs");

// highWaterMark breaks the chunks into sizes that will fit the buffer
var readable = fs.createReadStream(__dirname + "/greet.txt", { encoding: "utf8", highWaterMark: 4 * 1024 });

// creates a writable stream
var writable = fs.createWriteStream(__dirname + "/greetcopy.txt");

readable.on("data", function(chunk) {
    console.log(chunk.length);
    writable.write(chunk);
});
```

## Conceptual Aside: Pipes

**Pipe**: Connecting two streams by writing to one stream what is being read from another.

In Node you pipe from a Readable stream to a Writable stream.

## Pipes

**Method Chaining**: A method returns an object so we can keep calling more methods.

Sometimes it returns the parent object (called 'cascading') and sometimes some other object.

```
// app.js

var fs = require("fs");
var zlib = require("zlib");

// highWaterMark breaks the chunks into sizes that will fit the buffer
var readable = fs.createReadStream(__dirname + "/greet.txt");

// creates a writable stream
var writable = fs.createWriteStream(__dirname + "/greetcopy.txt");

var compressed = fs.createWriteStream(__dirname + "/greet.txt.gz")
var gzip = zlib.createGzip();

// Sending chunks of data from readable to writable
// is simpler by using the pipe function
readable.pipe(writable);

readable.pipe(gzip).pipe(compressed);
```