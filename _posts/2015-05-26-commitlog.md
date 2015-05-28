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

when I input `email` and `jone@jone.com` key:value pair, the following error happens again:

```JSON
{
    "message": "User validation failed",
    "name": "ValidationError",
    "errors": {
        "email": {
            "properties": {
                "type": "required",
                "message": "Path `{PATH}` is required.",
                "path": "email"
            },
            "message": "Path `email` is required.",
            "name": "ValidatorError",
            "kind": "required",
            "path": "email"
        }
    }
}
```

The reason is what I typed was `email + space` but not `email`.

In this case, server will regard "email " (6 chars) as a key instead of "email" (5 chars). so make sure typing `email` 5 characters exactly the same as that in the schema.  

# Porblem: TypeError: Cannot call method 'comparePassword' with null

Let's look at the js code:

```javascript
...
auth.post('/login', function(req, res){
  User.findOne({
    username: req.body.username
  }).select('password').exec(function(err, user) {
      if(err) throw err;

      if(!user) {
         res.send("User does not exist");
      }

      if(!user.comparePassword(req.body.password)) {
        res.status(401).send({success:false, message:"bad password"});
      }

      // user and password are both valid, then create token
      var token = createToken(user);
      res.json({
        success: true,
        message: "login successful",
        token: token,
      });
...
```
We could not deduce where is the triger for this error directly from the TypeError desciption.
Notice that, in the above code, we use several `if(...)` statement, however, in mongoose, express, this
should be handelled very carefully.

To solve this problem, you only need to make the above code more logical, that is adding some `else` statement if they are connect.

```javascript
// well written code
auth.post('/login', function(req, res) {
  User.findOne({
    username: req.body.username
  }).select('password').exec(function(err, user) {
    if (err) throw err;

    if (!user) {
      res.send("User does not exist");
    } else {
      // check password
      if (!user.comparePassword(req.body.password)) {
        res.status(401).send({
          success: false,
          message: "bad password"
        });
      } else {
        // user and password are both valid, then create token
        var token = createToken(user);
        res.json({
          success: true,
          message: "login successful",
          token: token,
        });
      }
    }
  });
});
```
> Note
> In express and mongoose, `if(...){}; if(){};` is different from `if(){...}else{...}`
