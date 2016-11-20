# Symbol.templateTag

This is a stage 0, "strawman" proposal.

## Problem

Template tags are a great feature with many novel applications. Due to their nature of improving readability and expressiveness, it's helpful if template tags are named tersely.

Unfortunately, if you have an existing API, it can be challenging to find a terse name for a template tag function that does not collide with existing functions provided by that API. Thus, it would be useful if existing functions may *overload* their behavior, depending on whether they are being called normally, or being used in a template tag context.

## Solution

It is proposed that a new well-known symbol, `Symbol.templateTag`, be provided, which can be used to extend *any* object (not limited to functions) for use as a template tag.

The behavior of ECMAScript would be changed such that, when evaluating

```js
tag`...Template literal...`
```

`tag[Symbol.templateTag]` would be evaluated, and used as the function invocation, as opposed to `tag` by itself.

To allow functions to work directly as template tags (thus also preserving backwards compatibility) `Function.prototype[Symbol.templateTag]` would be implemented such that it simply returns the function itself, i.e.:

```js
// Approximately
Function.prototype[Symbol.templateTag] = function() { return this; };
```

This solution allows traditional function invocation to coexist with tagged template literal invocation in the same function object, e.g.:

```js

// Traditional function call behaviour
function dsl(code) {
    // ...
}

// Tagged template literal invocation
dsl[Symbol.templateTag] = function(code, ...interps) {
    // ...
}
```

## Notes

  * Really, this is a problem of function argument overloading. One can formalize template tag invocation as a function call with a distinct argument signature. Given that JavaScript does not (yet) do argument overloading, it seems reasonable to address this problem in isolation.
