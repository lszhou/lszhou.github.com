---
layout: post
title: Express, Mongoose commit log
---

> This blog is used to record my recent commits and errors I have encounted.

# Porblem 1: Error('Can\'t set headers after they are sent.')

This erros happens when the callback function looks like this

```javascript
function(err, user) {
  if (err)
    return next(err);

  if (!user) {
    res.status(404).send({
      success: false,
      message: "user not found"
    });
  }

  res.json(user);
}
```

When current user does not exist, then "if(!user)..." will be executed, technically, then the execution should stop. But
we could see "res.json(user);" will still be executed. Thus, there are two response operations to only one request, which is not
allowed. Thus, the solution is straightforward, add an "else" statement like this:

```javascript
...
if (!user) {
  res.status(404).send({
    success: false,
    message: "user not found"
  });
} else {
  res.json(user);
}
...
```

# Tip 1: "nodemon" tool

Normally, when we use "nodejs" or "node" command to execute "server.js", the http server should be stopped and restart again to apply the new changes. Instead, we could use "nodemon" tool which could help us restart the server automatically when some changes are made in the codes.  

```
npm install -g nodemon // install
nodemon server.js // use nodemon command to execute server.js
```

# Problem 2: /user/:username

For the end point `/user/:username`, the character `:` is actually a prompt or syntax to signify that `username` is an variable. So when we apply this end point with real character, `:` should be omitted. i.e,

```
localhost:8080/users/:Tom   // This is wrong
localhost:8080/users/Tom    // This is right
```

Actually, as long as we use `:` to signify an variable, then this variable value could be accessed by `req.params`. `req.params` is an object containing properties mapped to the named route “parameters”.

One more official example, if you have the route `/user/:name`, then the “name” property is available as `req.params.name`. This object defaults to `{}`.

# Problem 3: "User validation failed"

```
"message": "User validation failed",
"name": "ValidationError",
"errors": ......
```

This eror is not from the prompt but from Postman, the solution is stupid, just because I passed the wrong field name 'firstname' instead of the right one in the schema `userName`. Change it, then it is done.
