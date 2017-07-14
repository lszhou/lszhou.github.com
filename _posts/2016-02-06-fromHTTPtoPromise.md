---
layout: post
title: From $http to $defer, $q and Pormise, Part I
---

CommonJS has the following definition for `promise`:

> Promises provide a well-defined **interface** for interacting with an object that represents **the result of an action** that is performed **asynchronously**, and may or may not be finished at any given point in time. By utilizing a standard interface, different components can return promises for asynchronous actions and consumers can utilize the promises in a predictable manner. Promises can also provide the fundamental entity to be leveraged for more syntactically convenient language-level extensions that assist with asynchronicity.

Simply speaking, a promise represents the evential result of an asynchronous operation. In current event-driven, non-blocking NodeJS-based web applications, we highly depend on
call back functions to improve the asynchrony. Such callbacks help application get high efficiency but also make the program much more confusing.

As [HankScorpio](http://stackoverflow.com/users/4518468/hankscorpio) pointed, there are many issues we encounted are about dealing with an asynchronous function that will return data at an unknown time in the future (as opposed to synchronous functions, which return data immediately to be available to the next line of code).

Actually, during the development, we do need some guarantee of the synchrony. In this series, we are going to see how to implement such "synchrony" and see why it is necessary.

---

Let's start from a Stack Overflow Q&A.

**\# Base Question**

If we define the factory method like this:

```javascript

dataFactory.all = function() {
  return 'test1';
}

```
and call it in controller:

```javascript

// inject dataFactory first
console.log(Data.all());

```

Then we can definitely get `test1` printed in the console.
However, if adding some logic in the above factory `all()` method like this:

```javascript

dataFactory.all = function() {

  $http
    .get('/api/hey')
    .then(function(externalData) {

      $http
        .get('/api/hi')
        .then(function(internalData) {

          return 'test2'; // 'test2' can not be printed, and console show 'undefined'
        });
    });
   //return 'test1'; //can be printed;
};

```

then the 'test2' can not be printed via the controller. Why?

We put the `return` statement in the callback function and the `all()` function has no return statement,
and by default all functions will return `undefined`. Thanks@[HankScorpio](http://stackoverflow.com/users/4518468/hankscorpio).

More specifically, as we know $http returns a promise. In dataFactory `all()` method, there are two `$http` calls.
This means both of them will return a promise.

The inside $http method call is

```javascript
$http
  .get('/api/hi')
```

which will return a promise which wraps the data fetched from url `'/api/hi'`. As we also know, AngularJS promise has a `then()` method and
the prototype looks like this `then(successCallback, errorCallback, notifyCallback)`. Note that The `successCallback, errorCallback, notifyCallback` can all be ignored, or passed `null` if you have no action to be performed. So here we have

```javascript
$http
  .get('/api/hi')
  .then(function(internalData) {

    return 'test2'; // 'test2' can not be printed, and console show 'undefined'
  });
```
In the `then` method, we did nothing for the data fetching from `'/api/hi'` but just return a string `test2`. So the result for the whole block execution
is `test2`.

Now the above codes could be simplified as

```javascript
dataFactory.all = function() {

  $http
    .get('/api/hey')
    .then(function(externalData) {

      var str = 'test2';
    });
};

```

So if want this block return this string as well we have to return the string using `return` statement like this:

```javascript
dataFactory.all = function() {

  $http
    .get('/api/hey')
    .then(function(externalData) {

       return 'test2'; // namely, return str = 'test2'; or return 'test2';
    });
};

```

Then `all()` method could be further simplified as:

```javascript
dataFactory.all = function() {

  var str0 = 'test2';
};
```

What to do next? Right, return again:

```javascript
dataFactory.all = function() {

  var str0 = 'test2';

  return str0; // or just return 'test2';
};
```

So finally we get the solution which is quite straightforward, just adding the missed `return` statement.

```javascript

dataFactory.all = function() {
  return $http.get('/api/hey').then(function(internalData) {
    return $http.get('/api/hi').then(function(externalData) {
      return 'test';
    });
  });
}

```
