---
title: Module systems
isPublicLesson: true
---

JavaScript itself, in its initial version, had no module system whatsoever baked into the language. With time, some module systems were created. The most popular standards were:

- **CommonJS Modules** - these are the ones implemented in Node.js
- **Asynchronous Module Definition (AMD)** - a popular implementation of this standard is *RequireJS*. We don't care about AMD at all as it's dead.

Eventually with time it was obvious that a module system would have to be introduced to the language natively, so ECMAScript 6 (ES6) Modules were created. This is what we've been using in this course. We could dive deep into the differences between CommonJS and ES6 Modules, but this knowledge isn't essential to our course - although a simple snippet to compare them wouldn't hurt, would it?

Although simple snippet to compare CJS and ES modules just to know the basics will be nice isn't it?
Here we go.

Named exports and imports:
```js
// utils.js
const { merge } = require("lodash");

const doSomething = () => {
  /*...*/
};

module.exports = { doSomething };

// usage.js
const { doSomething } = require("./utils");

doSomething();
```

```js
// utils.js
import { merge } from "lodash";

export const doSomething = () => {
  /*...*/
};

// usage.js
import { doSomething } from "./utils";

doSomething();
```

Default exports and imports:
```js
// my-export.js
const myDefaultExport = () => {
  /*...*/
};

module.exports = { default: myDefaultExport }

// usage.js
const myDefaultImport = require('./my-export').default

/*...*/
```

```js
// my-export.js
const myDefaultExport = () => {
  /*...*/
};

export default myDefaultExport

//usage.js
import myDefaultExport from './my-export'

/*...*/
```

There is also a dynamic imports, but we will not dig into it much.
One big difference is that `require` call is synchronous, while dynamic ES6 import returns Promise that you have to handle.
It means that it's non-blocking, while `require` call will indeed block your thread.
```js
const { myFunctionFromModule } = require("/module-name.js")
```

```js
import("/module-name.js").then(module => {  module.myFunctionFromModule() })
// or in async function
const { myFunctionFromModule } = await import("/module-name.js")
```

Currently, ES6 Modules are widely supported by browsers, although not yet enough for full adoption.

Only Node 14 supports ES6 Modules and with a lot of exceptions and instructions to make it work. This is all done to ensure backward compatibility or interoperability between old modules that are still using CommonJS and new ones using ES6. There is an alternative to Node.js made by the author of Node, named Deno. It's built with baked-in native support only for ES6 modules.

One thing that is important for us to know is that ES6 modules are designed to be statically analyzed easily, in a way that gives a lot of power to the tools.

What power? And what tools?

We're going to find out in the next lesson.
