# Express

## Installing Express & making It Easier To Build A Web Server

**Environment Variables**: Global variables specific to the environment (server) our code is living in.

Different servers can have different variable settings, and we can access those values in code.

**HTTP Method** Specifies the type of action the request wishes to make.

GET, POST, DELETE and others. Also called **verbs**.

```
// app.js

var express = require("express");
var app = express();

// If there is an environment variable the port will
// be set, otherwise it will default to port 3000.
var port = process.env.PORT || 3000;

// Responds to an HTTP request with GET method.
// No need to add Content-Type as Express takes care of this.
app.get("/", function(req, res) {
    res.send("<html><head></head><body><h1>Hello world!</h1></body></html>")
});

app.get("/api", function(req, res) {
    // Express serializes automatically.
    res.json({ firstname: "John", lastname: "Doe" });
});

app.listen(port);
```

## Routes

Express has pattern matching and offers full flexibility on working with routes.

```
// app.js

var express = require("express");
var app = express();

var port = process.env.PORT || 3000;

app.get("/", function(req, res) {
    res.send("<html><head></head><body><h1>Hello world!</h1></body></html>");
});

// Adding a route with a variable, id is variable name.
app.get("/person/:id", function(req, res) {
    res.send('<html><head></head><body><h1>Person: ' + req.params.id + '</h1></body></html>');
});

app.get("/api", function(req, res) {
    res.json({ firstname: "John", lastname: "Doe" });
});

app.listen(port);
```

## Static Files & Middleware

**Middleware**: Code that sits between two layers of software.

In the case of Express, sitting between the request and response.

**Static**: Not dynamic.

In other words, not processed by code in any way. For example HTML, CSS and image files are ***static*** files.

```
// app.js

var express = require("express");
var app = express();

var port = process.env.PORT || 3000;

// Using middleware
app.use("/assets", express.static(__dirname + "/public"));

// We can create our own middleware
// When you omit the route the middleware will
// run for every request no matter the route.
app.use("/", function(req, res, next) {
    console.log("Request Url: " + req.url);
    next();
});

app.get("/", function(req, res) {
    res.send("<html><head><link href=assets/style.css type=text/css rel=stylesheet></head><body><h1>Hello world!</h1></body></html>");
});

app.get("/person/:id", function(req, res) {
    res.send('<html><head></head><body><h1>Person: ' + req.params.id + '</h1></body></html>');
});

app.get("/api", function(req, res) {
    res.json({ firstname: "John", lastname: "Doe" });
});

app.listen(port);
```

## Templates & Template Engines

```
// app.js

var express = require("express");
var app = express();

var port = process.env.PORT || 3000;

// Set our templating engine.
// Views are looked for in a folder called views by default.
app.set("view engine", "ejs");

app.use("/assets", express.static(__dirname + "/public"));

app.use("/", function(req, res, next) {
    console.log("Request Url: " + req.url);
    next();
});

app.get("/", function(req, res) {
    res.render("index");
});

app.get("/person/:id", function(req, res) {
    res.render("person", { ID: req.params.id });
});

app.get("/api", function(req, res) {
    res.json({ firstname: "John", lastname: "Doe" });
});

app.listen(port);
```


## Querystring & Post Parameters

```
<!-- index.ejs -->

<html>
    <head>
        <link rel="stylesheet" href="/assets/style.css">
        <script src="https://code.jquery.com/jquery-1.11.3.min.js"></script>
    </head>
    <body>
        Hello world!
        <form action="/person" method="POST">
            First Name: <input type="text" id="firstname" name="firstname"> <br>
            Last Name: <input type="text" id="lastname" name="lastname"> <br>
            <input type="submit" value="Submit">
        </form>

        <script>
            $.ajax({
                type: "POST",
                url: "/personjson",
                data: JSON.stringify({ firstname: "Jane", lastname: "Doe"}),
                dataType: "json",
                contentType: "application/json"
            })
        </script>
    </body>
</html>
```

```
// app.js

var express = require("express");
var app = express();

var port = process.env.PORT || 3000;

// Set up bodyParser
var urlencodedParser = express.urlencoded({ extended: false });
var jsonParser = express.json();

// Set our templating engine.
// Views are looked for in a folder called views by default.
app.set("view engine", "ejs");

app.use("/assets", express.static(__dirname + "/public"));

app.use("/", function(req, res, next) {
    console.log("Request Url: " + req.url);
    next();
});

app.get("/", function(req, res) {
    res.render("index");
});

app.get("/person/:id", function(req, res) {
    res.render("person", { ID: req.params.id, Qstr: req.query.qstr });
});


// use bodyPsarser middleware
app.post("/person", urlencodedParser, function(req, res) {
    res.send("Thank you!");
    console.log(req.body.firstname);
    console.log(req.body.lastname);
});

app.post("/personjson", jsonParser, function(req, res) {
    res.send("Thank you for the JSON data");
    console.log(req.body.firstname);
    console.log(req.body.lastname);
});


app.get("/api", function(req, res) {
    res.json({ firstname: "John", lastname: "Doe" });
});

app.listen(port);
```

## RESTful APIs & JSON

**REST**: An architectural style for building APIs.

Stands for ***Representational State Transfer***. We decide that HTTP verbs and URLs mean something.

```
// app.js

var express = require("express");
var app = express();

var port = process.env.PORT || 3000;

// Set up bodyParser
var urlencodedParser = express.urlencoded({ extended: false });
var jsonParser = express.json();

// Set our templating engine.
// Views are looked for in a folder called views by default.
app.set("view engine", "ejs");

app.use("/assets", express.static(__dirname + "/public"));

app.use("/", function(req, res, next) {
    console.log("Request Url: " + req.url);
    next();
});

app.get("/", function(req, res) {
    res.render("index");
});

app.get("/person/:id", function(req, res) {
    res.render("person", { ID: req.params.id, Qstr: req.query.qstr });
});


// use bodyPsarser middleware
app.post("/person", urlencodedParser, function(req, res) {
    res.send("Thank you!");
    console.log(req.body.firstname);
    console.log(req.body.lastname);
});

app.get("/api/person/:id", function(req, res) {
    // get that data from database
    res.json({ firstname: "John", lastname: "Doe" });
});

app.post("/api/person", jsonParser, function(req, res) {
    // save to database
});

app.delete("/api/person/:id", function(req, res) {  
    // delete from database
});


app.listen(port);
```

## Structuring An App

There are many options for structuring an express app.

One of these is to use Express Generator

```
npm install express-generator -g
```

It generates a directory structure with particular files

```
.
├── app.js
├── bin
│   └── www
├── package.json
├── public
│   ├── images
│   ├── javascripts
│   └── stylesheets
│       └── style.css
├── routes
│   ├── index.js
│   └── users.js
└── views
    ├── error.pug
    ├── index.pug
    └── layout.pug

7 directories, 9 files
```

We can also use the MVC Pattern among many others.