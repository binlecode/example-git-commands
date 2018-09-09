# git-rebase

Two common practice of rewritting commit history is commit ```--amend``` and ```rebase```. For ```--amend``` see [git-commit-amend](./git-commit-amend.md).

In general, rebase is the process of moving or combining a sequence of commits to a new base commit. By its name, it changes the base of current branch to another, re-apply commits by creating new commits. Therefore, the current branch remains, but its commit history is different.

The primary use case of rebase is: 
The master branch has progressed since you started working on a feature branch. You want to get the latest updates to the master branch into your branch, but you want to keep your branch's history clean so it appears as if you've been working off the latest master branch.

You have two options for integrating your feature into a public(team) branch: merging directly or rebasing and then merging. The former option results in a 3-way merge and a merge commit, while the latter results in a fast-forward merge and a linear history. 

## rebase case study

With a repo with ```master``` branch
```bash
$ git branch
* master
$ git branch feature1
$ git branch
  feature1
* master
$ git checkout featur1
$ echo 'new change from feature1 branch' >> new_file
$ git add new_file
$ git commit -m 'commit new change to new_file in feature1'
# verify commit history
$ git log
commit 3709d3a60aa006492b3c5581745d871598e14f13 (HEAD -> feature1)
Author: Bin Le <binle@Bins-Mbp13d.local>
Date:   Fri Sep 7 18:50:42 2018 -0400
    changed new_file
commit e3a4ac22731ca0414dc82f421c866c24da393141 (master)
Author: Bin Le <binle@Bins-Mbp13d.local>
Date:   Fri Sep 7 11:18:35 2018 -0400
    revise reset_lifecycle_file
commit 3c0763b1e3b4a96e89bc934dff502b0277c129e3
Author: Bin Le <binle@Bins-Mbp13d.local>
Date:   Thu Sep 6 21:02:43 2018 -0400
    initial commit
# now switch back to master branch and introduce a change
$ git checkout master
# verify commit history on master branch
$ git log
commit e3a4ac22731ca0414dc82f421c866c24da393141 (HEAD -> master)
Author: Bin Le <binle@Bins-Mbp13d.local>
Date:   Fri Sep 7 11:18:35 2018 -0400
    revise reset_lifecycle_file
commit 3c0763b1e3b4a96e89bc934dff502b0277c129e3
Author: Bin Le <binle@Bins-Mbp13d.local>
Date:   Thu Sep 6 21:02:43 2018 -0400
    initial commit
# let's introduce some change to master branch
$ echo 'new change to reset_lifecycle_file in master branch' >> reset_lifecycle_file
$ git add reset_lifecycle_file
$ git commit -m 'add new change to reset_lifecycle_file'
$ git log
commit 1a662178b65e58d78fe842a2a8b145336ef45077 (HEAD -> master)
Author: Bin Le <binle@Bins-Mbp13d.local>
Date:   Fri Sep 7 18:51:49 2018 -0400
    change on reset_lifecycle_file
commit e3a4ac22731ca0414dc82f421c866c24da393141
Author: Bin Le <binle@Bins-Mbp13d.local>
Date:   Fri Sep 7 11:18:35 2018 -0400
    revise reset_lifecycle_file
commit 3c0763b1e3b4a96e89bc934dff502b0277c129e3
Author: Bin Le <binle@Bins-Mbp13d.local>
Date:   Thu Sep 6 21:02:43 2018 -0400
    initial commit
```

At this point you can see the forked HEADs between ```master``` and ```feature1``` branch:

```
master:     o(commit e3a4ac2...) -> o(commit 1a66217...)
            \
feature1:    o(commit e3a4ac2...) -> o(commit 3709d3a6...)
```
```bash
$ ls -l .git/refs/heads/
feature1	
master
# since we are currently at fature1 branch
$ cat .git/HEAD
ref: refs/heads/feature1
```

This means git ```HEAD``` is pointing to ref head of ```feature1``` branch.
Now let ```feature1``` branch rebase on ```master``` branch:

