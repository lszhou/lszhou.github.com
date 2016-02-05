---
layout: post
title: Misuse $http service
---

*The $http service is a core Angular service that facilitates communication with the remote HTTP servers via the browser's XMLHttpRequest object or via JSONP.*

\# `req.body`

`req.body` contains key-value pairs of data submitted in the request body, which means when we sent parameters to the backend, the parameters should be formated into JSON data instead of pure `String`, `Number`, etc.

For example, in the service, we have method as follows which helps update the job information:

```javascript

// update files inforamtion
jobFactory.update = function(jobname, newjobname) {
  return $http.put('/api/jobs/' + jobname, newjobname);
};

```

while if we apply this method in controller like this

```javascript

// update job information
var newjobname = 'helloCalgary'

Job
  .update(jobname, newjobname)
  .then(function(data){
    // more processing
  });

```

then you will get an error. Instead, we should wrap data in JSON, like this

`var newjobname = {newjobname: 'helloCalgary'}`


\# DELETE request using ``$http` does not allow for data to be sent in the body of the request.  

The AngularJS shortcut method to perform DELETE request is `delete(url, [config]);` where
`url` is relative or absolute URL specifying the destination of the request and `[config]` is optional configuration object which does not include any params attrs.

For example, if we want to delete db/server data by providing the data label, i.e, we want to delete data with label '2016Salary', we may want to use this block:

```javascript

var label = '2016Salary';
dataFactory.delete = function(label) {
  return $http.delete('/api/data', {label: '2016Salary'});
};

```
then if you execute `console.log(req.body)` in the backend, then you will find only `{}` is printed.

Instead, if you really want to delete that data, the standard RESTful codes should be

```javascript

var label = '2016Salary';
dataFactory.delete = function(label) {
  return $http.delete('/api/data' + label);
};

```

then in the backend, '2016Salary' could be accessed by `req.param.label`.

\# `$http` returns a `promise`

Demo first:

```javascript

// angularjs factory
dataFactory.all = function() {
  return 'hey';
}

// angularjs controller
console.log(Data.all());

```

Guess what we get printed in the console? 'hey'? Yes. This is normal method call as we do anywhere. let's continue!

```javascript

// server api
api.get('/api/hey', function(req, res){
  res.send{
    success: true,
    message: 'hey'
  }
})

// factory
dataFactory.all = function() {
  return $http.get('/api/hey');
};

// angularjs controller
console.log(Data.all());

```
Guess what we get printed in the console? `hey`? Nop. `{success: true, message: 'hey'}`, Nop....Why?

Actually we get a much more complicate object has these properties:

```
data – {string|Object} – The response body transformed with the transform functions.
status – {number} – HTTP status code of the response.
headers – {function([headerName])} – Header getter function.
config – {Object} – The configuration object that was used to generate the request.
statusText – {string} – HTTP status text of the response.

```

This object is called a `promise`. Wait, speak hunman language!

Ok, The CommonJS Promise proposal describes a promise as an **interface** for interacting with an object
that represents the result of an action that is performed **asynchronously**, and may or may not be finished at any given point in time.

Angularjs promise service worths a long independent article to explain. But now let's just remember `$http` returns a promise which wraps our actual return
data `{success: true, message: 'hey'}` in its `data` properties. And more important, this promise has a `.then()` method which could help us
unwrap the promise object and get our data.

So after we call `Data.all()`, just using the following chaining pattern to get the data:

```javascript

Data
  .all()
  .then(function(response){
    console.log(response.data.message); // this will print 'hey'
  })

```
