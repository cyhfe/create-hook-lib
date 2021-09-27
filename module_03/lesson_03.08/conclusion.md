---
title: Conclusion
isPublicLesson: true
---

In conclusion, despite its simplicity, Microbundle still has some flaws, the biggest being that it's not possible to add any Babel plugin or customization, even though Microbundle uses it under the hood. You also cannot inline Babel runtime helpers, so they will be reused. This contributes to smaller bundles for your apps. Yes - small often wins, but sometimes these things may be important to you. If you're new to all this tooling, Microbundle is the best choice to start with, and maybe continue with, depending on the size and needs of your library.

TypeScript is a bit more flexible, although as 95% of the modules published on NPM are compiled using Babel `importModules`, optimization is rather useless, but better than nothing. Absence of plugins and custom transforms make it an instrument for typechecking and declarations generation, but not as a compiler to the JS.

And the winner, despite its complexity, is Babel. I don't recommend it for less experienced users, but eventually, for maximum flexibility and power, bailing out into pure Babel for your libraries is the best thing that will fit all the use cases you can imagine.

We'll stick with Microbundle till the end of this course, so if you've been following along with Babel setup, you can revert it all by copying and pasting code into the next module's code folder.

Next up we have GitHub Actions, and autogeneration of documentation from `tsdocs`.
