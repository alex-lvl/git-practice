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

`git reset --hard HEAD^` works more destructively and instead overwrites the working directory to the initial state of commit(B). For example, any changes that were prepared to be staged and committed to create commit(C) would completely be overwritten and possibly destroyed. Only use this whenever a commit needs to be completely destroyed and start cleanly from commit(B). Also, use this when not working with collaborators. More simply, this sets commit(B) to its inital state with no changes _before_ or _after_ the staging state.

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

This is the tree now:

```
    Master Branch
A---B
     \
      C---D
      Feature Branch
```

#### When can we use this command?

We can use this command whenever we want to fix a mistake in the last commit or add a file that we forgot to add to the staging area. The caveat to this is that this command should only be used when other collaborators are not working on the same branch where these commit changes lie in.

# Git rebase -i

`git rebase -i` is a command which allows us to interactively stop after each commit we’re trying to modify, and then make whatever changes we wish. It can be a powerful tool to use, but can be misused, so be careful. Ensure that collaborators are aware of any attempts at rebasing commits in a shared repository.

Make three new commits in `Feature-Branch`
Each commit should have a message, commit(E), commit(F), commit(G) and changes with `console.log("new feature progress 1/3")` `console.log("new feature progress 2/3")` `console.log("new feature progress 3/3")`, respectively.

This is the git tree now:

```
    Master Branch
A---B
     \
      C---D---E---F---G
      Feature Branch
```

#### git rebase -i HEAD~3

Rebase allows us to change history ranging from the latest commit to a distant commit. `git rebase -i HEAD~3` allows us to edit the last 3 commits in our branch. This command allows us to perform multiple actions from pick, edit, remove, and more. For this case, we want to use squash to join commits E, F, and G to make our git tree a little cleaner with rebase. Here are a few more ways rebase can change history:

- Change our most recent commit
- Change multiple commit messages
- Reorder commits
- Squash commits together
- Split up commits

Let's try rebase now:

Run `git rebase -i HEAD~3`
An interactive window should appear with the editor of your choice. You will see the last three commits and a cheatsheet below this list showing the multiple actions you can perform to each commit. The list of commits should look like this:

`pick commit(E)`
`pick commit(F)`
`pick commit(G)`

For our purpose, we are using squash to join commits F and G to commit E. Change commit F and G from 'pick' to 'squash'. Like so:

`pick commit(E)`
`squash commit(F)`
`squash commit(G)`
Note: We could reorder the commits here if chosen to.

Save and exit the window.
A new window will appear asking to change the commit message. We can keep the commit message as is, which is 'commit(E)'

The git tree should now look like this:

```
    Master Branch
A---B
     \
      C---D---E(commits E, F, G joined together)
      Feature Branch
```

We are now ready to merge our changes to `Master`.

# git merge <branch>

`git merge <target branch>` should be run _from_ the `Master Branch` if attempting to merge changes to the main line branch. It is also possible to merge the `Master Branch` _from_ the target branch, in our case, `Feature-Branch`. We will learn more about this type of merge later.

It's important to note that using `git merge` is used to progress our tree _forward in time_. Or in other words, flow in a one directional fashion, but this does not have to be the case sometimes. This non destructive behavior is a benefit of using `git merge` because it is not used to change history.

### 3-way-merge

Merging our `Feature-Branch` to `Master` allows us to implement our changes to the main line branch. If this is a shared repository, the `Master` branch could have new commits from collaborators since branching off of commit B. For example:

```
    Master Branch
A---B---F(collaborator's commit)
     \
      C---D---E
      Feature Branch
```

This type of merge is called a _3-Way-Merge_. In some cases you might get a merge conflict that must be resolved before merging with a 3-Way-Merge. Once conflicts are resolved, then the git tree will appear as so:

```
    Master Branch
A---B(3)---F(1)-M(Merge commit)
     \         /
      C---D---E(2)
      Feature Branch
```

As we discussed earlier, `git merge` moves our `Master Branch` forward in time, in one direction. But, what if we merged _from_ the `Feature-Branch`? This is absolutely possible, but our tree will no longer flow in one direction but rather, two directions. Here's an example:

```
    Master Branch
A---B---F--------
     \           \
      C---D---E---M(Merge commit)
      Feature Branch
```

What we see here is still a 3-Way-Merge, but instead of our merge commit(M) being placed on the `Master Branch`, the merge commit and HEAD pointer are placed on our `Feature-Branch`. This is a useful method to use if we want to maintain and continue our work on `Feature-Branch` with the latest changes on `Master Branch`.

It is important to note that any new commits pushed to the `Master Branch` will create a new merge commit on the `Feature-Branch`. Or, run `git merge master` from `Feature-Branch` to bring in the latest changes on `Master` This is the two directional flow we discussed earlier. For example:

