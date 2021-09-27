---
title: Collecting hooks under index
isPublicLesson: true
---

Alright. Now as we have two hooks it would be nice to create a single point from which we can import them. For that we will create an `index.ts` file in the `/array` folder root. Let's re-export all types and functions from our files using `export from` syntax.

```ts
export { useBoolean, UseBoolean, UseBooleanActions } from "./useBoolean";
export { useMap, UseMap, MapOrEntries, UseMapActions } from "./useMap";
```

You may be tempted to use `*` to export everything, but this is not recommended because it gets transpiled to dynamically exported `require`s using `for` loops that are really hard to statically analyze with tools like Webpack, Rollup and other bundlers.

So, explicit static exports are usually a better choice, at least until ES6 modules are no longer new and common.js modules have been completely deprecated.

Next up we will quickly implement object-based API hooks and will export them the same way.
