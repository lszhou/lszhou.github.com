---
layout: post
title: File uploads with Express4.x via base64 encoding and decoding (Client-side)
---

In this article, I am going to review the file uploading with Express4.x.
There are actually several ways to upload files with NodeJS or Express, but here
I will only introduce a simple file uploading solution, i.e, base64 encoding and decoding.

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
