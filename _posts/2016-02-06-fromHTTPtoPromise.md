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

Recall what we discussed in the previous [notes](http://lszhou.github.io/misuseHttp), then we know the answer is,
we put the `return` statement in the callback function and the `all()` function has no return statement,
and by default all functions will return `undefined`. Thanks@[HankScorpio](http://stackoverflow.com/users/4518468/hankscorpio).

More specifically, let's review the above codes carefully. in dataFactory `all()` method, there are two `$http` calls.
This means both of them will return a promise.

```javascript
$http
  .get('/api/hi')
  .then(function(internalData) {

    return 'test2'; // 'test2' can not be printed, and console show 'undefined'
  });
```


```javascript

  $http
    .get('/api/hey')
    .then(function(externalData) {
      // ... promise p1
    });

```
Now let's analyze the two promises carefully and see what do that look like.

The internal `$http` returns a promise object, say `p1` which contains an property called `data` and the value of this property is exactly string `test2`.
From the external `$http` perspective, it receives promise `p1` from the internal `$http`.

Then the external `$http` wrap the internal
promise `p1` to be a new promise, say `p2` whose `data` property holds promise `p1`. In another word, the `all()` method returns a nested
promise.

But this function as well as `all()` method does not any additional operations of this string. So the final solution is also
very straightforward, i.e, all functions return the result of its inner call back functions.

Solution is this:

```javascript

dataFactory.all = function() {
  return $http.get('/api/hey').then(function(response) {
    return $http.get('/api/hi');
  });
}

```
