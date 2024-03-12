# Git Workflow Practice

## Overview

I will be using this repo to note down the things I have learned from working with git. For example, using git rebase, git merge, and so on. I will attempt to provide visuals to show my understanding of these topics. Also, i hope to provide edge cases when attempting these workflows.

## Current Git Tree

```
Master Branch
A---B
```

## Git reset

`git reset HEAD^` can be used to reset a commit that is _before_ a branch's `HEAD` pointer. This takes you back to commit(B), which is _before_ the `HEAD` commit(C) pointer, along with the changes that _were_ about to be committed in commit(B). In other words, this resets, or unstages, the staging area to what it was at commit(B). We would have to stage the changes again with `git add` and commit the staged changes to create commit(C) again. This allows us to make additional changes to the files that _were staged_ and committed on commit(B) to create commit(C). Also, we may add more additions, such as files or features, if chosen to do so. More simply, this sets commit(B) with changes _before_ the staging state.

`git reset --soft HEAD^` works similarly, but instead of resetting the staging area, the changes are kept intact and all that is needed is to commit the changes. This could be useful whenever some additions need to be made, such as files or features, without resetting the changes that _were staged_ for commit on commit(B) to create commit(C). More simply, this sets commit(B) with changes _after_ the staging state.

`git reset --hard HEAD^` works more destructively and instead overwrites the working directory to the initial state of commit(B). For example, any changes that were prepared to be staged and committed to create commit(C) would completely be overwritten and possibly destroyed. Only use this whenever a commit needs to be completely destroyed and start cleanly from commit(B). More simply, this sets commit(B) to its inital state with no changes _before_ or _after_ the staging state.

#### Scenario

Let's assume we commited commit(C) with some changes to an html file along with adding a javascript file in the `Master Branch`. These changes needed to be in a dedicated branch called `Feature-Branch`. For this scenario, we can use `git reset HEAD^` so that we can stash the changes and branch off from commit(B) to the `Feature-Branch`.

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
Finally, unload the changes into this new branch that we stashed before using `git stash pop`
Stage the changes with `git add .`
Commit the changes in the new branch `git commit -m "commit(C)"`

```
Master Branch
A---B
     \
      C
      Feature Branch
```

## Git commit --amend

`git commit --amend` is a command in git that allows us to rewrite the last commit. It's important to note that when using this command, git completely replaces the last commit with a new commit, which includes the changes that were amended.

#### Scenario

Lets assume we are almost ready to merge our feature-branch with our master branch. We have committed the final change labeled as commit: 'G' in feature-branch. But wait a minute? We forgot to add a file and we have a typo within our file.

```
      Master Branch
A---B---C---F-------
         \
          D---E---G (this commit is missing a file we forgot to add)
          Feature Branch
```

I created an index.html file which has a newly added paragraph saying "hello wolrd". This is a typo that we didnt catch when adding it to our last commit(G).

#### When can we use this command?

We can use this command whenever we want to fix a mistake in the last commit or add a file that we forgot to add to the staging area. The caveat to this is that this command should only be used when other collaborators are not working on the same branch where these commit changes lie in.
