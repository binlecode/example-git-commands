# git-merge-and-cherry-pick

Git merge will combine multiple sequences of commits into one unified history. In the most frequent use cases, git merge is used to combine two branches.

## fast-forward vs three-way merge

There are two common cases of merge: fast-forward and 3-way. Git will determine the merge algorithm automatically depending on feature and receiving (such as `master`) branch.

- A fast-forward merge can occur when there is a linear path from the current branch tip to the target branch. Instead of “actually” merging the branches, all Git has to do to integrate the histories is move (i.e., “fast forward”) the current branch tip up to the target branch tip.
- If the branches have diverged, there is not a linear path to the target branch, Git has no choice but to combine them via a 3-way merge. 3-way merges use a dedicated commit to tie together the two histories. The nomenclature comes from the fact that Git uses three commits to generate the merge commit: the two branch tips and their common ancestor. Merge conflict happens when the two branches to merge both changed the same part of the same file, Git won't be able to figure out which version to use. And Git stops right before the merge commit and asks usre to resolve the conflicts manually.

A typical conflict is presented as below:

```bash
here is some content not affected by the conflict
<<<<<<< master
this is conflicted text from master
=======
this is conflicted text from feature branch
```

These visual markers are: `<<<<<<<`, `=======`, and `>>>>>>>`. Generally the content before the ======= marker is the receiving branch and the part after is the merging branch.

Once you've identified conflicting sections, you can go in edit the file(s) and fix up the merge to your liking. When you're ready to finish the merge, all you have to do is run `git add` on the conflicted file(s) to tell Git they're resolved. Then, you run a normal `git commit` to generate the merge commit.

## merge a specifc commit

You were working on a certain branch of a git repository, and you committed some changes to it. Then you realize, a particular commit should also go to another branch of the repository buy not the entire change history. Git `cherry-pick` command is for this need.

This is the git command sequence:

```bash
# go to receiving branch (for example, master)
git checkout master
git cherry-pick <commit-hash-from-feature-branch>
```

## reference

[git-cherry-pick](https://git-scm.com/docs/git-cherry-pick)
