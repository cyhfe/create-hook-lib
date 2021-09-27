---
title: Setting up TypeScript
isPublicLesson: true
---

So far so good! We're going to use TypeScript throughout the course, so we have to set it up. Let's do that now.

The first thing to do, of course, is add a Typescript dependency.

Let's run `npm install typescript -D`.

After that we need to add the file `tsconfig.json`, a config that the typescript compiler uses to tune its various settings.

```json
{
  "compilerOptions": {
    "baseUrl": "src",
    "rootDir": "src",
    "lib": ["ESNext"],
    "target": "ES6",
    "module": "ES6",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "noEmit": true
  },
  "include": ["src"],
  "$schema": "https://json.schemastore.org/tsconfig"
}
```

Good, now we need to add yet another npm script: `"tsc": "tsc"`.

Now let's create our `src` folder at the root and within it an `index.ts` file, containing a simple test to see if it works as expected.

Ok, all good.

The last step for our initial developer config will be to add test tooling using jest.
