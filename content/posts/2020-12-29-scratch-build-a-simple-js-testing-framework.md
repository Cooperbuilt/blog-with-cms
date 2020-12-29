---
template: post
title: Scratch-Build a Simple JS Testing Framework
slug: scratch-built-js-testing-framework
draft: false
date: 2020-12-29T13:42:58.941Z
description: A practical guide to writing a simple JS testing framework in 22 lines of code
category: Javascript
tags:
  - Javascript
  - Education
---
Let's imagine a scenario. You're interviewing for a job and they ask you to build some sort of JS app that grows in complexity over time. You begin with a simple task and they continue to add acceptance criteria until your time runs out.

So what's the problem?

In these interviews, you will generally be using some JS platform, either proprietary like coderpad or freeware like JSFiddle. It's not an environment you would normally code in, and it almost never includes a testing suite.

More often than not, you have to write code and run it with your fingers crossed hoping it does what the interviewer wants. That's not how you write code in your daily life though, right?

Ideally we build out our acceptance criteria iteratively with tests validating each new piece of functionality.

But in an interview, you can't always guarantee you can import one of your favorite testing frameworks. You do your best to show off your developer skills without one of the most important tools in your toolbox - unit testing.

As a thought exercise, let's create a simple vanilla JS testing "framework" you can either copy pasta from a Gist or re-write at the beginning of your interview into any JS environment. This testing framework should feel familiar enough to be easy to use, robust enough to tackle most use cases, and simple enough to re-write from scratch.

Let's get started.

## Anatomy of a Modern JS Test.

Popular JS testing frameworks follow a basic testing structure like the following:

```js
test("does the thing", () => {
  expect(thing1).toBe(thing2);
});
// OR
describe("My grouped tests", () => {
  it("does what it should", () => {
    expect(thing1).toEqual(thing2);
  });
});
```

Tests can get far more complex from here:

```js
// Example from https://jestjs.io/docs
it("works with promises", () => {
  expect.assertions(1);
  return user.getUserName(4).then((data) => expect(data).toEqual("Mark"));
});
```

In the above examples, we see a few basic foundational elements:

### test/it function

**TL;DR**
The `test` function takes a test as a callback function, evaluates that callback, and signals either success or failure.

**Longer Form Description**
This function has two parameters - a string describing the test and a callback containing any number of _assertions_. Assertions are another word for the `expect something to equal something else` that we have seen in many forms in different testing frameworks. The test function evaluates the callback which either succeeds or fails. For the most part this is all handled in the same way - your framework aggregates successes and failures and outputs a console message with those successes and failures.

### expect function

**TL;DR** the expect function is the assertion you are making about your code. In human readable terms,

> "I _expect_ this koolaid to be the color red"

**Longer Form Description**
The expect function rarely exists by itself. Expect functions work in conjunction with a _matcher_ (more on that in a second). To take that human readable example above into some code:

`expect(koolaid.color).toBe('red');`

The expect function evaluates the argument, then based on the matcher used (toBe, toEqual, etc), makes some sort of comparison to the argument passed to the matcher, and either succeeds or fails that comparison. That success or failure response is bubbled up to the wrapping `test` or `it` function.

### matchers

**TL;DR** Matchers define how you want to test an expected value. Do you want a strict boolean (===) comparison? Do you want a loose comparison? Do you want to check if a value is `undefined`?

**Longer Form Description**
A matcher is the rest of the sentence `I expect this thing...`. For example, `I expect this crayon box "to contain" 12 crayons` and `I expect this function "to have been called"`
In most JS testing frameworks, matchers are _chained_ off of the `expect` function. This makes for a fairly human readable test structure - `expect(multiply(2, 11)).toEqual(22)`

## Defining Our Acceptance Criteria

Now that we know the basics of a modern test, we need to go back to our use case and define some acceptance criteria.
Let's start by re-stating the problem -

> As a developer, I don't have guaranteed access to unit testing frameworks in interviews. I need a simple testing method I can reproduce in an interview to leverage TDD.

From that, we can assume some acceptance criteria:

1. Create a testing method that has the core functionality of Jest / Mocha / Chai
2. Simplify that method enough to be easily copy-pastable and/or reproducible

## First Pass

### Writing matchers

Requirements:

1. Provide a `toBe` function that compares with strict equality standards
2. Provide a `toEqual` function that accurately compares two values and returns true if they are equal regardless of type

`if they are equal regardless of type` => If this seems like a logical leap to you, especially the "regardless of type" part, that's because it is. Matching can get quite complicated.

We've talked about toBe and toEqual in some of our examples. In modern frameworks, `toBe` matches primitives using `Object.is` and toEqual is capable of matching objects and arrays, as well as loose equality.

Let's take a stab at each:

**toBe**

```js
function toBeMatcher(value, expected) {
  return Object.is(value, expected);
}
```

