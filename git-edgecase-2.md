# Git edgecase #2

This edgecase involves an issue when rebasing while stashing changes. Before we begin, here is the git tree currently:

```
    Master Branch
A---B
     \
      C---D---E(commits E, F, G joined together)
      Feature Branch
```

We have concluded rebasing and squashing commits E, F, and G together. However, there may be an issue if you choose to stash your changes before rebasing.

For example, assume we added another feature related to commit E. We commit this feature, which will be commit F. However, we realize this commit is related to commit E and we want to make the tree a little cleaner by squashing commit F in to commit E.

### New edgecase scenario

```
M = Merge commits

    Master Branch
---M---K
    \
     J---L
     workflow-branch
```

When stashing before rebasing the workflow-branch to the master branch, the stash has an issue when popped after the rebase.

Merge conflicts occur that need to resolved, this creates two new commits, J and L, which then corrupts the stashed changes that were in commit L?
