---
layout: post
title: Head First in the "A" in "MEAN" full JS stack (2)
---

> This blog is only used to takes some notes while reading Pawel Kozlowski's AngularJS book
"Mastering Web Application Development with AngularJS (2013)" and Andrew Grantk's book "Beginning
AngularJS (2014)". All contents of this articial
are copies from this two books.

## What is AngularJS?

AngularJS is a client-side MVC framework written in JavaScript. It runs in a web
browser and greatly helps us (developers) to write modern, single-page, AJAX-style
web applications. It is a general purpose framework, but it shines when used to write
CRUD (Create Read Update Delete) type web applications.

## Hello World – the AngularJS example

```html
<html>
<head>
  <script src="http://ajax.googleapis.com/ajax/libs/angularjs/1.0.7/angular.js"></script>
</head>
<body ng-app ng-init="name = 'World'">
  <h1>Hello, {{name}}!</h1>
</body>
</html>
```
In the AngularJS, all the special HTML tags and attributes that the framework can
understand and interpret are referred to as directives.

## JavaScript Primer

**Including Scripts on a Page**

- Referencing an External Script

we need some way to tell the web browser that it has to process our JavaScript. To do this,
we use the `script` tag.

```html
<!DOCTYPE html>
<html>

<head>
  <title>JavaScript Primer</title>
</head>

<body>
  <!-- reference the myScript.js script file -->
  <script src="scripts/myScript.js"></script>

</body>

</html>
```
- Using an Inline Script

```
<script>console.log("Hello");</script>
```
**Function**

```html
<!DOCTYPE html>
<html>

<head>
  <title>JavaScript Primer</title>
  <script>
    function mySimpleFunction() {
      console.log("Hello");
    } // self-defined function

    mySimpleFunction(); // call the above function
  </script>
</head>

<body>
</body>

</html>
```

**Objects**

```html
<html>
  <head>
    <title>javascript Object</title>
  </head>
  <body>
  <script>

  //there are several ways to create a js object, here is one option
  var myobject{
    name = "Alice"; // fields
    Gender = "female";
    age = "20";

    myInfo: function(){ // function with no arguments
      console.log(this.Gender);
    }
  };

  console.log(myobject.name);
  myobject.myInfo(); //use object name to call the method directly.

  for (var info in myobject){
    console.log(myobject[info]); //Enumerating object fields
  }

  </script>

  </body>
</html>
```

**Control flow**

> The control flow and condition statement are exactly the same as Java syntax.

## Callbacks

In functional programming, functions are objects that can be passed around just
like any other value in JavaScript.

Here, the "callback function" in this section is diferent from the the "function" section above.

```html
<script>
  var mycallback = function(){
    console.log('hello.world!');
  }

  //use call back function
  function showme(mycallback){
    mycallback();
  }
</script>
```
In the above snippet, we store the nameless function into a variable "mycallback".
So we could just regard the variable "mycallback" is the "reference" to this nameless
function.

Then we pass the variable (or function reference) to another function directly, and
call the function by reference via "call operator" `( )`.

Actually, in real pratice, a common technique is to write the nameless call back function
right there in the method call instead bothering to store it in some variable first, like this:x:


```html
<script>
  function showme(
    function () {
      console.log('hello.world');
    }
  );
</script>
```
## JSON

JavaScript Object Notation, or JSON, is a lightweight data-interchange format. Essentially, it is way of representing
data in a way that is much more compact than XML yet still relatively human and totally machine-readable. If you
need to send data from place to place, or even store it somewhere, JSON is often a good choice.
