---
title: Tools for processing our code
isPublicLesson: true
---

Let's start with transpilers. Here are the options:

- Babel
- TypeScript Compiler
- SWC


[**Babel**]( https://babeljs.io/) is a *de facto* standard created around 2014. It was built to translate new modern JavaScript syntax to older syntaxes supported by browsers. In time people started using it for other purposes such as automating code migrations, so-called codemods, or compiling away dynamic code to static code to save runtime. Many libraries like `styled-components` and of course `react` are likely to have babel plugins that improve something in one way or another, using a compilation step to get some nice DX or performance improvements. Since version 7 Babel also supports TypeScript which makes it even more powerful.

[**TypeScript Compiler(TSC)**](https://www.typescriptlang.org/) is widely-used as a transpiler from TypeScript to some particular version of Javascript. Nowadays TypeScript tends to be used as a type-checker only, because Babel effectively compiles TypeScript to JavaScript whilst applying multiple library plugins of all sorts. TypeScript Compiler is *not pluggable* so there's no way to implement custom-made plugins that will affect the compilation of the code.

There is project named `ttypescript` that provides this capability by applying custom-made pluggable transforms. It's an amazing option for those projects that are still using TSC as the main compiler without Babel. It will slowly die out because of Babel's popularity and wide support.

Lastly, the most niche option is **SWC**. It's very similar to Babel and takes a lot of inspiration from it. It's also fully-pluggable, supports TypeScript and has most of the standard Babel plugins, though is unfortunately not compatible with third-party Babel plugins.

Unlike Babel, SWC is written in Rust, a super-quick compiled language that is as performant as C++, but way easier to work with. The current version of SWC outperforms Babel by a magnitude of 18 times in the worst-case scenario, and in some cases SWC is 50 times faster! It's almost a drop-in replacement for Babel, but due to the low popularity of Rust compared to JavaScript its development is pretty slow. I really hope it will get more traction soon as on a huge project, compile times can still be way too long.

Ok - before we move to the next lesson, where should we set Babel up?  I will need to explain a bit about custom-made plugins.

Custom plugins enable users to extend the base official functionality of Babel - for transpilers this means some pluggable transforms. So what exactly is a transform?

If you're advanced enough, you may know about **abstract syntax trees (ASTs)** and how to manipulate them. This is exactly what Babel plugins do, but this topic is way too advanced and may take another course to cover in depth. So let's try to put it simply. Babel plugins are functions that simply *take code in a format that is easy to operate on and outputs another code, based on logic provided by a developer.*

Let's look at an example, starting with `babel-plugin-lodash`, which changes wildcard imports like `import _ from 'lodash'` or `import { map, merge } from 'lodash'` to make them more precise, for example `import map from 'lodash/map'` and `import merge from 'lodash/merge'`, by finding their usages in the code. Why is this needed? Because with CommonJS modules, import of any function from `lodash` directly takes *all* `lodash` internals to the bundle. So to optimize that without affecting the code, this plugin comes in to save the day.

Hopefully these optimizations will soon be obsolete as Parcel, Webpack and Rollup have all introduced CommonJS tree-shaking functionality in their latest versions.

There are other plugins, such as `babel-plugin-polished` and `babel-plugin-styled-components` which make some dynamic code static if it's possible to predict. A good example would be `lighten` or `darken` functions, which lighten or darken original color by a percentage. So code like:

```js
import { lighten } from "polished";
const color = lighten(0.2, "#FFCD64");
const backgroundColor = lighten("0.2", "rgba(204,205,100,0.7)");
```

would be transformed to the following, removing the dependency on `polished` completely and removing two function calls from the code.

```js
const color = "#e5e6b1";
const backgroundColor = "rgba(229,230,177,0.7)";
```

As you can see this concept it pretty powerful and can be used for many things.

Good stuff! Let's move on and implement our build step next.