```bash
$ git rebase master
First, rewinding head to replay your work on top of it...
Applying: changed new_file
$ git log
commit 6666fbc94781f3045735461229339bfd7199df7e (HEAD -> feature1)
Author: Bin Le <binle@Bins-Mbp13d.local>
Date:   Fri Sep 7 18:50:42 2018 -0400
    changed new_file
commit 1a662178b65e58d78fe842a2a8b145336ef45077 (master)
Author: Bin Le <binle@Bins-Mbp13d.local>
Date:   Fri Sep 7 18:51:49 2018 -0400
    change on reset_lifecycle_file
commit e3a4ac22731ca0414dc82f421c866c24da393141
Author: Bin Le <binle@Bins-Mbp13d.local>
Date:   Fri Sep 7 11:18:35 2018 -0400
    revise reset_lifecycle_file
commit 3c0763b1e3b4a96e89bc934dff502b0277c129e3
Author: Bin Le <binle@Bins-Mbp13d.local>

:...skipping...
commit 6666fbc94781f3045735461229339bfd7199df7e (HEAD -> feature1)
Author: Bin Le <binle@Bins-Mbp13d.local>
Date:   Fri Sep 7 18:50:42 2018 -0400
    changed new_file
commit 1a662178b65e58d78fe842a2a8b145336ef45077 (master)
Author: Bin Le <binle@Bins-Mbp13d.local>
Date:   Fri Sep 7 18:51:49 2018 -0400
    change on reset_lifecycle_file
commit e3a4ac22731ca0414dc82f421c866c24da393141
Author: Bin Le <binle@Bins-Mbp13d.local>
Date:   Fri Sep 7 11:18:35 2018 -0400
    revise reset_lifecycle_file
commit 3c0763b1e3b4a96e89bc934dff502b0277c129e3
Author: Bin Le <binle@Bins-Mbp13d.local>
Date:   Thu Sep 6 21:02:43 2018 -0400
```

Now the ```feature1``` branch commit history tree is linearly (no more fork) based on commit ```1a66217...``` from ```master``` branch:

```
feature1: o(commit e3a4ac2... from master) -> o(commit 1a66217... from master) -> o(new commit 6666fbc94...)
```

Also note the ```...skipping...``` section from the terminal return, that whole stack of original commit history is skipped from ```feature1``` branch.

Rebase itself has 2 main modes: "manual" and "interactive" mode. The```--interactive``` (```-i``` for short) mode will open an editor with a list of the commits which are about to be changed. This list accepts commands, allowing the user to edit the list before initiating the rebase action.

Following the case above let's create a ```feature2``` branch from master ```HEAD~2``` (two commits before ```HEAD```) commit:
```bash
$ git checkout master
# create a new branch from two commits below HEAD
$ git checkout -b feature3 HEAD~2
Switched to a new branch 'feature3'
$ echo 'add line to reset_lifecycle_file' >> reset_lifecycle_file
$ git add .
$ git commit -m 'add line to reset_lifecycle_file'
```

At this point the relation between feature3 and master is like this:
```
o -> o -> o master
\
 o -> o feature3
```
After rebase, feature3 should look like
```
o -> o -> o master
           \
            o -> o feature3
```

To rebase with interactive mode, run:
```
$ git rebase -i master
```

Then vi editor is invoked to show the following rebase script for customization:
```
pick 9afa0a4 added change from feature3
s 9b3a72b add line to reset_lifecycle_file

# Rebase aa24883..9b3a72b onto aa24883 (2 commands)
#
# Commands:
# ...
#
```
The two pick commands can be changed to options such ```r```, ```s```, etc, as explained in the command list.
Here the first pick is changed to ```s``` (squash). Once vi editing is done the script is run. 

Conflict could occur from the content merging during rebase commit replay. Such conflict will be left for manual resolving. When conflict is resolved, rebase can continue by running:
```bash
git rebase --continue
```


Git rebase itself is not seriously dangerous. The real danger arise when executing history rewriting interactive rebases and force pushing the results to a remote branch that's shared by other users. This is a pattern that should be avoided, otherwise it will appear to others that the project history disappears.
