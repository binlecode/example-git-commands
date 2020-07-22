# git-reset

There are cases when a changed is commited, yet not pushed, but later becomes undesired.

In such cases, there are two common approaches:
- manually revert the change on top of the existing commit, and and commit again
(this is simpler to think but usually not preferred as it adds two commits, the latter is just to cancel the former)
- use `git reset`

The first thing reset will do is move what HEAD points to, it moves the current working branch to a commit point, but **without** changing the git Index or Working Directory.

Not like `git chckout`, with which the branch ref is not changed and only the HEAD ref has been moved to the target commit (HEAD if not specified), `git reset` moves both HEAD and branch ref to the target commit, thus changes all three git trees (workspace, staging index, and commit tree HEAD).

The command line arguments `--soft`, `--mixed`, and `--hard` define how to modify the Staging Index, and Working Directory trees.

#### default --mixed HEAD
`git reset`: the default invocation of git reset has implicit arguments of --mixed and HEAD, same as `git reset --mixed HEAD`. In this form HEAD is the specified commit.

for example `git status` returns the following:
```bash
On branch master
Changes to be committed:
  (use "git reset HEAD ..." to unstage)
    new file: new_file
    modified: reset_lifecycle_file
```

We have added a `new_file` and modified the contents of `reset_lifecycle_file`. These changes are then applied to the Staging Index.

After running `git reset --mixed`, the output is:
```bash
Unstaged changes after reset:
M	reset_lifecycle_file
```

And running `git status`, the output is:
```bash
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)
	modified:   reset_lifecycle_file
Untracked files:
  (use "git add <file>..." to include in what will be committed)
	new_file
```

This shows that the Staging Index has been reset to a HEAD commit state where `reset_lifecycle_file` is the only file in the index, and it's staged content has been reset to the version of HEAD. 

Note that after mixed reset, there are modifications to `reset_lifecycle_file` and there is an untracked file `new_file` remaining in Working Directory.


#### --hard
This is the most direct, **DANGEROUS**, and most used option. When passed `--hard` the **Commit History ref** pointers are updated to the specified commit. Then, the Staging Index and Working Directory are reset to match that of the specified commit.

for example, a branch `git status` returns following:

```bash
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
	new file:   new_file
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)
	modified:   reset_lifecycle_file
```

We can see that there are now pending changes. The Staging Index tree has a pending change for the addition of `new_file` and the Working Directory has a pending change for the modifications to `reset_lifecycle_file`.

After runnign `git reset --hard`, with `git status`, git indicates there are no pending changes. **This data loss cannot be undone.**

#### --soft

A git soft reset only changes commit history. Neither the Index or Working Directory is touched by a soft reset.

for example, by running `git log`, commit history is shown 
```bash
commit 17df60e096204d40066a44d438f1d7f55d73ca5b (HEAD -> master)
Author: ...
Date:   ...
    another change to reset_lifecycle_file
commit ef08b694cc229e08283465f2965a813d081146f4
Author: ...
Date:   ...
    revise reset_lifecycle_file
commit 3c0763b1e3b4a96e89bc934dff502b0277c129e3
Author: ...
Date:   ...
    initial commit
```

We would like to reset to first commit `3c0763b1e...` by running
```bash
git reset --soft 3c0763b1e
```

Here is the interesting output. Running `git status` shows:
```bash
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
	modified:   reset_lifecycle_file
Untracked files:
  (use "git add <file>..." to include in what will be committed)
	new_file
```

While `git log` now shows:
```bash
commit 3c0763b1e3b4a96e89bc934dff502b0277c129e3 (HEAD -> master)
Author: ...
Date:   ...
    initial commit
```

The log output now shows that there is a single commit in the Commit History. This helps to illustrate what `--soft` has done. As with all git reset invocations, the first action, by `--soft`, that git reset takes is to reset the commit tree. Our previous examples with `--hard` and `--mixed` continous beyond this step. `--mixed` resets the HEAD ref, while `--hard` continous further and resets the Working Directory.


## reset between branches

Basically it is the same as using reset within the branch, with only the target commit being another branch
```bash
git checkout test-branch
git reset --hard master
```
The hard reset above is reseting the `test-branch` to the HEAD commit of `master branch`.
All the rules abut hard, mixed, or soft options are the same.
