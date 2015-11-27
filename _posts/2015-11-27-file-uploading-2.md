---
layout: post
title: File uploads with Express4.x via base64 encoding and decoding (Client-side)
---

Today we will continue the topic about file uploading with Express4.x, but will focus on the backend, i.e,
express router

```javascript
api.post('/files', function(req, res) {
  // file data processing
}
```

---

So now recall the POST request from the fontend. Question: what is in the `req.body`?

Right, we have some basic file information, like file name, size, title, etc as well as the file itself as
dataURL. So if we want to get the original file back from these information, we need to know what is the original file
looks like, it should like this: *filename.extension*, i.e, music.mp3 or graph.jpg. etc.

Suppose the `req.body.name` is now *john.music-2014.mp3*, how to get the extension?

```javascript
// get file extension
var extension = (req.body.name).split('.')[(req.body.name).split('.').length -1];
```

Consider that sometimes the local data file name has some special characters, like space, `&, %, #`, we would like to kick those
out. And some filename does not make sense to the user, like *Selection_056.png*, or *21296830176_98c53298c1_o.jpg*, we would like to
use some much more meaningful filename to save in the database, for example, as for me, I would like to use the title *Family Photo* of
*Selection_056.png* as the new filename to save in the server. Following this idea, we get the new filename like this,

```javascript
// replace all characters by "_" except numbers and letters
var convertedTitle = (req.body.title).replace(/[^a-zA-Z0-9]/g,'_');
```
So for *Selection_056.png*, we save it in server as *FamilyPhoto.png*.

One problem left is how about tile "Family Photo" for *Selection_056.png* and
"Family&Photo" or *21296830176_98c53298c1_o.png*? Following the above schema, they will
both be save as "FamilyPhoto.png" although the original files could be two different ones.
So this schema will potentially generate a serious bug.

One solution is attach the uploading time (as a string) to the new filename. Since we only support
single file uploading, the uploading time will be different definitely for two files.

So the new renaming schema could be like this:

```javascript
// get file extension
var extension = (req.body.name).split('.')[(req.body.name).split('.').length -1];
var date = new Date();
var uploadTime = date.getHours().toString() + date.getMinutes().toString() + date.getSeconds().toString();
// replace all characters by "_" except numbers and letters
var convertedTitle = (req.body.title).replace(/[^a-zA-Z0-9]/g,'_');
var newfilename = convertedTitle + '_' + uploadTime + '.' + extension;
```

Now we have the filename to save in the server. How about the file data? i.e, how to decode the base64 string?

```javascript
// only decode the the byte array
var base64Data = (req.body.dataURI).split("base64,")[1];
var fileObject = new Buffer(base64Data, 'base64');
```

Done!

How to save all of these stuff to server? We need the help of NodeJS File I/O module **fs**,

```javascript
var fs = require('fs');
fs.writeFileSync(filepath, fileObject);
```
where `filepath` is the path of server file system you would like to save the file,
for example, `root/files/FamilyPhoto_12012015.png`.

But problem is if directory `root/files` does not exist?

```javascript
var UPLOAD_ROOT = `root/files`; // actually this could be written in config.js

if (!fs.existsSync(UPLOAD_ROOT)) {
  fs.mkdirSync(UPLOAD_ROOT);
}

fs.writeFileSync(file.filepath, fileObject);
```
All done.

Thanks for reading!
