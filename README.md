mqemitter&nbsp;&nbsp;[![Build Status](https://travis-ci.org/mcollina/mqemitter.png)](https://travis-ci.org/mcollina/mqemitter)
=================================================================

An Opinionated Message Queue with an emitter-style API, but with
callbacks.

  * <a href="#install">Installation</a>
  * <a href="#basic">Basic Example</a>
  * <a href="#api">API</a>
  * <a href="#wildcards">Wildcards</a>
  * <a href="#licence">Licence &amp; copyright</a>

<a name="install"></a>
## Installation

```
$ npm install mqemitter --save
```

<a name="basic"></a>
## Basic Example

```js
var mq = require('mqemitter')
  , emitter = mq({ concurrency: 5 })
  , message

emitter.on('hello world', function(message, cb) {
  // call callback when you are done
  // do not pass any errors, the emitter cannot handle it.
  cb()
})

// topic is mandatory
message = { topic: 'hello world', payload: 'or any other fields' }
emitter.emit(message, function() {
  // emitter will never return an error
})
```

## API

  * <a href="#mq"><code>MQEmitter</code></a>
  * <a href="#emit"><code>emitter#<b>emit()</b></code></a>
  * <a href="#on"><code>emitter#<b>on()</b></code></a>
  * <a href="#removeListener"><code>emitter#<b>removeListener()</b></code></a>

-------------------------------------------------------
<a name="mq"></a>
### MQEmitter(opts)

MQEmitter is the class and function exposed by this module.
It can be created by `MQEmitter()` or using `new MQEmitter()`.

An MQEmitter accepts the following options:

- `concurrency`: the maximum number of concurrent messages that can be
  on concurrent delivery.
- `wildcardOne`: the char that will match one level wildcards.
- `wildcardSome`: that char that will match multiple level wildcards.
- `separator`: the separator for the different levels.

For more information on wildcards, see [this explanation](#wildcards) or
[Qlobber](https://github.com/davedoesdev/qlobber).

-------------------------------------------------------
<a name="emit"></a>
### emitter.emit(message, callback())

Emit the given message, which must have a `topic` property, which can contain wildcards
as defined on creation.

-------------------------------------------------------
<a name="on"></a>
### emitter.on(topic, callback(message, done))

Add the given callback to the passed topic. Topic can contain wildcards,
as defined on creation.
The `callback`, accept two parameters, the passed message and a `done`
callback.

The callback __must never error__ and `done` must not be called with an
__`err`__ object.

-------------------------------------------------------
<a name="removeListener"></a>
### emitter.removeListener(topic, callback)

The inverse of `on`.

<a name="wildcards"></a>
## Wildcards

__MQEmitter__ supports the use of wildcards: every topic is splitted
according to `separator` (default `/`).

The wildcard character `+` matches exactly one word:

```javascript
var mq = require('mqemitter')
  , emitter = mq()

emitter.on('hello/+/world', function(message, cb) {
  // this will print { topic: 'hello/my/world', 'something': 'more' }
  console.log(message)
  cb()
})

emitter.on('hello/+', function(message, cb) {
  // this will not be called
  console.log(message)
  cb()
})

emitter.emit({ topic: 'hello/my/world', something: 'more' })
```

The wildcard character `#` matches zero or more words:

```javascript
var mq = require('mqemitter')
  , emitter = mq()

emitter.on('hello/#', function(message, cb) {
  // this will print { topic: 'hello/my/world', 'something': 'more' }
  console.log(message)
  cb()
})

emitter.on('#', function(message, cb) {
  // this will print { topic: 'hello/my/world', 'something': 'more' }
  console.log(message)
  cb()
})

emitter.on('hello/my/world/#', function(message, cb) {
  // this will print { topic: 'hello/my/world', 'something': 'more' }
  console.log(message)
  cb()
})

emitter.emit({ topic: 'hello/my/world', something: 'more' })
```

Of course, you can mix `#` and `+` in the same subscription.

## LICENSE

Copyright (c) 2014, Matteo Collina <hello@matteocollina.com>

Permission to use, copy, modify, and/or distribute this software for any
purpose with or without fee is hereby granted, provided that the above
copyright notice and this permission notice appear in all copies.

THE SOFTWARE IS PROVIDED "AS IS" AND THE AUTHOR DISCLAIMS ALL WARRANTIES
WITH REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED WARRANTIES OF
MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR
ANY SPECIAL, DIRECT, INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES
WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN
ACTION OF CONTRACT, NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF OR
IN CONNECTION WITH THE USE OR PERFORMANCE OF THIS SOFTWARE.