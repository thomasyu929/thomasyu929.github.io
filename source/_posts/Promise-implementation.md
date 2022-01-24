---
title: Promise/A+ - implementation
category: experience
tag: 
- Javascript
- Promise
---

> A *promise* represents the eventual result of an asynchronous operation. The primary way of interacting with a promise is through its `then` method, which registers callbacks to receive either a promise’s eventual value or the reason why the promise cannot be fulfilled.

## Before Start

*Promise/A+ specification:* https://github.com/promises-aplus/promises-spec

*Promise/A+ test suite:* https://github.com/promises-aplus/promises-tests

## Promise

### Terminology 

> 1. "promise" is an object or function with a `then` method whose behavior conforms to this specification.
> 2. "thenable" is an object or function that defines a `then` method.
> 3. "value" is any legal JavaScript value (including `undefined`, a thenable, or a promise).
> 4. "exception" is a value that is thrown using the `throw` statement.
> 5. "reason" is a value that indicates why a promise was rejected.

### Promise State

A promise must be in one of three states: `pending`, `fulfilled`, or `rejected`.

`fulfilled` state need have an immutable `value`, `rejected` state need have an immutable `reason`.

*Note:* `immutable` means `===`, object only require reference not changed.

### Then

```javascript
Promise.then(onFulfilled, onRejected);
```

Both of them are optional, if they are not function, must be ignored.

`then` will return `Promise` object.

### Promise example

```javascript
const promise = new Promise(function(resolve, reject) {
  // ... some code
  if (/*async success*/){
  	resolve(value);    
  } else {
		reject(error);                      
  }
});
promise.then(function(value) {
  // success
}, function(error) {
  // failure		same as Promise.prototype.catch()
});
```

---

## Implementation based on Promise/A+

源码：https://github.com/thomasyu929/leetcode-ui/blob/master/js-problems/_Promise/_Promise.js

#### Related Promise methods

TODO - See above link for now.

- [x] _Promise.resolve
- [x] _Promise.reject
- [x] _Promise.prototype.catch
- [x] _Promise.prototype.finally
- [x] _Promise.all
- [x] _Promise.allSettled
- [x] _Promise.any
- [x] _Promise.race
