# express-route-autocorrect

[![NPM version](https://badge.fury.io/js/express-route-autocorrect.svg)](https://badge.fury.io/js/express-route-autocorrect)
[![Build Status](https://api.travis-ci.org/iBicha/express-route-autocorrect.svg?branch=master)](https://travis-ci.org/iBicha/express-route-autocorrect)
[![Coverage Status](https://coveralls.io/repos/github/iBicha/express-route-autocorrect/badge.svg?branch=master)](https://coveralls.io/github/iBicha/express-route-autocorrect?branch=master)

Middleware that autocorrects url routes to the closest match.
## Install

    $ npm install express-route-autocorrect --save
## Usage

`express-route-autocorrect` should be set as the last middleware, but right before the 404 not found error handler.
It will populate `req.urlBestMatch` with the best match so you can decide what to do with it.
The middleware takes an array of routes to compare then against the request.
```javascript
var routeAutocorrect = require('express-route-autocorrect');
//some other middlewares...

app.use('/', index);
app.use('/users', users);
app.use('/some/other/route', someOtherView);

app.use(routeAutocorrect([
    '/',
    '/users',
    '/some/other/route'
]));

// catch 404 and forward to error handler
app.use(function (req, res, next) {
    // e.g. if req.url == '/user' then req.urlBestMatch will contain '/users'
    var err = new Error('Not Found. did you mean to go to ' + req.urlBestMatch + ' ?');
    err.status = 404;
    next(err);
});

// error handler view here...
```

You can also specify to redirect option, to automatically redirect to the `urlBestMatch`

```javascript
app.use(routeAutocorrect({
    routes: [
        '/',
        '/users',
        '/some/other/route'
    ],
    redirect: true
}));
```

If you're redirecting automatically, you have the ability to pass in a `threshold` option.

This allows you to handle not-very-close "matches" by sending users to an actual 404 page.
In the following example, a request for `/users/julie-smith` would result in a match!

```javascript
app.use(routeAutocorrect({
    routes: [
        '/users/jane-smith',
        '/users/john-smith'
    ],
    redirect: true,
    treshold: .99
}));
```

But by providing a threshold, if a user made a request to `/users/julie-smith`, the best match found would not meet the requirements for automatic redirection.
