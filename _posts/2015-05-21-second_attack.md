---
layout: post
title: Second Attack (Node.js/Express)
---

Express is a minimal yet flexible and powerful web development framework for
the Node.js (Node) platform.

(History) It is written by TJ. Holowaychuk. He built Express on top of Connect -an extensible HTTP server framework, developed by him. Connect provides a set of high performance plug-ins known as middleware. Connect includes over 20 commonly used middleware, including a logger, session support, cookieParser, and more.

(Big picture) Here are some of the tasks Express can do for you:

- Routing based on URL paths
- Managing sessions via cookies
- Parsing incoming requests (like form data or JSON)
- Rejecting malformed requests

Each of the above tasks is handled by a middleware shown in Figure [here](http://www.google.ca/imgres?imgurl=http://media.developeriq.in/images/nodeexpress_2_9_2015_1.png&imgrefurl=http://developeriq.in/articles/2015/feb/09/node-expressjs-and-mongoose-part-ii/&h=221&w=462&tbnid=4rsxq57EBVVJgM:&zoom=1&docid=gyoVH2FgVJ-iyM&ei=RhNeVdS2FNLWoATZyoGgAQ&tbm=isch&ved=0CCUQMygJMAk).

## Section 1: The stuff that makes up Express

**The application object**

The application object is an instance of Express, conventionally represented by the
variable named *app* . This is the main object of your Express app and the bulk of the
functionality is built on it. We can Create it by calling the top-level *express()* function
exported by the Express module:

```javascript
//create an instance of the Express module:
var express = require('express');
var app = new express();
```

The *app* object has methods for

- Routing HTTP requests; see for example, app.METHOD and app.param.
- Configuring middleware; see app.route.
- Rendering HTML views; see app.render.
- Registering a template engine; see app.engine.


[Here](http://expressjs.com/api.html) are all the methods and
properties available on the application object *app*.

Currently I am using the following methods:

```javascript
app.get('title'); //.get(name): Returns the value of name app setting
// => undefined

app.set('title', 'My Site');
app.get('title');
// => "My Site"
```

```javascript
//app.get(path, callback [, callback ...])
//Routes HTTP GET requests to the specified path
//with the specified callback functions.

app.get('/', function (req, res) {
  res.send('GET request to homepage');
});
```
```
// app.METHOD(path, callback [, callback ...])
// Routes an HTTP request, where METHOD is the HTTP method of the
//request, such as GET, PUT, POST, and so on, in lowercase. Thus,
//the actual methods are app.get(), app.post(), app.put(), and so on.
```

**The request object**

The HTTP request object is created when a client makes a request to the Express app.
The object is conventionally represented by a variable named *req*, which contains a
number of properties and methods related to the current request.

There are also some methods and properities, for example:

``` javascript
- req.url // The request path, along with any query parameters

- req.get(header) // Gets the request HTTP header
```

**The response object**

The response object is created along with the request object, and is conventionally
represented by a variable named res . While it may sound a little strange that both
of them should be created together, it is a necessity to give all the middlewares a
chance to work on the request and the response object, before passing the control
to the next middleware.

## Section 2: Concepts in Express

**Asynchronous Javascript and Callback functions (callbacks)**

Unlike the more common synchronous functions, asynchronous functions do
not return immediately; at the same time they do not block the execution of its
succeeding code. This means other tasks are not piled up waiting for the current
task to be completed.

However, to resume control from the async operation and to
handle its result, we need to use a callback function. The callback function is passed
to the async function to be executed after the async function is done with its job.

**Node modules**

A Node module is a JavaScript library that can be modularly included in Node
applications using the *require()* function.

There are lots of third party modules built upon Node.
Express, Socket.io and Mongoose are the most popular ones.

What the module is capable of is entirely dependent on the module—it can be
simple helper functions to something more complex such as a web development framework,
which is what Express is.

Thus, programmatically speaking, express works as a ("complex") module in
*app* and is required by *require()* method. All libraries or functionalities or components
fit together via the concept "modules" and *require*.

> The bulk of web server-related functionality in Express is provided by its built-in
middlewares. Features not supported by Express out of the box are implemented
using Node modules.

> Since Express provides just the bare minimum functionality of a web server, it
does not support some common but crucial functionality, such as connecting to
a database, sending e-mails, and so on. In such cases, you will need to find and
install the appropriate Node modules and use them to get your task done.

> The Node community is very active and has developed modules for almost every
requirement on a typical web project. So remember, if you are looking to do something
tricky or complex, probably there is a Node module for it already, if it does not exist,
probably you should create it and share it with the Node community. If you are in no
mood for sharing with others, make it a private Node module and keep it to yourself.

> If it makes you wonder what is the difference between a public and a private
module: public modules can be published on the npm registry and installed by
the general public, whereas private modules remain private.

There are two approaches to writing Node modules: one involves attaching
properties and functions to the exports object, the other involves assigning
JavaScript objects to the module.exports property of a module.


**Middlewares**

(Definition)Express functionality is provided through middleware, which are nothing but
asynchronous functions that manipulate the request and response objects.

(Definition, again) A middleware is a JavaScript function to handle HTTP requests to an Express app.

The functionality could be:

- manipulate the request and the response objects
- or perform an isolated action,
- or terminate the request flow by sending a response to the client,
- or pass on the control to the next middleware, etc.

Middlewares are loaded in an Express app using the *app.use()* method.

```
app.use(function(req, res, next) {
  console.log('Request from: ' + req.ip);
  next();
});
```
A middleware is just a function that accepts three parameters: req ,
res , and next . The req parameter is the request object, the res parameter is the
response object, and the next parameter is a reference to the next middleware in
line.

> Note:

> Any middleware can end a request by sending a response back to the client
using one of the response methods on the res object. Any middleware that does
not call a response method must call the next middleware in line, else the request
will be left hanging in there.

In practice, middlewares will be (1) created as a JavaScript object defined right in the file.
(2) or might be included as a Node module.

In another word, a middleware could be (2) defined first and then passed to the *app.use()* method, for example:

```javascript
//define function/object forbidder first, then pass it to app.use
var forbidder = function(forbidden_day) {
  var days = ['Sunday', 'Monday', 'Tueday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];
  return function(req, res, next) {
    var day = new Date().getDay();
    if (days[day] === forbidden_day) {
      res.send('No visitors allowed on ' + forbidden_day + 's!');
    } else {
      next();
    }
  }
};

app.use(forbidder('Wednesday'));

app.use(app.router);
```

or (2) written as a module, for example:

```javascript
/*----------forbidder.js module file-----------*/
module.exports = function(forbidden_day) {
  var days = ['Sunday', 'Monday', 'Tueday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];
  return function(req, res, next) {
    var day = new Date().getDay();
    if (days[day] === forbidden_day) {
      res.send('No visitors allowed on ' + forbidden_day + 's!');
    } else {
      next();
    }
  }
};

/*----------app.js module file-----------*/
var forbidder = require('./forbidder.js');
app.use(forbidder('Wednesday'));
```

**Request flow**

There is no independent .js file corresponding to each http request, i.e, to load the home page,
there would be a file named home.js , for the contact page, contact.js , and so on.

There is a single entry point for all the requests
coming to the app—via *app.js* —which bootstraps the Express framework.

When an HTTP request arrives at your app, it goes through a stack of middlewares.
All the middlewares in the chain have the capacity to modify the request and the
response object in any form and manner.

Among the middlewares, the most important is the **router middleware**, which gives Express
the capability to define routes and handle them.

The router middleware makes it possible to define flexible URI schema which can be
handled by their respective handler function.

> The destinations of the HTTP request URIs are defined via routes in the app.
Routes are how you tell your app "for this URI, execute this piece of JavaScript
code". The corresponding JavaScript function for a route is called a route handler.

> - Route is defined to be URI schema to access resources

> - Route Handlers are actually some (callback) functions that process the http
request for their respective routes.

It is the responsibility of the route handler to respond to an HTTP request, or pass
it on to another handler function if it does not. Route handlers may be defined in
the app.js file or loaded as a Node module.

Here is a working example of some routes and their handlers defined right in the
app.js file:

```javascript
var http = require('http');
var express = require('express');
var app = express();
app.get('/', function(req, res) {
  res.send('Welcome!');
});
app.get('/hello.text', function(req, res) {
  res.send('Hola!');
});
app.get('/contact', function(req, res) {
  res.render('contact');
});
http.createServer(app).listen(3000, function() {
  console.log('Express server listening on port ' + 3000);
});
```

Defining the routes and their handlers in the app.js file may work fine if the number
of routes is relatively few. It becomes messy if the number of routes starts growing.
That's where defining the routes and their handlers in a Node module comes in
handy. for example:

```javascript
/*---------------routes.js--------------*/
module.exports = function(app) {

  app.get('/', function(req, res) {
    res.send('Welcome!');
  });

  app.get('/hello.text', function(req, res) {
    res.send('Hola!');
  });

  app.get('/contact', function(req, res) {
    res.render('contact');
  });
};

/*---------------app.js----------------*/
var http = require('http');
var express = require('express');
var app = express();
var routes = require('./routes')(app);

http.createServer(app).listen(3000, function() {
  console.log('Express server listening on port ' + 3000);
});
```



**Reference**

- "*Express Web Application Development (2013)*" by Hage Yaapa