```
M = A merge commit created after running git merge

    Master Branch
A---B---F------- -------H(Collaborator Commit)
     \           \       \
      C---D---E---M---G---M(New Merge Commit)
      Feature Branch
```

This workflow should continue until we merge the `Feature-Branch` to `Master`.

```
    Master Branch
A---B---F------- -------H---I------M(Merge commit)
     \           \       \   \    / git merge Feature-Branch
      C---D---E---M---G---M---M---
      Feature Branch      |___|___ New merge commits
```

As seen in the tree above, this can become very messy. However, there can be a better workflow solution that may be a better approach, which we will discuss later.

### Fast-Forward Merge

A fast forward merge occurs when a `Master Branch` has no changes while new changes, or commits, are made on another branch. In essence, the other branch, in our case `Feature-Branch`, is "absorbed" into the `Master Branch` without creating a new merge commit.

Let's see an example. Before, a collaborator pushed a change to `Master`, which became commit F. But, now assume a collaborator did not push any new changes in to the `Master Branch`. In this case, commit F did not exist on `Master`. Let's see how that would look:

```
    Master Branch
A---B
     \
      C---D---E
      Feature Branch
```

If work on the `Feature-Branch` has been completed and ready to merge in to `Master`, we can call `git merge Feature-Branch` _from_ the `Master Branch` to initiate a Fast-Forward Merge. This will skip creating a merge commit and move the HEAD pointer to the latest commit on the `Feature-Branch`. Now look at the git tree:

```
Master Branch
A---B---C---D---E(HEAD)
```

Notice, there is no merge commit. The `Feature-Branch` is absorbed into the `Master Branch`. However, we can avoid a fast forward merge by running `git merge Feature-Branch --no-ff`. This is a no fast forward command that can create a merge commit and keep the `Feature-Branch` alive. Using no fast forward will make the tree look like so:

```
    Master Branch
A---B-----------M(Merge commit)
     \         /
      C---D---E
      Feature Branch
```

### Merge and Rebase Workflow

We've covered both rebase and merge. Now, we are going to learn a technique that can enhance developer workflow using rebase and merge together.

Before we dive in, let us discuss a feature of commits. Commits in git are meant to be _inmutable_, or in other words, they can not be changed once they are created.

With this in mind, it is important to recall that rebase can be used to rewrite history. Whenever we are using rebase to "change" a commit, or move a branch to the latest commit on the `Master Branch`, rebase is not changing the commits. Rebase takes _copies_ of the chosen commits to be rebased and reapplies those commits to the branch as _new_ commits. You can spot this by noticing that the commit hashes change when checking the log with `git log`.

Therefore, it is important to understand that there are risks involved when applying this workflow with other collaborators. If you are sharing a branch with other collaborators, ensure that they are aware before using the `rebase` command. Overall, it is best to avoid using this workflow when working on shared branches.

At last, let's practice using this workflow. Recall we merged the `Feature-Branch` to `Master`. Delete the `Feature-Branch` with `git branch -d Feature-Branch`. Let's begin on a fresh branch, but before that, have a look at the git tree so far.

```
M = Merge commits

    Master Branch
A---B---F------- -------H---I------M (we are starting from here)
     \           \       \   \    / git merge Feature-Branch
      C---D---E---M---G---M---M---
      Feature Branch
```

We will begin from the last merge commit on `Master`. Now create a new branch called `workflow-branch` using `git branch workflow-branch`.

Move into the new branch by running `git checkout workflow-branch`

Add a change to the `index.html` with this code:

```
  <p>hello world</p>
  <p>from commit F</p>
  <p>from commit H</p>
  <p>from commit I</p>
  <p>workflow-branch commit j</p>
```

Commit the changes with this message "commit j"

Have a look at the git tree now:

```
M = Merge commits

    Master Branch
---M
    \
     J
     workflow-branch
```

Now lets assume a collaborator made a change that was pushed to `Master` lets mock this change. Go back to `Master` with `git checkout Master`. Now apply this change to the `index.html` file with the code below and commit the change with `git commit -m "commit K"`

```
  <p>hello world</p>
  <p>from commit F</p>
  <p>from commit H</p>
  <p>from commit I</p>
  <p>collaborator change Master commit K</p>
```

Visual reprentation of the tree now:

```
M = Merge commits

    Master Branch
---M---K
    \
     J
     workflow-branch
```

Assume we continue to work on the `workflow-branch`. Run `git checkout workflow-branch`. Now apply another change to the `index.html` file with the code below and commit it by running `git commit -m commit L`:

```
  <p>hello world</p>
  <p>from commit F</p>
  <p>from commit H</p>
  <p>from commit I</p>
  <p>workflow-branch commit J</p>
  <p>workflow-branch commit L</p>
```
