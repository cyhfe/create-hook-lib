---
title: Generating changelog
isPublicLesson: true
---

Another thing that we need to do is add the generation of changelogs. How are we going to do that? 

Simple, with commit messages! There is a tool named `auto-changelog` that does exactly what it says. We can quickly set it up now:

`npm install -D auto-changelog`

After it's installed, we need to configure it. In order to do that we need to change our `package.json`, adding these lines:

```json
{
  "auto-changelog": {
    "commitLimit": false,
    "template": "keepachangelog"
  }
}
```

`commitLimit` removes the limit on maximum commits per release,  and `template` tells the tool to use `keepachangelog` format which is a changelogs specification (read more about it [here](https://keepachangelog.com/en/1.0.0/)).

Why this exact format and why not default? The answer is because in the next publishing lesson, we will use a Github Action that will expect exactly this format.

Another thing we need to do is add a `.npmrc` file with a line to remove the default tag version prefix of `v`, like this:

```
tag-version-prefix=""
```

This means we will get `0.0.1` rather than `v0.0.1`. This prevents our auto-changelog tool from generating changelog logs with `v` prefix, which will make them truly compatible with
`keepachangelog` format.

Good. The last change to our `package.json` will be in the `scripts` section.

```json
{
  "scripts": {
    "preversion": "npm run clean && npm run build && npm run test",
    "version": "auto-changelog -p && git add CHANGELOG.md",
    "postversion": "git push && git push --tags"
  }
}
```

There's a lot going on here! Let me explain.

In order to bump up the version of our library we use `npm version` script. This script will take a parameter that may look like `minor`, `major`, `prerelease` etc.

We're going to run the following: `npm --no-git-tag-version version prerelease`.

We need the `--no-git-tag-version` flag to tell the `npm version` script:
- not to create a tag for this version in git 
- not to create a commit for this tag (because by default it will)

We're only doing it now to test if everything is working, without making actual changes to our repository. Before I press enter let me explain what will happen. The exact order of execution is as follows:

- Check to make sure the git working directory is clean before we get started. Your scripts may add files to the commit in future steps. This step is skipped if the `--force` flag is set.
- Run the `preversion` script. These scripts have access to the old version in `package.json`. A typical use would be running the full test suite before publishing, and this is what we do here. Any files you want to be added to the commit should be explicitly added using `git add`, but we don't need to add anything here, so we're good.
- Bump version in `package.json` as requested (`patch`, `minor`, `major`, etc).
- Run the `version` script. These scripts have access to the new version in `package.json`. Again, scripts should explicitly add generated files to the commit using `git add`. This is what we do here - we generate `CHANGELOG.md` and then add it to the commit.
- Commit and tag. In our dry run this is not happening.
- Run the `postversion` script. Use it to clean up the file system or automatically push the commit and/or tag. This is what we do too.

For now, as you can see our `CHANGELOG.md` is pretty empty as we don't have any tag published yet. To make `auto-changelog` take commit into the `CHANGELOG.md` you don't need to do anything, just make sure your commits are meaningful and if you worked in the branch for some time after all the issues are resolved, don't forget to squash all commits into one with a meaningful name.
Then you can merge stuff easily. All typical merge messages of major Git platforms such as Github, Bitbucket and GitLab are supported, and are ignored by default.

Okay, let's make a tag now.  Don't worry -  we'll be able to delete tags and squash commits later. Just run `npm version prerelease`.... everything is good. Let's check GitHub, and it's there - awesome!

Make some change to your code and commit it with "very meaningful message" as a message.

Let's run `npm version prerelease` again. Bam! It all worked again and as you can see a bunch of commits went into our old version that was previously empty, and the new version too! Amazing.

It all works, and it's time to delete these tags. Running `git push --delete origin 0.0.1-0` and `git push --delete origin 0.0.1-1` will do the trick.

If you want you can also squash your commit history using interactive rebase, like this:

[comment]: <GM: something to complete here>
`git rebase --interactive HEAD~N`
 <... shows how to do that>

Awesome. Let's move to the last step, how to publish our library!

