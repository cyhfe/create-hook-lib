---
title: Tree shaking
isPublicLesson: true
---

"What's tree shaking?" you may ask. 

> *Tree shaking is the process of removing unused code from your bundle.*

When you develop your modern web app, you usually use tools like Parcel or Webpack. These are **bundlers**. They take all your modular and human-readable code and transform it into a single file or "bundle". They also make sure that all the other resources for your web app like CSS, images, fonts etc are collected into one folder. 

For development, this process can be done without any optimizations, so it's quickly ready to test, but in production it can be done in a slow and highly-optimized way. Typical optimizations are:

- **Minification** - this means all your code will be on the same line, without any spaces. All variable and function names will be replaced with super-short names, as will many other things that can take less characters without changing the logic of the code.
- **Dead code elimination** - this means the removal of code that's never going to run no matter what you do. For example, you may have a useless `if` statement that is always `true`, with a `return` statement. All code below it will never run. This code would be identified as useless and deleted.
- **Code splitting** - this is where you send only the small chunk of code needed to render your first page, and for any other page, additional code will be downloaded on demand.
- **Tree-shaking** - this is almost the same thing as dead code elimination, but is based on modules and usage of these modules and their exports.

Imagine you're using a library of helper functions, let say `lodash`. Everyone loves `lodash` but the problem is that people mostly use less than ten of its dozens of functions. Without a tree-shaking optimization, if you imported, for example, the `merge` function like this:  `import { merge } from 'lodash'`, all of the functions available in `lodash` will be taken along and end up in your bundle, even though you only need `merge`. Tree-shaking will check what functions, classes, or constants are used and then takes only those into the bundle. So instead of the whole of `lodash` weighing 66.9 kilobytes, you will only import around 4 kilobytes. You can check if the library that you use is shakable by using services like Bundlephobia.com.

You might wonder why bundle size is so important. 

First of all, not all connections are equal, so if you're sitting somewhere in Europe, say Poland or Ukraine, you can expect super-stable half gigabit cable connection at home or in the office.  But, if we look at the cellular connection in these two countries, only three or four years ago, Ukraine had only 2G or so-called EDGE connection, whilst Poland has already been enjoying 4G for a few years. So your average user in Poland will download `lodash` within 5ms, but the average user on the streets of Kyiv will wait for whole second for it to download. Now imagine adding on top of it React, a few libraries, helpers, CSS-in-JS library or whatever, and you could quickly end up with a megabyte of gzipped JavaScript.

As connections may differ, so will the performance of the device itself. While a super-powered MacBook Pro will parse and compile downloaded JavaScript in a few milliseconds, a five- or six year- old smartphone may take more than 30 seconds to parse. An article from Addy Osmani back in 2018 called [The Cost of Javascript](https://medium.com/@addyosmani/the-cost-of-javascript-in-2018-7d8950fbb5d4) showed the example of CNN.com, and how much it would take for the average smartphone to parse versus a powerful computer. So whilst a desktop machine took only two seconds to parse JS, for an average phone it took 14 seconds, and the super-budget Alcatel X1 phone took 36 seconds! Absolutely crazy!

Tree-shaking is not a silver bullet and is just a small piece of the web performance puzzle, but when you develop a library you need to be 100% sure your code is tree-shakable whenever possible. How can we achieve this?

In the future, there should be only one requirement - use ES6 Modules! For now though, it remains slightly more complicated.  A simple set of actions for most libraries would be:

- Compile your code using Babel or any other transpiler, but make sure you keep ES6 Modules as your compile target. The popular Babel preset `@babel/env` compiles ES6 Modules to CommonJS by default, so you'll need to disable that behavior.
- In your module's `package.json` file set a property `sideEffect` to `false`.

That's it! If you will follow these two simple rules your app will be tree-shakable by Webpack and other bundlers such as Parcel and Rollup.

You might wonder what the `sideEffect` field is for. It tells the bundlers that your package's modules are side-effect free (meaning that none of your functions modify or rely on anything global). In other words, just importing this module without any concrete import definitions like `import 'my-module'` won't cause any code to run.


If that's still unclear, do you remember polyfills? Polyfills are code that runs before anything else and enable new features of the language to work in older browsers that don't support them natively. For example, old browsers don't have a `map` method on the Array prototype. As behavior of this method is pretty straightforward we can modify a prototype of Array by adding `map` function to the prototype if we see it's missing in the browser that runs our code.

Next up we're going to talk about what bundlers and transpilers are available on the market.