This strict equality check will be able to handle undefined and null checks as well as basic strict value comparison. For more info on `Object.is`, see [this mdn link](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is)

**toEqual**
This one gets a little tougher. If we were to write this for just handling multiple types, we would compare the value and expected with `==` (for more info on the abstract comparison algorithm, check out [this link](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness))

If we did that though, we wouldn't be able to compare arrays or objects, which cuts out a huge swath of functionality for us.

Using JSON.stringify gives us a way to handle a bunch of cases:

1. Shallow objects
2. 1D arrays
3. Almost all of the cases that toBe handles.

However, if we ONLY use JSON.stringify, we will run into comparison issues for things like toEqualMatcher(1, '1'); 

To handle loose equality checks and arrays and objects, we can add a loose equality check first, followed by a JSON.stringify approach. This covers most of our bases.

Here's one take: 
```js
function toEqualMatcher(value, expected) {
  return value == expected || JSON.stringify(value) === JSON.stringify(expected);
}
```

**NOTE**
This comparison will only fail for arrays with the same items, but different orderings. You can pre-sort the arrays before use if that fits your use-case. 

### Writing `expect`

Function requirements:

1. Takes a value
2. Returns an object of matchers
3. Each matcher returns true or false

Let's break down `expect(multiply(2,11)).toBe(22)`

The expect function above takes an initial value, in this case a function that evaluates to a value, and returns an object with our matchers. This creates a closure
over the initial value to give our matchers something to compare
to.

A simple reproduction looks like this:

```js
function expect(initialValue) {
  return {
    toBe(expected) {
      return toBeMatcher(initialValue, expected);
    },
  };
}
```

By calling `expect()` we immediately return the object which contains the matcher function (in this case, just toBe). Because the evaluation of `expect` returns an object, we are then able to access our matchers via dot notation (`expect(thing1).toBe(thing2)`)

Go ahead and throw this function in your console and play around with it.

Using the closure pattern seen above, we can add as many new matchers as we want. Right now, we only have a strict equality check. We could easily add a loose equality check as well using the other matcher we wrote above:

```js
function expect(initialValue) {
  return {
    toBe(expected) {
      return toBeMatcher(initialValue, expected);
    },
    toEqual(expected) {
      return toEqualMatcher(initialValue, expected)
    }
  }
}
```

### Writing `test/it`

Function requirements:

1. Takes a string title and a function callback
2. if the callback evaluates to true, return a success message with the title
3. If the callback evaluates to false, return a failure message with the title

In our simple testing framework, we can do the following:

1. If the callback returns true, the test function should console log the test title string followed by some sort of pass emoji.
2. If the callback returns false or throws an error, the test function should console.log with the test title string followed by some sort of fail emoji

Here is a simple swing:

```js
function test(title, callback) {
  try {
    const result = callback();
    if (result) {
      return console.log(`✅ ${title}`);
    }
    return console.log(`❌ ${title}`);
  } catch (error) {
    console.log(`❌ ${title}`);
    console.log(`🚨 Error Message: ${error}`);
  }
}
```

### Simplifying It
We have defined our matchers, our expect function, and our test function. Given our original acceptance criteria, we should take a hard look at how to simplify this 
so you can easily reproduce it under stressful circumstances.

Simplification steps:
1. Don't write our matchers outside of the expect. These functions can be in-lined to the expect to lessen the total burden of writing.
2. Explicitly name our matchers something that will help you practically apply them
3. Replace emojis with plain text so you don't need to hunt down emojis during your interview

## Bringing it All Together
You are at your interview. The interviewer gives you a prompt. You are short on time, but you know what to do. Instead of jumping right into solutioning - you tell your interviewer you are going to set up a small test suite. You let them know an ounce of preparation is worth a pound of cure as you type out the following:

```js
function expect(initialValue) {
  return {
    toStrictlyEqual(expected) {
      return Object.is(initialValue, expected)
    },
    toLooselyEqual(expected) {
      return initialValue == expected || JSON.stringify(initialValue) === JSON.stringify(expected);
    }
  }
}

function test(title, callback) {
  try {
    const result = callback();
    if (result) {
      return console.log(`PASS!: ${title}`);
    }
    return console.log(`FAIL: ${title}`);
  } catch (error) {
    console.log(`FAIL: ${title}`);
    console.log(`ERROR: Error Message: ${error}`);
  }
}
```

Your interviewer looks upon you with awe and immediately offers you the position (just kidding). But in 22 lines of code, you have given yourself some of the creature comforts of a test environment in what is normally semi-hostile and foreign environment. If you want to copy-pasta this in your next interview, [here is a gist](https://gist.github.com/Cooperbuilt/574ba683efc2382c34e9d44c44cccbbe) or you could memorize it / make it your own. 

Best of luck! 

