---
title: What's transpilation.
isPublicLesson: true
---

You may have heard the term transpilation before, but just to be sure that everyone is on the same page I'll explain what it is and why it's needed. 

Transpilation is a process of *transforming one code into another code*, mostly human-readable code in one version of a language into another version *or* another language. Many people prefer using the word "compile", which is also correct, but let's stick to "transpilation" for this lesson.

Examples of use cases for transpilation would be:

- from one programming language into equivalent code in another programming language
- from older versions to newer version of a language - for example a transpiler that supports Python could transpile code from Python 2 into Python 3
- from modern syntax towards an older version of syntax for backward compatibility
- refactorings, migrations etc can be automated in some cases when you have a proper transpiler at hand

So what are the common examples of transpilers in the JavaScript ecosystem?

**TypeScript** compiler is partially a transpiler. Another *de facto* standard in the JavaScript ecosystem is **Babel**.

TypeScript compilation is, aside from typechecking of our code, about transpilation of TypeScript code into some target JavaScript code. 

It's the same with Babel, but instead of transforming from one language to another it works mostly on transforming modern JavaScript syntax into an older version of JavaScript, removing all the fancy new syntax constructions or features to ensure maximum compatibility with older browsers or Node versions.

As a side note - yes, Babel also transforms TypeScript into JavaScript, out-of-the-box, from version 7 .

We're going to use Babel for our transpilation requirements. I'll explain how this will help us later in the course.

In the next lesson you'll get to know the most popular module systems for JavaScript. 
