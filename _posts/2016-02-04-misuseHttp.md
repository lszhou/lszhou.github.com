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
