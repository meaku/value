value
=====
**Convenient high-performance type-checking for JavaScript**

value is designed to ease [type-checking in JavaScript](http://bonsaiden.github.com/JavaScript-Garden/#types.typeof) while keeping performance in mind. It comes with a [fluent api](http://martinfowler.com/bliki/FluentInterface.html) to improve the readability of your code. Use value to sanitize function parameters and to provide better error messages if some type is unexpected. 

[![Build Status](https://secure.travis-ci.org/jhnns/value.png?branch=master)](http://travis-ci.org/jhnns/value)


<br />

Installation
------------

`npm install value`

If you're not using a CommonJS-system in the browser value is namespaced under `window.jhnns.value`.

<br />

Examples
--------



```javascript
var value = require("value");

// value takes every possible type in JavaScript
// and provides some methods to test for the type.
value(undefined).typeOf(Object); // = false
value(null).typeOf(Object); // = false
value({}).typeOf(Object); // = true
value(2).typeOf(Number); // = true
value("hello").typeOf(String); // = true
value(/myRegExp/).typeOf(RegExp); // = true
value(document.createElement("a")).typeOf(HTMLAnchorElement); // = true

// value also provides a negation for better readability
value(2).notTypeOf(String); // = true

// you can also check conveniently for null and undefined with one call
value(undefined).isSet(); // = false
value(null).isNotSet(); // = true

// value also supports convenient type-checking within collections
value([1, 2, 3]).each.typeOf(Number); // = true
value([1, "2", 3]).any.notTypeOf(Number); // = true
value({ one: 1, two: 2, three: null }).any.isNotSet(); // = true
```

<br />

API
--------

### typeOf(constructor): Boolean <sup>*Alias: instanceOf*</sup>

Tests if the subject is a child of the constructor. Respects the complete inheritance chain. Keep in mind that everything that is neither null nor undefined is a child of Object (see below).

### notTypeOf(constructor): Boolean <sup>*Alias: notInstanceOf*</sup>

Negation of `typeOf()`

### isSet(): Boolean

Returns true when the subject is neither `null` nor `undefined`

### isNotSet(): Boolean

Negation of `isSet()`

### each.typeOf(constructor): Boolean <sup>*Alias: each.instanceOf*</sup>

Invokes `value(item).typeOf(constructor)` on every item within the given collection. Returns true if every item returned true. A collection can be an array or an object.

### each.isSet(): Boolean

Invokes the value(item).isSet() on every item within the given collection. Returns true if every item returned true. A collection can be an array or an object.

### any.notTypeOf(constructor): Boolean <sup>*Alias: any.notInstanceOf*</sup>

Negation of `each.typeOf()`

### any.isNotSet(): Boolean

Negation of `each.isSet()`

### getConstructor(): Function|null <sup>*Alias: getClass*</sup>

Returns `subject.constructor` or `null` if the subject was `null` or `undefined`.

<br />

Fixes
--------

value contains a set of opinionated presumptions to avoid common and annoying pitfalls. It has been designed to act like you would expect from a language like ActionScript with optional static types. The returned result is not always conform with the JavaScript type-specification.

This is a collection of cases where value acts differently to the original `typeof`-operator:

### null is not an Object

In contrast to `typeof null === "object"`

```javascript
value(null).typeOf(Object); // = false
```

### new String() is a String

In constrast to `typeof new String() === "object"`

```javascript
value(new String()).typeOf(String); // = true 
```

### All set values are objects

```javascript
value("hello").typeOf(Object); // = true 
value(new String("hello")).typeOf(Object); // = true 
```

The primitive value `"hello"` is not an Object in JavaScript. But actually it *acts* like an Object because it is [casted to an Object](http://stackoverflow.com/a/2051893) internally when trying to access a property.

So: Everything that is not `null` and `undefined` is an Object. If you want to test whether the given value is a plain Object or not, just call `getConstructor()`:

```javascript
value({}).getConstructor() === Object; // = true
value("hello").getConstructor() === Object; // = false
```

Why not directly accessing the `constructor`-property? Well, in this case you won't have to write:

```javascript
if (myVar && myVar.constructor === Object) { ... }
```

### Infinity and NaN are not numbers

In constrast to `typeof NaN === "number"` and `typeof Infinity === "number"`

```javascript
value(NaN).typeOf(Number); // = false
value(Infinity).typeOf(Number); // = false
```

While `NaN` and `Infinity` are numbers from a theoretical point of view it's a common mistake when trying to sanitize calculation parameters. value assumes that numbers are numeric - that's why `NaN` and `Infinity` are not numbers.


<br />

Please note
--------

### arguments !== Array

This stays the same: `value(arguments).typeOf(Array)` will return false because `arguments` doesn't provide all methods of `Array`.

