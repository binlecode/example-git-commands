# example-git-common-commands

There are many cases that you want to rename a local and remote branch.
Here are the simple steps to carry out in command line.

### Important preparation

Before anything else, make sure your local target branch is synchronized with remote (origin):
```bash
git branch -a
```

The `-a` is used to list all branches, both local and remote.
To remove local list of branches for remote sync, use `--prune` option with `fetch` command.

```bash
git fetch --prune
```

If there are pending changes, commit and push to remote:
```bash
git commit -m
git push origin <branch-name>
```

If your local branch is behind remote:
```bash
git pull origin <branch-name>
```

Merge all changes and commit in the target branch.

### Optional: backup the branch to be renamed
```bash
git checkout <branch-name>
git branch <branch-backup-name>
```

### Rename your local branch

If you are on the new branch (```<new-branch-name>```) you want to rename:
```bash
git branch -m <new-branch-name> <branch-name>
```

If you are on a different branch other than ```<new-branch-name>``` and ```<branch-name>```:
```bash
git branch -m <old-branch-name> <branch-name>
```

### Sync the change to remote (origin)

#### Option 1 Delete the old remote branch and push the new local branch.

```bash
git push origin :<old-branch-name> <new-branch-name>
```

There are basically two steps involved in this push:
- delete branch by adding `:` to the old branch name
- add back branch with old branch name, but with content of new branch

You may receive an error message saying:
"remote: error: By default, deleting the current branch is denied, because... ".

That's because: Every repository has a 'current' branch. It is the one that will be checked out by default when the repository is cloned.

The 'current' branch is the special HEAD reference. HEAD must point to something that is expected to always exist.

In this case, you need to go to github website on the far right side in the navigation menu choose 'Settings', in the Settings Tab choose 'Default Branch', and change default branch to one different from the ```<old-branch-name>```.


#### Option 2, replace remote branch content with new branch

Without deletion, the content of old branch can be replaced by new branch:
```bash
git push origin <branch-name>
```
