# Git Workflow Practice

## Overview

I will be using this repo to note down the things I have learned from working with git. For example, using git rebase, git merge, and so on. I will attempt to provide visuals to show my understanding of these topics. Also, i hope to provide edge cases when attempting these workflows.

## Current Git Tree

```
Master Branch
A---B---C{HEAD}
```

## Git reset

`git reset HEAD^` can be used to reset a commit that is _before_ a branch's `HEAD` pointer. This takes you back to commit(B), which is _before_ the `HEAD` commit(C) pointer, along with the changes that _were_ about to be committed in commit(B). In other words, this resets, or unstages, the staging area to what it was at commit(B). We would have to stage the changes again with `git add` and commit the staged changes to create commit(C) again. This allows us to make additional changes to the files that _were staged_ and committed on commit(B) to create commit(C). Also, we may add more additions, such as files or features, if chosen to do so. More simply, this sets commit(B) with changes _before_ the staging state.

`git reset --soft HEAD^` works similarly, but instead of resetting the staging area, the changes are kept intact and all that is needed is to commit the changes. This could be useful whenever some additions need to be made, such as files or features, without resetting the changes that _were staged_ for commit on commit(B) to create commit(C). More simply, this sets commit(B) with changes _after_ the staging state.

`git reset --hard HEAD^` works more destructively and instead overwrites the working directory to the initial state of commit(B). For example, any changes that were prepared to be staged and committed to create commit(C) would completely be overwritten and possibly destroyed. Only use this whenever a commit needs to be completely destroyed and start cleanly from commit(B). More simply, this sets commit(B) to its inital state with no changes _before_ or _after_ the staging state.

#### Scenario

Let's assume we commited commit(C) with some changes to an html file in the `Master Branch`. These changes needed to be in a dedicated branch called `Feature-Branch`. For this scenario, we can use `git reset HEAD^` so that we can stash the changes and branch off from commit(B) to the `Feature-Branch`.

```
Master Branch
A---B---C
        This commit needs to reset and added to a new branch
```

Run `git reset HEAD^`:

```
Master Branch
A---B
    We have reset the HEAD to commit B with changes made before adding to staging area.
```

Now run `git stash` to store the changes
Then run `git branch Feature-Branch` to create the new branch
Lets move to the branch using `git checkout Feature-Branch`
Finally, unload the changes into the new branch that we stashed earlier. `git stash pop`
Stage the changes with `git add .`
Commit the changes in the new branch `git commit -m "commit(C)"`

```
Master Branch
A---B
     \
      C
      Feature Branch

Note: There is an edge case i will provide in another article called git-edgecase-1.md
```

## Git commit --amend

`git commit --amend` is a command in git that allows us to rewrite the last commit. It's important to note that when using this command, git completely replaces the last commit with a new commit, which includes the changes that were amended.

#### Scenario

Now that we moved our changes to a new branch `Feature-Branch` we can start working on our feature, but wait, there was a typo in the html file! This fix is no big deal with `git commit --amend`. We can patch this up without it looking like we made this typo in the first place.

Let's do that now but first, for visualization, this is what the git tree looks like currently:

```
    Master Branch
A---B
     \
      C
      Feature Branch
```

Now make sure we're in `Feature-Branch` with `git checkout Feature-Branch`.
Then, make sure the last commit is commit (C) so we can amend the last commit.
Go to index.html and change the `<p>hello wolrd<p>` to `<p>hello world<p>`
Stage the html file with `git add index.html`
If all looks well, now we can run `git commit --amend`.

An interactive window will appear asking to change the commit message. In this case, we will change the commit message to "commit (C) amended". Now close the window. The changes should now be set and you should see that commit (C) has a new hash, which shows that it was replaced with a new commit.

The current tree should still look similar, but remember that commit (C) is a new commit

```
    Master Branch
A---B
     \
      C(modified)
      Feature Branch
```

`WARNING: MAKE SURE YOU'RE NOT WORKING WITH COLLABORATORS ON THE SAME BRANCH WHEN USING THIS COMMAND`

Finally, let's add a new javascript file and commit it to the `Feature-Branch`. Create a new `app.js` file and add `console.log('I am a javascript file!)`. Stage the change `git add app.js` and commit with `git commit -m "commit (D)"`

#### When can we use this command?

We can use this command whenever we want to fix a mistake in the last commit or add a file that we forgot to add to the staging area. The caveat to this is that this command should only be used when other collaborators are not working on the same branch where these commit changes lie in.
