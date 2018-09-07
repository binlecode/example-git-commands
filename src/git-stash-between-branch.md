# git-stash-between-branch


## why stashing
Things are easily in a messy state and you want to switch branches for a bit to work on something else but don’t want to do a commit for half-done work just so you can get back to this point later. Such work-in-progress (wip) changes are for stash, not commit.

Stashing takes the dirty state of your wip content and saves it so that you can reapply at any time to any branch. The power of stash is partly because the stashed changes are free from any branch.

## how to stash

Assuem you have some uncommited changes by:
```bash
git status
```
And you want to switch branches, before you switch branch:
```bash
git stash
```
You will see confirmation info like:
Saved working directory and index state "WIP on master: 049d078 added the index file ..."
Now you recheck current status you will find the previously existing uncommited changes are gone (nothing to commit):
```bash
git status
```

There is a ```save``` option that's useful to label a 'name' to a stash:
```bash
git stash save “Your stash message”
```

With ```save``` option, another useful option, almost always needed in a messy wip case, is to include untracked files:
```bash
git stash save --include-untracked
```
In my experience, this is likely the most used option as I don't tend to track temporary wip files.

## how to apply stash

At this point, you can easily switch branches and do work elsewhere; your changes are stored on your stack. To see which stashes you’ve stored, you can use git stash list:
```bash
git stash list
```
You can keep stashing changes as many times as you want to 'stage' your wip contents. such as:
```bash
stash@{0}: WIP on master: "Your stash message"
stash@{1}: WIP on develop: c264051 Revert ...
```

As shown above, two stashes were done before. And the most recent stash made is in the top. You can apply the **most-recent** one to your current branch by:
```bash
git stash apply
```

More often you want to apply stashes selectively. You can stash one by its name, such as:
```bash
git stash apply stash@{1}
```

You can also have uncommitted changes when you apply a stash. Git will ask you to merge conflicts if anything no longer applies cleanly.

The stash ```apply``` option won't remove the stash from the stash list, so you can re-apply it anytime. To apply the stash and remove it from the stash list at the same, the ```pop``` option is used instead.
```bash
git stash pop stash@{0}
```

To get more information from your stash list, such as diff details between stash and current content:
```bash
git stash show stash@{1}
```
Or to get a full diff details for all stashes:
```bash
git stash show -p
```

Another common use case is to apply stash to a particular branch:
```bash
git stash branch <branch-name> stash@{1}
```

When the stashed changes are applied, by default the previously staged changes before stashing is not restaged, aka get overritten by the stash. To preserve staged changes, ```git stash apply``` can run with a ```--index``` option to tell git to try to re-apply the previously staged changes after stashing.
```bash
git stash apply --index
```
Or pop a stash with staged (indexed) changes re-staged:
```bash
git stash pop --index
```

Instead of pop a stashed change, to simply remove it, you can run git stash drop with the name of the stash to remove:
```bash
git stash drop stash@{0}
```

