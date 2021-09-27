---
title: GitHub Actions
isPublicLesson: true
---

The next thing to add is **continuous integration (CI)**, using **GitHub Actions (GHA)**. Continuous integration is a set of automated tasks or jobs that are run, usually triggered by some event in the repository. The goal of those tasks is usually to check whether functional tests are still green, and compilation has been successful.

Bear in mind that CI tasks are not limited to functional tests. They may check for other things such as whether code follows any format or naming conventions, or whether test coverage stays above its target percentage. It can also automatically publish your package, deploy your app and so on.

In other words, a CI service or platform gives you the ability to run any script on a set of triggers or events that help achieve some goal in a consistent and regular way.

Let's take a look at GitHub Actions.

This is a CI platform provided by GitHub. Its typical events include pushes into the repository, new pull requests, new tag pushed etc. The first workflow that we're going to implement will be a typical "run tests, run lint and check if it's all good" triggered by any push to the master or new pull request. If any of the jobs fail it means that the code is broken in some way and that it needs to be fixed before it can be merged into the master branch. If we wanted to push directly to the master where we have the rights, then at least we'll get notified that we've missed something.

So, to get our CI working with GHA, we need to create the folder `.github/workflows` and then the file `ci.yml` inside it, and paste in this code:

```yaml
name: Node.js CI

on:
  push:
    branches: [master]
  pull_request:
    branches: [master]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v2
      - name: Use Node.js 12.x
        uses: actions/setup-node@v1
        with:
          node-version: 12.x
      - uses: actions/cache@v2
        with:
          path: node_modules
          key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
          restore-keys: |
            ${{ runner.os }}-node-
      - name: Install Dependencies
        run: npm install
      - name: Linting
        run: npm run lint
      - name: Testing
        run: npm run test
      - name: Building
        run: npm run build
```

Let me explain step-by-step what's happening here.

- First we name our workflow. 

- Then we specify which events should trigger this workflow to run. In our case it's on each pull request opened against the master, or direct push into the master branch.

- After that we set the jobs that have to run to ensure our codebase is passing all the checks. We run it on latest ubuntu. 

- Then we finally describe our steps (that are made of actions).

What does "action" mean here? In GHA it's a piece of workflow script that hides inside itself another script (we could say it *encapsulates* its logic). Another example would be a regular JavaScript function that has actions passed in its parameters. We may write some custom actions, or we may reuse predefined or community-created actions.

- The first, mandatory, step is to run a checkout action that will checkout our code from the repository.

- Then we install node with the latest LTS (long-term support) version (14 being the latest LTS version at the time of this course)

- Next we use another predefined action that enables `node_modules` caching, so unless we change `package-lock.json`, `node_modules` won't be installed from scratch, but can be
quickly downloaded from the GitHub servers.

- Our next step `npm install` will therefore just be happy checking that all dependencies are already there, effectively skipping the step.

- Next we're running the lint, test and build commands. 

If they're all green then we're good to merge our pull request, or if we were YOLO pushing into the master, we can be sure our changes weren't destructive in any way.

[comment]: <GM: do you need to also say about setting up a repo - and does it need our files in it?>

Ok, so let's commit and push the changes, and then head to GitHub to see if it runs now.

It runs... it's a success! If somebody opens a pull request into the master to contribute to our library, this workflow will run against their changes to ensure quality.

Let's try breaking something, then create a branch, commit and push it, and then open a pull request with the master branch as a target. As you can see our workflow started.... and it failed as expected. A merge will also be impossible until we fix our code.

That's our first, basic and most important GHA Workflow. We will implement few more in the following lessons.

If you're wondering if there are other CI platforms, there are plenty, including Circle CI, Travis CI and many others. Since Github Actions is the most native to GitHub and pretty popular
nowadays, we're going to stick with that. 

In the next lesson we will learn how to generate changelogs for our library.
