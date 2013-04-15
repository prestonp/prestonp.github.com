---
layout: post
title: RESTful forms in express
category: posts
---

Express by default is quite agnostic about application structure. I've been working on a personal project where
listing routes in express quickly got bloated and cumbersome. Recently, I plugged 
in [express-resource](https://github.com/visionmedia/express-resource) for *resourceful routing*. This reduced a lot
of the explicit url routing to implicit REST actions.

Basically in a RESTful architecture, CRUD operations (Create, read, update, delete)
are implemented for resources. HTTP has request methods such as GET, POST, PUT, DELETE.

Using these request methods we can implement REST actions. As an example, lets define
a **forum** resource:
     
    HTTP    | URL                 |  Action 
    ---------------------------------------
    GET     /forums              ->  index
    GET     /forums/new          ->  new
    POST    /forums              ->  create
    GET     /forums/:forum       ->  show
    GET     /forums/:forum/edit  ->  edit
    PUT     /forums/:forum       ->  update
    DELETE  /forums/:forum       ->  destroy

As a user browses to the above urls, we want the resource controller to handle the associated action.
To implement this scheme we can simply require `express-resource` in our project:

    var Resource = require('express-resource')

This enables **app.resource** which maps our url scheme to the above actions:

    app.resource('forums', require('./controllers/forum'));

Define controllers/forum.js:

    exports.index = function(req, res){
      res.send('forum index');
    };
    
    exports.new = function(req, res){
      res.send('new forum');
    };
    
    exports.create = function(req, res){
      res.send('create forum');
    };
    
    exports.show = function(req, res){
      res.send('show forum ' + req.forum.title);
    };
    
    exports.edit = function(req, res){
      res.send('edit forum ' + req.forum.title);
    };
    
    exports.update = function(req, res){
      res.send('update forum ' + req.forum.title);
    };
    
    exports.destroy = function(req, res){
      res.send('destroy forum ' + req.forum.title);
    };


Now we can browse to **http://localhost:3000/forums** which triggers **forum.index(req, res)**

This makes routing *much easier* and consistent since you basically replace

    app.get('/forums', Forum.index)
    app.get('/forums/new', Forum.new)
    app.get('/forums/:id', Forum.show)
    app.post('/forums', Forum.create)
    app.put('/forums/:id', Forum.update)
    // etc..

with a one-liner:

    app.resource('forums', require('./controllers/forum'));

**Caveat**: HTML forms do not support PUT or DELETE methods, only GET and POST. One way to get around this is 
to use POST and add a hidden value to specify if its PUT or DELETE. To support this, enable methodOverride() 
in your express stack:

    app.use(express.methodOverride());

Then on the client side, we'll emulate a POST form by creating a typical POST form with a hidden `_method` var. We'll set 
this to `put` so the server will automatically handle it

    <form> ...
      <input type="hidden" name="_method" value="put" />
    </form>

Then you can use app.put() or app.del() freely! Since express-resource uses HTTP PUT and DELETE for the update and delete actions,
you can just tag forms with _method to emulate PUT and DELETE requests.
