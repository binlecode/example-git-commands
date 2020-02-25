

Recovering a deleted file from the git repository, after a commit, is not complicated if you know the right commands and method for doing so. The process takes 3 steps:
- Identify deleted files
- Find commit information in repo about the deleted files
- Restore the file


List all files that have been deleted from a git repository:
```bash
git log --diff-filter=D --summary | grep delete
```
If you know the exact filename of the deleted file you can skip this step.

List information about a particular filename that has been deleted from a git repository:
```bash
git log -- [deleted-filename]
```

Restore a file that has been deleted from a git repository. You need to find the last (or a specific) commit (before the file was deleted):
```bash
git rev-list -n 1 HEAD -- [deleted-filename]
```
Now that you have the last commit, check out the repository at the commit BEFORE the file was deleted:
```bash
git checkout <commit-number> -- [deleted-filename]
```

That is it. Your deleted file has been recovered as a new file to the current repo version.