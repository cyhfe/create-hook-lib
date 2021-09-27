---
title: Implementing build step with Babel
isPublicLesson: true
---

The Babel setup is the most complex as it is lower-level and more customizable than TypeScript and other similar tools. I could now start to explain all the plugins, transforms, presets and other stuff you need to install in order to make your own Babel preset, but in my experience it's just not worth it. The easiest solution of all is to just use `babel-preset-react-app`. 

So, what is this preset and why you should trust it?

You may have heard of `create-react-app` - a ready-to-go CLI tool that provides the developer with everything they need for React app development, testing and building for production. As this tool has basically abstracted away Jest, ESLint, TypeScript, Babel and Webpack, it has some modules that provide a standalone usage. One of these is `babel-preset-react-app` a preset that is ready to compile TypeScript and Flow, and includes all the required optimizations, along with a minimal API to finetune some pieces of it.

To use it you just need to install `@babel/cli` to run Babel from the CLI, and `babel-preset-react-app` to install everything it needs.

Now let's create a `babel.config.js` file:

```js
const isCjs = process.env.IS_CJS === "true";
process.env.NODE_ENV = "production";

module.exports = {
  presets: [
    [
      require("babel-preset-react-app"),
      {
        useESModules: !isCjs,
        typescript: true,
        flow: false,
        helpers: true,
        absoluteRuntime: false,
      },
    ],
  ],
  ignore: [
    "**/src/**/*.d.ts",
    "**/src/setupTests.js",
    "**/src/setupTests.ts",
    "**/src/**/__tests__/**/*.ts",
    "**/src/**/__tests__/**/*.tsx",
    "**/src/**/*.test.tsx",
    "**/src/**/*.test.ts",
  ],
};
```
So what's this setup doing?

- Telling Babel to ignore certain files.

- Enabling TypeScript compilation.

- Disabling support for Flow,  a zombie competitor of TypeScript. 

- Giving us an option to compile down to ES Modules or CommonJS modules depending on the `IS_CJS` flag.

- Enabling runtime `helpers` - the same as we did for the Typescript option `importHelpers`, but instead of `tslib` we're going to add `@babel/runtime` package to our dependencies list in `package.json`, removing `tslib` which is now useless.

Another thing we need to change is our `tsconfig` in order to emit declarations only, and no JS code. To do that add a `emitDeclarationsOnly` flag and set it to `true`. Also you may remove `tsconfig-cjs.json` as it will not be used any more.

The final step will be to change our build commands to reflect our changes.

```json
{
  "scripts": {
    "build:types": "tsc",
    "build:cjs": "IS_CJS=true babel --out-dir cjs --extensions \".js\",\".jsx\",\".ts\",\".tsx\" --source-maps",
    "build:esm": "babel --out-dir esm --extensions \".js\",\".jsx\",\".ts\",\".tsx\" --source-maps",
    "clean": "rimraf types cjs esm",
    "build": "yarn clean && yarn:types && yarn build:esm && yarn build:cjs"
  }
}
```

Let's run build: `yarn build`..... and all good! Nice!

If you're really interested you can dig deeper into the internals of `babel-preset-react-app`. You will learn a lot about its optimizations, presets, transforms and what they all do. If your library uses `lodash`, `styled-components` or any library that has some optional babel plugins you may install them too and add them as plugins to your babel config. To do that add  a `plugins` key to your babel config file below `presets` with the plugins as an array, like `plugins: ['lodash']`. For more information, read the documentation for Babel or the plugin itself.
