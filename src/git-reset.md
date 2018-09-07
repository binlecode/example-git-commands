# git-reset


## Git basic concepts to understand reset

#### Git states
 Git's three internal state management mechanism's, The Commit Tree (HEAD), The Staging Index, and The Working Directory. Sometimes these mechanisms are called Git's "three trees".


#### The HEAD
HEAD is the pointer to the current branch reference, which is in turn a pointer to the last commit made on that branch. That means HEAD will be the parent of the next commit that is created. It’s generally simplest to think of HEAD as the snapshot of your last commit on that branch.

#### The Index
The Index is your proposed next commit. This is commonly viewed as Git’s “Staging Area” as this is what's included in next commit.

Git populates this index with a list of all the file contents that were last checked out into your working branch and what they looked like when they were originally checked out. You then replace some of those files with new versions of them, and ```git commit``` converts that into the tree for a new commit.

One simple way to view git index is run:
```bash
git ls-files -s
```
which shows workspace files with staging (-s, aka --staging) metadata.


## Git reset

The first thing reset will do is move what HEAD points to, it moves the current working branch to a commit point, but **without** changing the git Index or Working Directory.

Not like ```git chckout```, with which the branch ref is not changed and only the HEAD ref has been moved to the target commit (the branch is now in a 'detached HEAD' state), ```git reset``` moves both HEAD and branch ref to the target commit, thus changes all three git trees.

The command line arguments ```--soft```, ```--mixed```, and ```--hard``` define how to modify the Staging Index, and Working Directory trees.

#### default --mixed HEAD
```git reset```: the default invocation of git reset has implicit arguments of --mixed and HEAD, same as ```git reset --mixed HEAD```. In this form HEAD is the specified commit.

for example ```git status``` returns the following:
```
On branch master
Changes to be committed:
  (use "git reset HEAD ..." to unstage)
    new file: new_file
    modified: reset_lifecycle_file
```
We have added a ```new_file``` and modified the contents of ```reset_lifecycle_file```. These changes are then applied to the Staging Index.

After running ```git reset --mixed```, the output is:
```
Unstaged changes after reset:
M	reset_lifecycle_file
```
And running ```git status```, the output is:
```
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)
	modified:   reset_lifecycle_file
Untracked files:
  (use "git add <file>..." to include in what will be committed)
	new_file
```
This shows that the Staging Index has been reset to a HEAD commit state where ```reset_lifecycle_file``` is the only file in the index, and it's staged content has been reset to the version of HEAD. 

Note that after mixed reset, there are modifications to ```reset_lifecycle_file``` and there is an untracked file ```new_file``` remaining in Working Directory.


#### --hard
This is the most direct, **DANGEROUS**, and most used option. When passed ```--hard``` the **Commit History ref** pointers are updated to the specified commit. Then, the Staging Index and Working Directory are reset to match that of the specified commit.

for example, a branch ```git status``` returns following:

```
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
	new file:   new_file
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)
	modified:   reset_lifecycle_file
```
We can see that there are now pending changes. The Staging Index tree has a pending change for the addition of ```new_file``` and the Working Directory has a pending change for the modifications to ```reset_lifecycle_file```.

After runnign ```git reset --hard```, with ```git status```, git indicates there are no pending changes. **This data loss cannot be undone.**

#### --soft

A git soft reset only changes commit history. Neither the Index or Working Directory is touched by a soft reset.

for example, by running ```git log```, commit history is shown 
```
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

We would like to reset to first commit ```3c0763b1e...``` by running
```bash
git reset --soft 3c0763b1e
```
Here is the interesting output. Running ```git status``` shows:
```
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
	modified:   reset_lifecycle_file
Untracked files:
  (use "git add <file>..." to include in what will be committed)
	new_file
```

While ```git log``` now shows:
```
commit 3c0763b1e3b4a96e89bc934dff502b0277c129e3 (HEAD -> master)
Author: ...
Date:   ...
    initial commit
```

The log output now shows that there is a single commit in the Commit History. This helps to clearly illustrate what ```--soft``` has done. As with all git reset invocations, the first action git reset takes is to reset the commit tree. Our previous examples with ```--hard``` and ```--mixed``` continous beyond this step. ```--mixed``` resets the HEAD ref, while ```--hard``` continous further and resets the Working Directory.



