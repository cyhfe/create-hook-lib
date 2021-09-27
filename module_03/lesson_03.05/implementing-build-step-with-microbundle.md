---
title: Implementing build step with Microbundle
isPublicLesson: true
---

Firstly, we're going to keep this simple and stupid by implementing the build step with a very handy tool named **Microbundle**, which abstracts away all the complexity of Babel, TypeScript and Rollup under one simple `microbundle` command.

We can begin by setting `sideEffects: false` in `package.json` to ensure our library is marked as tree-shakable.

Then let's set up Microbundle. Its setup is pretty straightforward.

```json
{
  "name": "react-hanger",
  "source": "src/index.ts",
  "main": "dist/index.js",
  "module": "dist/index.module.js",
  "esmodule": "dist/index.modern.js",
  "umd:main": "dist/index.umd.js",
  "types": "types/index.d.ts",
  "scripts": {
    "build": "microbundle --no-compress"
  }
}
```

It's also recommended to set `"module": "ESNext"` and `"target": "ESNext"` in our `tsconfig.json`.

Let's call our build command with `npm run build`.

Boom! We're done. That was easy huh?
