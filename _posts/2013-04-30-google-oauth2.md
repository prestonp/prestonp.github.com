---
layout: post
title: Adding Google OAuth 2.0 with PassportJS
category: posts
---

[PassportJS](http://passportjs.org) makes authentication a breeze! Here's a quick run down for
a typical express app. 

For federated authentication, OAuth 2.0 is taking traction and is the recommended protocol. You can
find the google oauth strategy [here](https://github.com/jaredhanson/passport-google-oauth).

Install
-------

Protip: Use --save to automagically update your package.json

    $ npm install passport --save
    $ npm install passport-google-oauth --save


Configure Strategy
------------------

In your app, require the above modules

    var passport = require('passport')
      , GoogleStrategy = require('passport-google-oauth').OAuth2Strategy;

Then request a client ID from Google APIs for OAuth2. Hang onto the **client id**,
**client secret**, and **redirect URI**. I set up my redirect url as 
`exampledomain.com/auth/google/callback`. Make sure you keep it consistent 
throughout your passport configurations as well as the google api console.

Next, configure your google strategy:

    passport.use(new GoogleStrategy({
        clientID: YOUR_CLIENT_ID,
        clientSecret: YOUR_CLIENT_SECRET,
        callbackURL: app.get('site url') + '/auth/google/callback',
        scope: 'https://www.google.com/m8/feeds https://www.googleapis.com/auth/userinfo.email 
          https://www.googleapis.com/auth/userinfo.profile'
      },
      function(accessToken, refreshToken, profile, done) {
         User.updateOrCreate({ googleId: profile.id }, 
            function(err, user) {
              if(err) { throw err; }
                done(null, user);
              }
        );
      }
    ));

Note: It's a good idea to map your site url to development or production environments. 
The express way of doing this is through `app.set()` and `app.set()`. 

    // NODE_ENV == 'production' 
    app.configure('production', function() {
      app.set('site url', env.site_url.prod);       // www.domain.com
    });

    // NODE_ENV == 'development'
    app.configure('development', function() {
      app.set('site url', env.site_url.dev);        // localhost:3000
    });

Session Serialization
---------------------
For sessions, passport maintains a cookie that identifies each session.
Each request doesn't contain credentials but just the unique identifier for
that session. Basically, the client hangs onto its ID while the server
deserializes the id to the User object. This object is then accessed via
`req.user`.

The done() function is known as the **verify callback** which is called
after you check for valid credentials. In our case, we're using
OAuth so we're checking to see if the id matches someone in our
database. In basic authentication, you would check if username and
password match and THEN use done().

    passport.serializeUser(function(user, done) {
      done(null, user.id);    // id stored in a delicious cookie
    });

    passport.deserializeUser(function(id, done) {
      User
        .findOne({ googleId: id })
        .exec(function(err, user) {
          done(err, user);    // Now req.user == user
        });
    });

Here's an example of supporting multiple auth strategies. All you would need to
do is configure more strategies like the above google example.

    passport.serializeUser(function(user, done) {
      var ids = {
        facebookId: user.facebookId,
        twitterId: user.twitterId,
        googleId: user.googleId
      };
      done(null, ids);    // ids stored in a delicious cookie
    });

    passport.deserializeUser(function(ids, done) {
      User
        .findOne({ facebookId: ids.facebookId })
        .or({ twitterId: ids.twitterId })
        .or({ googleId: ids.googleId })
        .exec(function(err, user) {
          done(err, user);    // Now req.user == user
        });
    });

Define Routes
-------------
Last but not least, let's define our routes for authentication:

    app.get('/auth/google', passport.authenticate('google'));
    app.get('/auth/google/callback',
      passport.authenticate('google', { successRedirect: '/',
                                        failureRedirect: '/login'}));

    app.get('/login', function(req, res) {
      res.render('login');
    });

    app.get('/logout', function(req, res){
      req.logout();
      res.redirect('/');
    });

Now you can drop a link to `/auth/google` which will, after successful login,
give you `req.user`.
