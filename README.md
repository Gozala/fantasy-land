# Fantasy Land Specification

(aka "Algebraic JavaScript Specification")

![](logo.png)

This project specifies interoperability of common algebraic
structures:

* Functor
* Monad

## General

An algebra is a set of values, a set of operators that it is closed
under and some laws it must obey.

## Terminology

1. "value" is any JavaScript value, including any which have the
   structures defined below.

## Algebras

### Functor

Functor values **MUST** implement `map` function that complies to
a following rules:

1. `map(u, function(a) { return a; })` equals `u`
2. `map(u, function(x) { return f(g(x)); })` equals `map(map(u, g), f)`

#### `map` function

A value which has a functor must provide a `map` implementation. That takes
functor value and `f` function:

    map.define(u, function(u, f) {
      // ...
    })

1. `f` must be a function,

    1. If `f` is not a function, the behaviour of `map` is
       unspecified.
    2. `f` can return any value.

2. `map` must return a value of the same functor

### Monad

A value which satisfies the specification of a monad do not need to implement:
Functor's  `map` as it's defined by default:

```js
map.define(Object, function(wrapped, f) {
   return flatMap(wrapped, function(unwrapped) {
      return derive(wrapped, f(unwrapped));
   });
});
```

1. `of(a).then(f)` equals `f(a)`
2. 
2. `flatMap(m, function(u) { return derive(m, u) })` equals `m`
2. `m.then(of)` equals `m`
3. `m.then(f).then(g)` equals `m.then(function(x) { return f(x).then(g); })`

#### `flatMap` function

A value which is a monad must provide a `flatMap` implementation. The `flatMap`
function takes `m` monad and `f` function as arguments:

    flatMap(m, f)

1. `f` must be a function which returns a value

    1. If `f` is not a function, the behaviour of `flatMap` is
       unspecified.
    2. `f` must return a value of the same monad

2. `flatMap` must return a value of the same monad

#### `derive` function

A value which has a monad must provide `derive` implementation.
Implementation must take `source` argument and `value`:

```js
derive(m, a)
```

1. `derive` must provide a value of the same monad

    1. No parts of `a` should be checked
