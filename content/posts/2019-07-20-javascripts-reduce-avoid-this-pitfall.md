---
template: post
title: 'Javascript''s Reduce: Avoid This Pitfall'
slug: pitfalls-of-reduce
draft: true
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
Reduce is a powerful functional array method. In reduce, unlike all other array methods, the transformations and choices you make about the values are completely optional. 

Consider the following

* You may change the number of elements in the final collection like filter, but it's optional.
* You may change the TYPE of element returned, but it's optional.
* You may change the shape of elements like map, but it's optional.

All this flexibility comes from one particular part of the reduce API.

## The Accumulator

The accumulator can be the user's best friend. As noted above, by crafting the accumulator on each pass through the array, the user can make any number of transformations. 

With great power comes great responsibility though. 

## Spread Operators

If you've ever googled reduce, likely you've seen a lot of things like this:

```
foo.reduce(function(accumulator, currentValue) {
   return [...accumulator, ...currentValue.bar];
}, []);
```
Looks normal right? 

Well it is, actually
