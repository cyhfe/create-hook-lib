---
title: Setting up testing environment
isPublicLesson: true
---

First of all we have to add **Jest**. Jest is the most popular testing framework in the JavaScript ecosystem. It runs tests, has multiple matchers, reporters, lots of extensions and
plugins, and overall is a very well-made piece of software.

So as usual, lets  install it and add it to our development dependencies using the `npm install -D jest` command.

Then `npm i -D ts-jest @types/jest` to add support for TypeScript.

Okay, so now we have what we need to create a config for it. To do that, run:

`npx ts-jest config:init`

You should see that `jest.config.js` has been created in our project root.

The next step will be to change our test npm script in `package.json` to `"test": "jest"` and add watch mode script, which looks like:
`"test:watch": "npm test -- --watch",`

And now we're pretty much done! Let's see if it works.

Create `index.test.ts` and add the following code:

```ts
describe("test", () => {
  it("hello world", () => {
    expect(true).toBe(true);
  });
});
```

Now run `npm test`. It's green! Everything is working as expected.

As the last step, I also want to add a set of recommended Jest-related rules to our ESLint config.

Let's install the jest plugin to do that: `npm i eslint-plugin -jest -D`

Then add the configuration related to it into our ESLint config file `.eslintrc.json` so it looks like this:

```json
{
  "extends": ["react-app", "plugin:jest/recommended", "prettier"],
  "plugins": ["prettier", "jest"],
  "env": {
    "jest/globals": true
  },
  "rules": {
    "prettier/prettier": "warn"
  }
}
```

Congratulations! We've finished our initial developer environment setup, and are ready for the development of custom hooks. If you're not interested in that, skip straight to the **_Preparing Code for Publishing_** module.