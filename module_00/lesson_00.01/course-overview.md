---
title: Course overview
description: Course overview
role: "INTRODUCTION"
isPublicLesson: true
---

# Introduction

Hi everyone. I'm Andrey Los, CTO at Liveflow, ex- Lead Software Engineer at Revolut and co-author and maintainer of the `react-hanger` open-source project, which is in essence just a set of reusable custom React Hooks.

The main goal of this course is to teach you how to properly organize, compile, deploy and publish almost any reusable NPM library.

As an example library we will have a tiny version of `react-hanger` reimplemented step-by-step, going from nothing to a fully-automated publishing pipeline that follows the best practices.

If you're not familiar with the concept of React Hooks, we do recommend you get familiar with its basic concepts, although if your only interest relates to library publishing and tooling, you can skip through the hooks implementation details in the next two modules, straight into the module **_Preparing Code for Publishing_**.

Code in this course will be in TypeScript, so you may want to familiarize yourself with its basic concepts beforehand. But, again, if you don't care about the code or React, go straight to **_Preparing Code for Publishing_**.

In this module, we're going to lay out how to work with the course materials.

The next module will be all about setting up the initial things needed to make sure our tests are running, linters are checking our code and our TypeScript is type-checked.

The module **_Building a Small react-hanger Clone_** will cover the minimal implementation of a `react-hanger` library. We won't be including all the hooks, but if you're into React you can definitely learn a thing or two about testing React Hooks, typing them properly, and a little about how to approach your hooks API design.

In **_Preparing Code for Publishing_** we will dig a bit deeper into some of the details you need to know to prepare your code for publishing, so your consumers are happy with your library. We'll cover transpilation, tree-shaking, module systems, bundling and other tools that do all that stuff.

The last module will be all about Continuous Integration using Github Actions, generating changelogs automatically and of course publishing those changelogs into Github Releases.

With all that in mind, let's move to the next lesson!
