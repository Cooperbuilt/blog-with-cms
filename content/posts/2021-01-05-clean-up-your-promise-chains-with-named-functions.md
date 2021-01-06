---
template: post
title: Clean Up Your Promise Chains With Named Functions
slug: named-function-promise-chains
draft: true
date: 2021-01-06T00:38:59.378Z
description: >-
  Sometimes you have a promise that depends on a promise. This tends to result
  in a pyramid of peril (not as bad as the "pyramid of doom" from the callback
  days). If you want a more declarative, less pyramid-like option, look to named
  functions.
category: Deep Dive
tags:
  - Javascript
  - Promises
  - Deep Dive
---
I was recently working on a project that required multiple api calls, each predicated on the result of the previous call. In between each of these calls, the return data needed to be massaged and then forwarded to the next api call.

In pseudo-code, it went something like this:

```js
  fetchAllGitRepos(some data)
    .then(res => {
       
    })
```
