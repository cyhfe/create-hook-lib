---
title: Publishing library
isPublicLesson: true
---

The last step in making our library real is to publish it! Of course, after we ran `npm version` successfully with proper version bump we could execute `npm publish` and call it a day, but that would be boring. We want to automate stuff, so let's make another Github Action!

The idea is as follows. When we push a new tag, GHA should run a `publish` workflow that will publish our package to NPM and generate a Github Release based on our `CHANGELOG.md`. Here's how to do that.

```yaml
name: Publish to NPM

on:
  push:
    tags:
      - '[0-9]+.[0-9]+.[0-9]+*'
jobs:
  build:

    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [12.x]

    steps:
      - uses: actions/checkout@v2
      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v1
        with:
          node-version: ${{ matrix.node-version }}
      - uses: actions/cache@v2
        with:
          path: node_modules
          key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
          restore-keys: |
            ${{ runner.os }}-node-

      - name: Install Dependencies
        run: npm install

      - name: Building
        run: npm run build

      - name: Publish to NPM
        run: |
          npm config set //registry.npmjs.org/:_authToken=$NODE_AUTH_TOKEN
          npm publish
        env:
          NODE_AUTH_TOKEN: ${{ secrets.NPM_AUTH_TOKEN }}
      - name: Package lib
        run: npm pack

      - name: Release
        uses: docker://antonyurchenko/git-release:v3
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          ALLOW_EMPTY_CHANGELOG: "false"
          ALLOW_TAG_PREFIX: "true"
        with:
          args: |
            react-hanger-clone-for-course-*.tgz
```

Let's commit and push that, then run `npm version prerelease` and see if everything is working.

And it does! Amazing stuff.

[comment]: <GM: are you going to do any sort of rounding up or conclusion?>
