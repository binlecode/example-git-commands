# git-sync-forked

As a github user, we sometimes need to sync the forked repo up to the new changes in the original source repo. That source repo, in git term, is called ```upstream```.

Before you can sync your fork with an upstream repo, you need to configure a git ```remote``` that points to the upstream repository.

#### checkout your forked repo
```bash
git clone <your-forked-repo-url>
```

#### define upstream repository
First you will check the current defined remotes:
```bash
git remote -v
origin  https://github.com/<YOUR_USERNAME>/<YOUR_FORK>.git (fetch)
origin  https://github.com/<YOUR_USERNAME>/<YOUR_FORK>.git (push)
```
Note the protocol can be either ```https``` or ```ssh``` depending on your account security settings.
The list returned above means there's no other remotes than the forked repo itself.

Let's define new remote entry for the upstream repository:
```bash
git remote add upstream https://github.com/<ORIGINAL_OWNER>/<ORIGINAL_REPOSITORY>.git
```

Now if check defined remotes again:
```bash
git remote -v
origin  https://github.com/<YOUR_USERNAME>/<YOUR_FORK>.git (fetch)
origin  https://github.com/<YOUR_USERNAME>/<YOUR_FORK>.git (push)
upstream	https://github.com/<ORIGINAL_OWNER>/<ORIGINAL_REPOSITORY>.git (fetch)
upstream	https://github.com/<ORIGINAL_OWNER>/<ORIGINAL_REPOSITORY>.git (push)
```

#### sync with upstream

Now we have upstream defined we can pull changes from upstream:
```bash
git fetch upstream
```
Now select a branch as merge target and merge the changes:
```
git checkout master
git merge upstream/master
```
Now you can push your local merged changes to your forked repo, aka ```origin```.
```bash
git push origin [master]
```
Note that the branch name (```master```) is optional if you are pushing to same branch.

Now if you go to github portal your forked branch, you will find the status as:
'This branch is even with <ORIGINAL_OWNER>'.
