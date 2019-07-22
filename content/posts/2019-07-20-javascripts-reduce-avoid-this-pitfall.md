---
template: post
title: 'Javascript''s Reduce: Avoid This Pitfall'
slug: one-pitfall-of-reduce
draft: false
date: 2019-07-20T16:13:45.594Z
description: >-
  Reduce is a powerful tool in the Javascript developer tool belt but it's not
  perfect. We look at one common thing many developers do in their reduce
  methods that could potentially cost you. 
category: Javascript Array Methods
tags:
  - Javascript Fundamentals
  - Javascript Array Methods
---
Reduce is a powerful functional array method. The transformations and choices you make about the returned values are completely optional. 

Consider the following

* You may change the number of elements in the final collection like filter, but it's optional.
* You may change the TYPE of element returned, but it's optional.
* You may change the shape of elements like map, but it's optional.

All this flexibility comes from one particular part of the reduce API.

## The Accumulator

> The accumulator accumulates the callback's return values. It is the accumulated value previously returned in the last invocation of the callback, or initialValue, if supplied 

* [MDN docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce#Syntax)

The common example for reduce is almost always taking some array of numbers and reducing it to one number, e.g.

```javascript
const sum = [1, 2, 3, 4].reduce((accumulator, currentValue) => accumulator + currentValue)
```

There's a slight problem, this is almost never how I've actually seen or used reduce. A lot of reduce involves the transformation of data, and/or building some new collection out of data. 

For example, we receive an array of objects like this: 

```javascript
const collection = [{id: '1AZ', name: 'Henry Henryson'}, {id: '2AZ', name: "Alice Aliceson"}]
```

But want to transform it into an object for faster lookups and easier ergonomics. We may do something like this: 

```javascript
const newCollection = collection.reduce((accumulator, currentValue) => {
    accumulator[currentValue.id] = {...cur}
    return accumulator
}, {})
```

This yields us: 

```javascript
{
 '1AZ': {id: '1AZ', name: 'Henry Henryson'},
 '2AZ': {id: '2AZ', name: "Alice Aliceson"}
}
```

So what's the pitfall? 

## Spread Operators

If you've ever googled reduce, likely you've seen something like this:

```javascript
foo.reduce((accumulator, currentValue) => {
  return [...accumulator, currentValue.bar]
}, [])
```

Looks normal right? 

(NOTE: before anyone yells at me, yes this example should be a map and reduce isn't necessary. I'm using it as a simple example to show the pitfalls of spreading the accumulator)

Well it is. But there's a small problem with spreading that accumulator.

The javascript we write isn't always the JS that gets delivered to a client. Often times we are transpiling our JS to work cross-browser. The spread operator is an ES6 feature, and gets transpiled to ensure it works everywhere it could be run.

Let's look at that same reduce code above when run through Babel on the `es2015` aka ES6 preset (using the excellent Babel repl found [here](https://babeljs.io/repl#?babili=false&browsers=&build=&builtIns=false&spec=false&loose=false&code_lz=Q&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=true&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=es2015&prettier=true&targets=&version=7.5.5&externalPlugins=%40babel%2Fplugin-transform-react-jsx%407.3.0)).

```javascript
function _toConsumableArray(arr) {
  return (
    _arrayWithoutHoles(arr) || _iterableToArray(arr) || _nonIterableSpread()
  );
}

function _nonIterableSpread() {
  throw new TypeError("Invalid attempt to spread non-iterable instance");
}

function _iterableToArray(iter) {
  if (
    Symbol.iterator in Object(iter) ||
    Object.prototype.toString.call(iter) === "[object Arguments]"
  )
    return Array.from(iter);
}

function _arrayWithoutHoles(arr) {
  if (Array.isArray(arr)) {
    for (var i = 0, arr2 = new Array(arr.length); i < arr.length; i++) {
      arr2[i] = arr[i];
    }
    return arr2;
  }
}

var foo = [];
foo.reduce(function(accumulator, currentValue) {
  return [].concat(
    _toConsumableArray(accumulator),
    _toConsumableArray(currentValue.bar)
  );
}, []);
```

That's a lot of code. 

Now let's make one modification. 

```javascript
foo.reduce((accumulator, currentValue) => {
  accumulator.push(currentValue.bar)
  return accumulator
}, [])
```

Notice here we aren't spreading the accumulator, but instead are locally mutating it within the reduce callback.

Let's see the Babel transpilation of that code. 

```javascript
var foo = [];
foo.reduce(function(accumulator, currentValue) {
  accumulator.push(currentValue.bar);
  return accumulator;
}, []);
```

Thats a lot _less_ code. 

## So What

Let's look at some performance stats (check out the perf test [here](https://jsperf.com/reduce-with-and-without-spread-operator)).

On an array of 10,000 elements, here's how our two versions of reduce performed. 

![JS bench test showing spreading as the slower option](/media/screen-shot-2019-07-21-at-2.42.29-pm.png "JS bench test")

**reduce with spread:**
8.10 ops/second.

**reduce without spread:**
5,192 ops/second.

The only code difference between these two snippets is the spread operator. 

The performance difference, however,  is massive. 

## A Note on Mutations

There are a few reasons folks spread their accumulators. One is because it looks nicer (and it sort of does) another is out of an aversion to mutating anything in JS land. 

It seems like folks are going out of their way to make all operations immutable.  

Spreading the accumulator is one example where this goes wrong. Mutations are not the enemy if you control them. Look at our above reduce implementations. The mutation of the accumulator is locally scoped and absolutely appropriate. It even results in faster code. 

This article is not about mutable vs immutable, but it's worth mentioning briefly here. 

## TL;DR

1. Spreading the accumulator in reduce results in a far slower execution speed than mutating the accumulator. 
2. This likely doesn't matter at all when your collections are small.
3. This definitely can matter when your collections are large.
4. Make sure to look at your transpiled JS (use the [Babel Repl](https://babeljs.io/repl#?babili=false&browsers=&build=&builtIns=false&spec=false&loose=false&code_lz=Q&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=true&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=es2015&prettier=true&targets=&version=7.5.5&externalPlugins=%40babel%2Fplugin-transform-react-jsx%407.3.0)) to find potential code bloat.
