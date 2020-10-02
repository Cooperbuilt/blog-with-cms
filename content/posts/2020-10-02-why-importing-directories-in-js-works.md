---
template: post
title: Why Importing Directories in JS Works
slug: importing-directories-in-js
draft: false
date: 2020-10-02T11:57:54.570Z
description: >-
  Have you ever seen an import statement where a file name is never specified?
  Something like Import Button from './components/button'. This is importing a
  directory, and yet it works? Let's figure out why.
category: JavaScript
tags:
  - JavaScript
  - Deep Dive
  - Node
---
# JS, Imports, and Index.js

Have you every seen a React import like this and just trusted that it works? 

```jsx
import Button from "./components/button"
```

This is importing a directory, not a button. But lo and behold, when you spin up your app alarm bells **don't** go off and your beautiful button shows up. 

But how? 

## Starting with File Structure

There are a few file structures that land us in this situation - the `index.js` file with a component defined within it, and the `index.js` file that acts as a public interface. Both are a matter of choice that feature importing a directory to use a component.

### File Structure - Example 1

**Index.js Components**

In this setup, component code is written directly in the `index.js` of each directory, with any supporting code residing in that same directory. Context is provided by the directory name, not the component file name. 

**Example Directory Structure**

```jsx
├── src
| ├── index.js <!-- Application root -->
| ├── components/
| ├──── button/
| ├────── index.js
| ├────── button.css
| ├──── form/
| ├────── index.js
| ├────── form.css
```

**Example `/button/index.js`**

```jsx
const button = ({onClick, text}) => (
	<button onClick={onClick}>
    {text}
	</button>
);

export default button;
```

**Example Import**

```jsx
import Button from './components/button'
```

### File Structure - Example 2

**Index.js As an Interface**

In this setup, component code is written in explicitly named files (button code is written in `button.js` form code is written in `form.js` etc) and the `index.js` file is used as an interface that exports the public API. *I've made a small codesandbox link [here](https://codesandbox.io/s/serene-albattani-1s1go?fontsize=14&hidenavigation=1&theme=dark) to see this in action.*

**Example Directory Structure**

```jsx
├── src
| ├── index.js <!-- Application root -->
| ├── components/
| ├──── button/
| ├────── index.js
| ├────── button.js
| ├────── button.css
| ├──── form/
| ├────── index.js
| ├────── form.js
| ├────── form.css
```

**Same Example Button**

```jsx
const button = ({onClick, text}) => (
	<button onClick={onClick}>
    {text}
	</button>
);

export default button;
```

**Example `index.js` File**

```jsx
export { default as Button } from "./button.js";
```

**Example import**

```jsx
import { Button } from './components/button';
```

On a side note, this public interface style lends itself well to an `index.js` file at the root of your components that handles exporting all of the components you consider to be "public". For example: 

```jsx
├── src
| ├── index.js <!-- Application root -->
| ├── components/
| ├──── index.js <!-- Components root -->
| ├──── button/
| ├────── index.js
| ├────── button.js
| ├────── button.css
| ├──── form/
| ├────── index.js
| ├────── form.js
| ├────── form.css
| ├──── someInternalComponent/
| ├────── index.js
| ├────── someInternalComponent.js
| ├────── someInternalComponent.css
```

This results in an index file that looks like:

```jsx
export { default as Button } from "./Button";
export { default as Form } from "./Form";
```

This style of interface relies on directory imports at two levels and makes using components a breeze throughout the app.

---

This was a bit of an aside. What we came here for is a better understanding of how/why we can import a directory: 

```jsx
import { Button } from './components/button';
```

and it works.

For that, we need to dive into some details

## How Imports Resolve

Let's get something out of the way. Import syntax and require syntax are functionally equivalent. If you disagree, check out [this repl](https://babeljs.io/en/repl#?browsers=&build=&builtIns=false&spec=false&loose=false&code_lz=JYWwDg9gTgLgBAbzgCwKYBt0QIxwL5wBmUEIcA5AHQD0amE5AUIwMYQB2AzhOqpVgHMAFHSzYhASglwgA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=es2015%2Creact%2Cstage-2&prettier=false&targets=&version=7.8.4&externalPlugins=)

So when we `import { Button } from './components/button';` we are actually writing something like `var Button = require('./components/button')`

Knowing this allows us to focus our energy on require syntax to solve our question. For that, we go to the docs. Particularly [this section](https://nodejs.org/api/modules.html#modules_all_together): 

```jsx
LOAD_AS_DIRECTORY(X)
1. If X/package.json is a file,
   a. Parse X/package.json, and look for "main" field.
   b. If "main" is a falsy value, GOTO 2.
   c. let M = X + (json main field)
   d. LOAD_AS_FILE(M)
   e. LOAD_INDEX(M)
   f. LOAD_INDEX(X) DEPRECATED
   g. THROW "not found"
2. LOAD_INDEX(X)

LOAD_INDEX(X)
1. If X/index.js is a file, load X/index.js as JavaScript text. STOP
2. If X/index.json is a file, parse X/index.json to a JavaScript object. STOP
3. If X/index.node is a file, load X/index.node as binary addon. STOP
```

This is some pseudo code from the friendly folks over at `node.js`. Let's break it down:

When calling `require` with a directory, for example `import { Button } from './components/button';`, node will check if `'./components/button/package.json'` exists. If there is no `package.json` at this file location, node will move to the `LOAD_INDEX(X)` directive. 

In this section, node will check if `'./components/button/index.js'` exists. **If it does, it will load this file as a Javascript text.** In the cases we have defined above, node would find an `index.js` file with either a component defined within or a pass-through export. Either way, the `index.js` is read and executed and your beautifully crafted button shows up as expected.

## A Quick Roundup

Importing a directory works if there is an `index.js` file located within that directory with an export defined. Using this pattern gives you some great flexibility in how you structure the directories and file names of your project. 

Knowing how this magic works gives you the power to leverage it willingly. For example, structuring your project to only use directory imports (`import Button from './components/button` instead of `import Button from './components/button/button.js`) means the underlying component names and file structure can change as much as necessary without blowing up all your import statements.
