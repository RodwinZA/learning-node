# HTTP & Being A Web Server

## Conceptual Aside: TCP/IP

**Protocol**: A set of rules two sides agree on to use when communicating.

Both the client and server are programmed to understand and use that particular set of rules. It's similar to two people from different countries agreeing on a language to speak in.

## Conceptual Aside: Addresses & Ports

**Port**: Once a computer receives a packet, how it knows what program to send it to.

When a program is setup on the operating system to receive packets from a particular port, it is said that the program is 'listening' to that port.

## Conceptual Aside: HTTP

**HTTP**: A set of rules (and a format) for data being transferred on the web.

Stands for ***HyperText Transfer Protocol***. It's a format (of various) defining data being transferred via TCP/IP.

**MIME type**: A standard for specifying the type of data being sent.

Stands for ***Multipurpose Internet Mail Extensions***.
Examples: application/json, text/html, image/jpeg

## http_parser

## Let's Build A Web Server In Node

```
// app.js

var http = require("http");

// createServer function takes a callback function
// The callback is turned into an
http.createServer(function(req, res) {
    
    // Set status code and headers
    res.writeHead(200, { "Content-Type": "text/plain" });
    res.end("Hello world\n");

}).listen(1337, "127.0.0.1");
```

## Outputting HTML & Templates

**Template**: Text designed to be the basis for the final text or contet after being processed.

There's usually some specific template language, so the template system knows how to replace placeholders with real values.

```
<!-- index.html -->

<html>
    <head></head>
    <body>
        <h1>{Message}</h1>
    </body>
</html>
```

```
// app.js

var http = require("http");
var fs = require("fs");

// createServer function takes a callback function
// The callback is turned into an
http.createServer(function(req, res) {
    
    // Set status code and headers
    res.writeHead(200, { "Content-Type": "text/html" });
    // Get contents of the html file.
    // readFileSync returns a buffer that we change into
    // a string by setting the utf8 encoding.
    var html = fs.readFileSync(__dirname + "/index.html", "utf8");
    var message = "Hello world...";
    // We use the string method replace to specify
    // which placeholder we want to change to which
    // string-containing variable
    html = html.replace("{Message}", message);
    res.end(html);

}).listen(1337, "127.0.0.1");
```

## Streams & Performance

The following code demonstrates how to improve performance using streams

```
<!-- index.html -->

<html>
    <head></head>
    <body>
        <h1>Hello world!</h1>
    </body>
</html>
```

```
// app.js

var http = require("http");
var fs = require("fs");

http.createServer(function(req, res) {
    
    // Set status code and headers
    res.writeHead(200, { "Content-Type": "text/html" });
    // Using readFileSync could cause memory issues when url is visited
    // by many people.
    // We already have a writable stream available, the res object.
    // This means we can pipe the writable stream to the readable stream.
    fs.createReadStream(__dirname + "/index.html").pipe(res);

}).listen(1337, "127.0.0.1");
```
In real-world applications use streams wherever you can, and when you don't, make sure it's on purpose.

## Conceptual Aside: APIs & Endpoints

**API**: A set of tools for building a software application.

Stands for ***Application Programming Interface***. On the web the tools are usually made available via a set of URLs which accept and send only data via HTTP and TCP/IP.

**Endpoint**: One URL in a web API.

Sometimes that endpoint (URL) does multiple things by making choices based on the HTTP request headers.

## Outputting JSON

**Serialize**: Translating an object into a format that can be stored or transferred.

JSON, CSV, XML and others are popular. ***Deserialize*** is the opposite (converting the format back into an object).

```
// app.js

var http = require("http");

http.createServer(function(req, res) {
    
    // Set status code and headers
    res.writeHead(200, { "Content-Type": "application/json" });
    var obj = {
        firstname: "John",
        lastname: "Doe"
    }
    // Serialize object to be able to receive object
    // and convert it into a JS object in the browser.
    res.end(JSON.stringify(obj));

}).listen(1337, "127.0.0.1");
```

## Routing

**Routing**: Mapping HTTP requests to content.

Whether actual files that exist on the server, or not.

```
// app.js

var http = require("http");
var fs = require("fs");

http.createServer(function(req, res) {

    if (req.url === "/") {
        fs.createReadStream(__dirname + "/index.html").pipe(res);
    }

    else if (req.url === "/api") {
        // Set status code and headers
        res.writeHead(200, { "Content-Type": "application/json" });
        var obj = {
            firstname: "John",
            lastname: "Doe"
        };
        // Serialize object to be able to receive object
        // and convert it into a JS object in the browser.
        res.end(JSON.stringify(obj));
    }
    else {
        res.writeHead(404);
        res.end();    
    }
    
}).listen(1337, "127.0.0.1");
```