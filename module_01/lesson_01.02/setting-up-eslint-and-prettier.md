---
title: Setting up ESLint and Prettier
isPublicLesson: true
---

In this lesson we will setup ESLint and Prettier.

**ESLint** is a tool that scans your code for common mistakes and bad practice that may cause bugs, or just checks whether your code follows a particular style. It has lots of plugins to extend its default set of rules. Nowadays it's a standard tool for any JavaScript / TypeScript project.

**Prettier** is a formatting tool that automatically formats CSS, JavaScript, TypeScript, HTML, Markdown and many other files for you. It has some opinionated defaults but is partially configurable. It can be used standalone or integrated with ESLint via a plugin. We will integrate it into our ESLint pipeline because it's more convenient for daily use.

Open `package.json` to add all the plugins we need. We're going to use `react-app config`, because it's proven to catch only problems that may lead to bugs. It's built of multiple plugins and presets so we need to include them all in our package.json.

```
    "@typescript-eslint/eslint-plugin": "3.1.0",
    "@typescript-eslint/parser": "3.1.0",
    "babel-eslint": "10.1.0",
    "eslint": "7.2.0",
    "eslint-config-react-app": "5.2.1",
    "eslint-plugin-flowtype": "4.7.0",
    "eslint-plugin-import": "2.20.2",
    "eslint-plugin-jsx-a11y": "6.2.3",
    "eslint-plugin-react": "7.20.0",
    "eslint-plugin-react-hooks": "2.5.1",
```

We'll also need to add the prettier plugin for eslint and config, as well as `prettier` itself.

```
    "eslint-plugin-prettier": "3.1.3",
    "eslint-config-prettier": "6.11.0",
    "prettier": "2.0.5"
```

Now, let's run `npm install` to install them.

Next we need to add the actual config for ESLint and Prettier by adding the `.eslintrc.json` and `.prettierrc.json` files. 

Let start with `.eslintrc.json`. As I said before, the base config is a config from `create-react-app` which is a great default for anything React-related. On top of that we will add the Prettier config and plugin to integrate Prettier into ESLint. So here's how it looks!

```json
{
  "extends": ["react-app", "prettier"],
  "plugins": ["prettier"],
  "rules": {
    "prettier/prettier": "warn"
  }
}
```

The only rule we have added to the rules list is one that makes all prettier-related issues into warnings. Specific rules, if we're not using defaults, will be taken from the `.prettierrc.json` file. 

Create the `.prettierrc.json` file now in the same location.

```json
{
  "printWidth": 110,
  "semi": true,
  "singleQuote": true,
  "trailingComma": "all"
}
```

Good. The next thing to add is a couple of npm scripts to `package.json`.

```json
{
  "lint": "eslint ./src --max-warnings 0",
  "lint:fix": "npm run lint -- --fix"
}
```


`lint` will run ESLint against our codebase. `lint:fix` will do the same but also fix any fixable ESLint issues.

Another nice thing would be to enable a Prettier autofix on `file>save`. I'll show you how I do that on my **WebStorm** editor.

[comment]: <GM: instrucs to be added for both>

INSTRUCTION HERE FOR WEBSTORM

How would we do that in **VSCode**? Let me show you that as well.

CUT AND VIDEO ON HOW TO DO THAT IN VSCODE
