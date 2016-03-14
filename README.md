![logo](https://rawgit.com/senecajs/varo/master/assets/varo-logo.svg)
> pattern matched composition for browser apps

# varo
[![npm version][npm-badge]][npm-url]
[![Build Status][travis-badge]][travis-url]
[![Coverage Status][coveralls-badge]][coveralls-url]
[![Dependency Status][david-badge]][david-url]
[![Gitter][gitter-badge]][gitter-url]

__varo__ is a _pattern matched logic_ library designed for the browser. Varo is designed to
compliment [Seneca][senecajs.org] by providing a similar, albeit, smaller set of API's. It's ideal
use case is in browser-side apps.

The focus of Varo is to provide the ability to compose logic around patterns; it does
not handle transport or any other concerns.

If you're using this module, and need help, you can:

- Post a [github issue][],
- Tweet to [@senecajs][],
- Ask on the [Gitter][gitter-url].
- Contact __Lead Maintainer:__ [Dean McDonnell][lead]

## Install
To install, simply use npm,

```sh
npm install varo
```

## Test
To run tests, simply use npm:

```sh
npm run test
```

## Browserify
To run browserify, simply use npm:

```sh
npm run browserify
```

## Quick Example

```js
'use strict'

var Varo = require('varo')()

// Only one handler will be called, more specific wins,
// then insertion order is checked on two identical matches.
Varo.handle({role: 'sum'}, function (msg, done) {
  return done(null, {answer: (msg.left + msg.right)})
})

// Won't be called unless the pattern above was removed
Varo.handle({role: 'sum'}, function (msg, done) {
  return done(null, {answer: (msg.left - msg.right)})
})

// Listen for anything emitted via emit
Varo.observe({role: 'user'}, function (msg) {
  console.log(msg.user.name)
})

Varo.emit(role: 'user', event: 'change', user: {name: 'Dean'}})

// Get a response to a message. Handy for asking for data or
// making calculations.
Varo.act({role: 'sum', left: 1, right: 2}, function (err, reply) {
  console.log(reply)
})

// You don't have to use a callback, if you are running an handler that
// doesn't respond then it is ok to leave it out.
Varo.act({role: 'sum', left: 1, right: 2})

```

## API

### .handle(msg, handler(err, reply)) : _this_
Adds a handler for the msg provided. The handler is called when .act() is used. Note that only
one handler will be called.

```js
Varo.handle({role: 'sum'}, function (msg, done) {
  return done(null, {answer: (msg.left - msg.right)})
})
```

### .observe(msg, observer(msg)) : _this_
Adds an observer that listens for any message matching the msg param. The observer is called
when .emit() is used. Multiple observers can handle a single message; there is no callback

```js
Varo.observe({role: 'sum'}, function (msg) {
  console.log(msg)
})
```

### .act(msg [, reply]) : _this_
Sends the provided message to any interested single handler. Calls to act can be
fire and forget or request response as necessary.

```js
Varo.act({role: 'sum', left: 1, right: 2}, function (err, reply) {
  console.log(reply)
})

Varo.act({role: 'sum', left: 1, right: 2})
```

### .emit(msg) : _this_
Sends the provided message to any interested observers. Calls to emit have no callback and are
considered fire and forget. This makes `.emit() / .observere()` a great pattern matched replacement
for event emitters.

```js
Varo.handle({role: 'user', event: 'changed'}, function (msg) {
  console.log(msg.user.name)
})

Varo.emit({role: 'user', event: 'changed', user: {name: 'Dean'})
```

### .plugin(plugin(Varo)) : _this_
Calls the provided function with the current instance of varo. Useful to group functionality
together in modular format.

```js
Varo.plugin(function (varo) {
  varo.handle({role: 'sum'}, function (msg, done) {
    return done(null, {answer: (msg.left + msg.right)})
  })

  varo.observe({role: 'sum'}, function (msg) {
    console.log(msg)
  })
})

Varo.act({role: 'sum', left: 1, right: 2}, function (err, reply) {
  console.log(reply)
})
```

### .removeHandler(handler) : _this_
Removes a named handler. Does not work for anonymous functions.

```js
var handler = function (msg, done) {
  return done(null, {answer: (msg.left + msg.right)})
}

Varo.handle({role: 'sum'}, handler)
Varo.removeHandler(handler)
```

### .removeObserver(observer) : _this_
Removes a named observer. Does not work for anonymous functions.

```js
var observer = function (msg) {
  console.log(msg)
}

Varo.observe({role: 'sum'}, observer)
Varo.removeObserver(observer)
```

## Contributing
The [Senecajs org][] encourages open participation. If you feel you can help in any way, be it with
documentation, examples, extra testing, or new features please get in touch.

## License
Copyright (c) 2015, Dean McDonnell and other contributors. <br />
Licensed under [MIT][].

[MIT]: ./LICENSE
[lead]: https://github.com/mcdonnelldean
[Senecajs org]: https://github.com/senecajs/
[Seneca.js]: https://www.npmjs.com/package/seneca
[@senecajs]: http://twitter.com/senecajs
[senecajs.org]: http://senecajs.org/

[npm-badge]: https://img.shields.io/npm/v/varo.svg
[npm-url]: https://npmjs.com/package/varo
[travis-badge]: https://api.travis-ci.org/senecajs/varo.svg?branch=master
[travis-url]: https://travis-ci.org/senecajs/varo
[coveralls-badge]:https://coveralls.io/repos/senecajs/varo/badge.svg?branch=master&service=github
[coveralls-url]: https://coveralls.io/github/senecajs/varo?branch=master
[david-badge]: https://david-dm.org/senecajs/varo.svg
[david-url]: https://david-dm.org/senecajs/varo
[gitter-badge]: https://badges.gitter.im/Join%20Chat.svg
[gitter-url]: https://gitter.im/senecajs/seneca
[github issue]: https://github.com/senecajs/varo/issues
