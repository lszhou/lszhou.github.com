---
layout: post
title: File uploads with Express4.x via base64 encoding and decoding (Client-side)
---

In this article, We are going to review the file uploading with Express4.x.
There are actually several ways to upload files with NodeJS or Express, but here
We will only introduce a simple file uploading solution, i.e, base64 encoding and decoding.
We will not spend a lot of time on the algorithm of how to encode a file to data URL but will focus
on the front-end AngularJS file uploading process, i.e, how AngularJS components work together
to $http.post the file data (file basic information, size, filename, etc and file itself in data URLs forma)
to the backend.

---
Base64 encoding schemes are commonly used when there is a need to encode binary data that needs to
be stored and transferred over media that are designed to deal with textual data. This is
to ensure that the data remain intact without modification during transport.

[WIKIPEDIA Base64](https://en.wikipedia.org/wiki/Base64#Examples) has good description of the
concepts. Basically, base64 is an encoding scheme, you could understand it as a translator, it can
translate any binary data in an ASCII string format into a radix-64 representation.
The high-level intuition is we could use base64 to *translate* any file, music.mp3, sample.txt,
setup.exe, book.pdf, etc to a String like this:

```
TWFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5IGhpcyByZWFzb24sIGJ1dCBieSB0aGlz
IHNpbmd1bGFyIHBhc3Npb24gZnJvbSBvdGhlciBhbmltYWxzLCB3aGljaCBpcyBhIGx1c3Qgb2Yg
dGhlIG1pbmQsIHRoYXQgYnkgYSBwZXJzZXZlcmFuY2Ugb2YgZGVsaWdodCBpbiB0aGUgY29udGlu
dWVkIGFuZCBpbmRlZmF0aWdhYmxlIGdlbmVyYXRpb24gb2Yga25vd2xlZGdlLCBleGNlZWRzIHRo
ZSBzaG9ydCB2ZWhlbWVuY2Ugb2YgYW55IGNhcm5hbCBwbGVhc3VyZS4=
```

If we encode a file in base64 and represent it by [data URIs](https://developer.mozilla.org/en-US/docs/Web/HTTP/data_URIs)
as following:

```
data:text/html, TWFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5IGhpcyByZWFzb24sIGJ1dCBieSB0aGlz
IHNpbmd1bGFyIHBhc3Npb24gZnJvbSBvdGhlciBhbmltYWxzLCB3aGljaCBpcyBhIGx1c3Qgb2Yg
dGhlIG1pbmQsIHRoYXQgYnkgYSBwZXJzZXZlcmFuY2Ugb2YgZGVsaWdodCBpbiB0aGUgY29udGlu
dWVkIGFuZCBpbmRlZmF0aWdhYmxlIGdlbmVyYXRpb24gb2Yga25vd2xlZGdlLCBleGNlZWRzIHRo
ZSBzaG9ydCB2ZWhlbWVuY2Ugb2YgYW55IGNhcm5hbCBwbGVhc3VyZS4=
```

then we can easy convert `upload a file` to 'upload a string'. In the back end, we decode this
data URL and translate it back to the original file and save it in the file system of server.

Ok, now let's review some front-end codes which was written following AngularJS MVC Architecture. So in the following sections,
we will mention some AngularJS concepts, directive, controller, service, data binding, etc.

The basic front-end idea is, we read the file as dataURL via directive (`FileRead`) from HTML to controller ('FileController'),
in controller, we call method `upload(...)` to pass dataURL to service (`FileService`)  where we will call `$http.post` to POST the dataURL to the backend.

Many thanks to [Endy Tjahjono](http://stackoverflow.com/users/196451/endy-tjahjono) who wrote the following directive

```javascript
app.directive("fileread", [function() {
  'use strict';

  return {

    scope: {
      fileread: "="
    },

    link: function(scope, element, attributes) {

      element.bind("change", function(changeEvent) {

        var reader = new FileReader();

        reader.onload = function(loadEvent) {

          scope.$apply(function() {
            scope.fileread = loadEvent.target.result;
          });
        };

        reader.readAsDataURL(changeEvent.target.files[0]);
      });
    }
  };
}]);

```

With the help of this directive, we can easily read a file in dataURL format.
In the html view, the code looks like this

```html
<input type = 'file' id = 'fileUpload' name='file' fileread="fileCtrl.info.dataURI" >
```

You may noticed that, we read our file as dataURL by directive `fileread` to variable `fileCtrl.info.dataURI`.
Here `fileCtrl` is the controller, `info` is a JSON variable to store some file infomation, like dataURL, file name, size, etc.
Actually, we using the angularjs feature data binding.

Here are some pieces of controller codes:

```javascript
app.controller('FileController', [function(){
  'user strict';

  var vm = this;

  // file info in JSON
  vm.info = {
    name: null,
    description: null,
    title: null,
    size: null,
    dataURI: null // base64-encoded version of original file
  };
  //.....
  //....
  //....
}]);
```
When a controller is attached to the DOM via the ng-controller directive (simply, when a webpage is loaded),
Angular will instantiate a new controller object, using the specified Controller's constructor function.
An empty json vm.info will be created. Then when a file is selected, directive `fileread` will read the file
as dataURL to `vm.info.dataURL` variable *automically* which is implemented actually by angularjs data binding feature.

Then we can define an `ng-click` function `upload()` in FileController as follows:

```javascript
// upload new file
vm.upload = function() {

  // get file size and name from global variables
  vm.info.size = window.size;
  vm.info.name = window.name;

  // clear error and message
  vm.error = '';
  vm.message = '';

  if (vm.info.size > 30) { // if the selected file is too large

    vm.error = 'Oops! File is too large, please select a file less than 30 MB.';

  } else if (vm.info.dataURI === null) { // if no file is selected

    vm.error = 'Oops! No file is selected.';

  } else if (vm.info.dataURI == 'data:') {

    vm.error = 'Oops! Selected file has no content, please confirm the right file.';

  } else {

    // declare processing flag
    vm.processing = true;

    File
      .upload(vm.info)
      .success(function(data){

        vm.processing = false;

        if(data.success === false) {

          vm.error = data.message;

        } else {

          // clear up the form
          vm.info = {
            name: null,
            description: null,
            title: null,
            size: null,
            dataURI: null
          };

          // clear up error and message
          vm.error = '';
          vm.message = '';

          vm.message = 'Upload Successfully!';
        }
      });
  }
};
```

`upload(vm.info)` method could be found in FileService

```javascript
app.factory('File', ['$http', function($http) {
  'use strict';

  var fileFactory = {};

  // post file name, description, size and title
  fileFactory.upload = function(info) {
    return $http.post('/api/files', info);
  };

  return fileFactory;
}]);
```

With this FileService, we can POST our data (File information and file itself) to the backend.
In the next article, I wil introduce how to decode the data and get our original file back and
save it in the server file system.

Thanks for reading!
