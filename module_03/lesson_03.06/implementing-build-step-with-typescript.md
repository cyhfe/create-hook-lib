---
title: Implementing build step with TypeScript
isPublicLesson: true
---

Implementing the build step with TypeScript is pretty simple too, as we already have TypeScript working for us.

First we need to create `tsconfig-cjs.json` to use it to compile CommonJS modules. Its contents will look like this:

```json
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "module": "commonjs",
    "outDir": "./cjs",
    "declarations": true
  }
}
```

In our main `tsconfig.json` we need to make sure we have the following settings:

- `outDir` set to `./esm`
- `declarations` set to `true`
- `declarationDir` set to `./types`
- `importHelpers` set to `true`

While three of those are pretty self-explanatory, `importHelpers` may need some explanation.

When modern and fancy syntax is compiled to an older, compatible version of JS syntax, TypeScript generates a lot of helpers code that it puts alongside each file that needs it. But if you have numerous files these helpers get duplicated multiple times, creating a lot of useless code that can be easily reused. To optimize that, there is a module named `tslib` that consists of all the runtime helpers that TypeScript generates. The flag `importHelpers` in `tsconfig` imports these helpers from `tslib` package, instead of generating them inline. 

We therefore need to add `tslib` as a dependency, so it will be installed along with our module.

The last step will be to change `package.json`:

```json
{
  "name": "react-hanger",
  "source": "src/index.ts",
  "main": "cjs/index.js",
  "module": "esm/index.js",
  "types": "types/index.d.ts",
  "scripts": {
    "build:esm": "tsc",
    "build:cjs": "tsc -p tsconfig-cjs.json",
    "clean": "rimraf types cjs esm",
    "build": "yarn clean && yarn build:esm && yarn build:cjs"
  }
}
```


We've removed UMD as this is pretty useless nowadays. With Microbundle it's easy, but with Typescript it would require a lot of changes so as we were focusing only on CommonJS and ES Modules, we're going to ignore it.

Let's call our `build` command and see how it works. All good!

This type of build is pretty simple to implement and the results it gives are as good as `microbundle`. It also enables old-fashioned CommonJS manual "tree-shaking" where Node.js users still import CommonJS files directly file by file, or via `babel-import-plugin`. 

Microbundle puts all of the CommonJS code in one file, so no matter if you're using one function from the library or a hundred, Webpack (or another bundler) will grab them all. When you use raw Typescript Compiler or raw Babel, which we will implement in the next lessons, you have the option to have file-by-file exports, which should improve and in some way guarantee (with careful manual work) that nothing unused will land in our bundle.

"Great stuff!", you might think. And it sure is, but as CommonJS tree-shaking is now coming to all major bundlers, you may no longer care about it. With the current state of tooling, the benefits of having separate files for each function/hook/component instead of one library bundle are becoming increasingly marginal. However, as an exercise, and to get a good understanding of how these tools work, we will still implement our build scripts using them, and without any wrapper tooling like Webpack babel-loader, or Rollup babel-plugin.

Ok - let's implement the same thing using Babel!
