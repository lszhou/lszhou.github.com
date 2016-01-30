---
layout: post
title: Sharing Code Between AngularJS Controllers, Part II
---


\# Using *$controller*

*In Angular, a Controller is defined by a JavaScript constructor function that is used to augment the Angular Scope.
When a Controller is attached to the DOM via the ng-controller directive, Angular will instantiate a new Controller object,
using the specified Controller's constructor function. A new child scope will be created and made available as an injectable
parameter to the Controller's constructor function as $scope.*

The above child scope is independent with each other, i.e, these scopes are protected by their independency. The scope of one controller
instant/object is not accessible by another. But as the root for all child scopes, `$rootScope` is accesible from every controllers.

From this we have another idea to *access* another controller's scope.
