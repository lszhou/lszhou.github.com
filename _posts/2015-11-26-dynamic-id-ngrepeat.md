---
layout: post
title: Set dynamic ids using angularjs ng-repeat $index
---

"I delete a file from the file list, but the wrong one is deleted finally." It was a serious
bug I found from my current project. Fortunately, it was found only in the development branch
not from a released product. In this article, I will talk something about how to use angularjs
ngRepeat directive to iterate over objects and how to use ng-repeat `$index` variable to handle
tracking and duplicates.
---

*The ngRepeat directive instantiates a template once per item from a collection. Each template instance gets its own scope, where the given loop variable is set to the current collection item, and `$index` is set to the item index or key.* This is
the offcial description of ngRepeat directive and `$index` variable, how to understand it?

Let's read an [example](http://plnkr.co/edit/VwR5PEOL6TEkx3sTen8a?p=preview):

```html
<!DOCTYPE html>
<html ng-app="myApp">

<head>
  <!-- some references and basic setting -->
</head>

<body ng-controller="Ctrl">
  <div ng-repeat="item in items">
    <button type="button" class="btn btn-info" data-toggle="modal" data-target="#example">
      {{item}}
    </button>

    <div class="modal" id="example">
        <div class="modal-content">
          {{item}}
        </div>
    </div>
  </div>
</body>
</html>
```
In this example, the directive is used by `<div ng-repeat="item in items">`. Here the items the declared in the controller
`Ctrl` as `$scope.items = ['hi', 'hey', 'hello'];`. It is the so-called **collection** in the above official words and here the variable `item` is actually the so-called `loop variable `. ngRepeat instantiates a template for each `item`. i.e, 'hi', 'hey', 'hello' respectively. In the background, an angularjs variable `$index` is set to be current items index or key.

To minimize creation of DOM elements, ngRepeat uses a function to "keep track" of all items in the collection and their corresponding DOM elements. The default tracking function (which tracks items by their identity) does not allow duplicate items in arrays. This is because when there are duplicates, it is not possible to maintain a one-to-one mapping between collection items and DOM elements.

So we can not set out current `$scope.items = ['hi', 'hey', 'hello'];` to be `$scope.items = [42, 42, 43, 43];` without `$index`. If we really want to iterate on an array which contains duplicates, then we should do it like this

```html
<div ng-repeat="n in [42, 42, 43, 43] track by $index">
  {{n}}
</div>
```

If you read the code carefully, we may notice that it still has some problems.

```html
<button type="button" class="btn btn-info" data-toggle="modal" data-target="#example">
  {{item}}
</button>
```

The above codes help display the items correctly but the following code not,

```html
<div class="modal" id="example">
    <div class="modal-content">
      {{item}}
    </div>
</div>
```

Here is also the point for the serious bug I mentioned at the very beginning of this article.
With this article, we are expecting to create a modal for each item and display the item in the modal.
Simply, it "equals" to the following codes:

```html
<div class="modal" id="example">
    <div class="modal-content">
      hi
    </div>
</div>
<div class="modal" id="example">
    <div class="modal-content">
      hey
    </div>
</div>
<div class="modal" id="example">
    <div class="modal-content">
      hello
    </div>
</div>
```

Now do you see the problem? Yes, for each modal, we set the same id! That's why the three modals have the same
contents `hi`. To solve this problem, we should have so-called dynamic ids for different modals. How to get the ids?

```html
<div ng-repeat="item in items ">

  <button type="button" class="btn btn-info" data-toggle="modal" data-target="#example{{$index}}">
    {{item}}
  </button>

  <div class="modal" id="example{{$index}}">
      <div class="modal-content">
        {{item}}
      </div>
  </div>

</div>
```
Thanks for reading!
