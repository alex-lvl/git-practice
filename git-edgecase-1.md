# Git edgecase #1

In the next section we'll discuss using `git commit --amend`. But before we do, there is an edgecase I would like to discuss.

This is the git tree so far:

```
Master Branch
A---B
     \
      C
      Feature Branch
```

Now, lets assume that commit (B) has a description named "init html", but we want to rename it to "init html file (commit B)" to better reflect the git tree in the git log. We could `git checkout master` and then run `git commit --amend` to change the commit message, but there's a problem with this scenario.

Let's recall that `git commit --amend` is a command that can rewrite history, which can be destructive to the git tree. If we called this command on the master branch to amend commit (B), the changes and commits made on the feature-branch would be completely overwritten and the data could potentially be removed from history or `Master Branch`.

Why? Because `git commit --amend` is a command that will overwrite the last commit with a _new_ commit.

The new git tree would look like this if we called `git commit --amend` on the `Master Branch`

```
Master Branch
A---B(modified)
    Commit here is completely overwritten with a new commit. The feature branch would be overwritten
```

When moving the pointer to the HEAD of the `Feature-Branch`, this is what we see. The old commit (B) before modifying with `git commit --amend` is still present.

```
Master Branch
A---B(before modification)
     \
      C
      Feature Branch
```

To fix this, we can `git rebase master` while in the `Feature-Branch`. The `Feature-Branch` should now be reapplied to the amended `Master Branch`. In other words, commit(C) is updated with a new pointer to commit (B).

```
Master Branch
A---B(modified)
     \
      C
      Feature Branch
```

### My confusion

Now, this might be a solution, but I am very confused on how the `Feature-Branch` didnt pull the modified commit (B) and, instead, kept the old commit (B). For example, did amending commit (B) on `Master Branch` completely create a new instance without the `Feature-Branch` after amending? Did `Feature-Branch` still exist on the newly amended `Master Branch`? Are there two new instances of _trees_ until rebasing occurs? I understand during rebasing, the commits in the `Feature-Branch` are copied and then applied as new commits to the `Master Branch`. This applies commits on `Feature-Branch` on top of the latest commits from the amended `Master Branch`. This seems like a rabbit hole.

### My Assumption

Branches are pointers. With this in mind, we can assume that the `Master Branch` creates a new commit for commit (B) when amended, and therefore, this would create a new pointer to commit (B), which could be the `HEAD` pointer?(Maybe ahead of commit(B)?). So, this all occurs on the `Master Branch` NOT the `Feature-Branch`.

### A solution to avoid this fatal mistake edge case

We can use `git rebase -i HEAD~2` which opens an interative rebase using an editor. When this window opens, search for commit (B) and change "pick" to "reword". Save and close the editor.

A new window will open for you to edit the commit message from commit (B). Save and close the editor to apply the changes.
