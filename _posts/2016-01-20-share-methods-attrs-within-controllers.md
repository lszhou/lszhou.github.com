---
layout: post
title: Sharing Code Between AngularJS Controllers
---

Typically, there are several ways to share methods among controllers.
Today we are going to introduce two of them.

---

\# Using  Services

> The controllers get the UserService injected by adding it to the controller function as params.

The standard way to share code between controllers is using services. Actually service is also
the recommended way by the AngularJS officially. In the AngularJS documentation, we can find
a perfect [example](http://fdietz.github.io/recipes-with-angular-js/controllers/sharing-code-between-controllers-using-services.html)
to show how to do that with services. The key word I think using service is better to be "[dependency injection](https://docs.angularjs.org/guide/di)" which
helps how controllers get hold of their dependencies.
