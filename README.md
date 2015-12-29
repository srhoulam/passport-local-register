# passport-local-register

[Passport](http://passportjs.org/) strategy for authenticating with a username
and password and registering new accounts on failure.

This module differs from [passport-local](https://github.com/jaredhanson/passport-local)
by allowing you to turn failed authentication requests into user registrations. Using this
module correctly, login should only fail if the account identifier (e.g., username)
supplied is already taken.

Except where explicitly specified, this module works as
[passport-local](https://github.com/jaredhanson/passport-local)
would. It is strongly suggested that you be familiar with
**passport-local** before using this module, as this document
will not seek to duplicate **passport-local**'s documentation.

## Install

```bash
$ npm install passport-local-register
```

## Usage

### Configure Strategy

Configuring this strategy is almost identical to configuring
[passport-local](https://github.com/jaredhanson/passport-local),
except that it requires one more callback to be passed to the
constructor.

#### Example
```js
passport.use(new RegisterStrategy(
  function verify(username, password, done) {
    User.findOne({
      'username': username
    }, function(err, user) {
      if (err) { return done(err); }
      if (!user) { return done(null, false); }
      if (!user.verifyPassword(password)) { return done(null, false); }
      return done(null, user);
    });
  }, function create(username, password, done) {
    User.create({
      'username' : username
    }, function(err, user) {
      if(err) {
        return done(err);
      }
      if(!user) {
        err = new Error("User creation failed.");
        return done(err);
      }

      done(null, user);
    });
  }
));
```

#### Authenticate Requests

Use `passport.authenticate()`, specifying the `'localRegister'` strategy, to
authenticate requests.

For example, as route middleware in an [Express](http://expressjs.com/)
application:

```js
app.post('/login', 
  passport.authenticate('localRegister', { failureRedirect: '/login' }),
  function(req, res) {
    res.redirect('/');
  });
```

## Credits

- [Saad Rhoulam](http://github.com/srhoulam), register-on-fail functionality
- [Jared Hanson](http://github.com/jaredhanson), everything else

## License

[The MIT License](http://opensource.org/licenses/MIT)
