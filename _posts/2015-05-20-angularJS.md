---
layout: post
title: Head First in the "A" in "MEAN" full JS stack 
---

```html
<!--Keywords:
- Directives, Filters, and Data Binding;
- View, Controller and Scope;
- Modules, Routes and Factories; (modules are containers)
-->

<html ng-app="flapperNews" ng-controller="MainCtrl">

<head>
  <!--Webpage titile-->
  <title>My Angular App!</title>
  <!--Load the angularJS framework-->
  <script src="http://ajax.googleapis.com/ajax/libs/angularjs/1.3.10/angular.min.js"></script>
  <!--Load the javascript file app.js-->
  <script src="app.js"></script>
</head>

<body>

  <h3>Test, test, test</h3>
  <h2>Test, test, test</h2>
  <p>Paragraph one</p>
  <p>Paragraph two</p>

  <!--empty html form-->
  <input type="text" />

  <!--New line here-->
  <br/>

  <!--create a html form with default content-->
  <input type="text" value="Please type here">
  <br>

  <!--create an empty html form, and assign the input to be content-->
  <!--exercise of angularJS directives, model-->
  <input type="text" ng-model="content" />{{content}}
  <br/>

  <!--Build a button, and display the valuem but there is no response-->
  <input type="submit" value="I want to submit now">


  <!--An HTML form with two input fields and one submit button, no http response is provided.-->
  <form action="XXX.asp" method="get">
    First name: <input type="text" name="fname"> <br>
    Last name: <input type="text" name="lname"> <br>
    <input type="submit" value="Submit it now">
  </form>

<!--exercise of angularJS directives, ng-init and ng-repeat-->
<!--ng-repeat usage: <div ng-repeat="(key, value) in myObj"> ... </div> -->
<!--https://docs.angularjs.org/api/ng/directive/ngRepeat-->
  <div ng-init="names = ['Tom', 'Alice', 'Bob', 'longsheng']">
    <ul>
      <li ng-repeat="NAME in names"> {{NAME}}</li>
    </ul>
  </div>


<div ng-init="Gender=['male','female']">
  <div ng-repeat="myGender in Gender">
    {{myGender}}
  </div>
</div>

<!--Using filte and AngularJs View and Controller-->
<!--This piece of code does not actually work!-->
<div ng-controller="mycontroller">
  <br/>
  <input type="text" ng-model="name" />
  <br/>
  <ul>
    <li ng-repeat="mycustomer in customers | filter:name| orderBy: 'city' ">{{mycustomer.name}} - {{mycustomer.city}} </li>
  </ul>
</div>

<script>
  function mycontroller($scope) {
      $scope.customers = [
        {name: 'Tom', city = 'Phoenix'},
        {name: 'Alice', city = 'New York'},
        {name: 'Bob', city = 'Orlando'}
      ];
    }
</script>

<!--AngularJS module

==============Creating a Module==========

var mymodule = angular.module('mymodule', []); //The empty array is where the so called "dependency injection" comes in

i.e,

var mymodule = angular.module('mymodule', ['helpModule']);

=====Creating a controller in a Module====

var mymodule = angular.module('mymodule', []); // create a module

function mycontroller($scope){
  $scope.customers = [
  ...
  ];
}

mymodule.controller('mycontroller', mycontroller); // create a controller

===============DEfining Routes===========

var mymodule = angular.module('mymodule', []); // create a module

mymodule.config (function($routeProvider){
  $routeProvider
    .when('/',
      {
        controller: 'mycontroller',
        templateUrl: 'View1.html'
      })
    .when('/partial2',
      {

      })
    .otherwise({redirectTo: '/'});
});

-->

</body>
</html>
```
