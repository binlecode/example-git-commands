# git-search by git grep

Git provide utils to search through the code and commit messages.

## use git grep to search in source file

Git grep looks through the files in working directory.

```sh
git grep stash
# ignore case
git grep -i StaSH
# quote ' is optional, but needed for special chars
git grep '`master`'
git grep 'stash\s'

# -n (--line-number): show line number
# -p (--show-function): show function or section header
# --break and --heading provide better search result formatting
git grep -n -p --break --heading stash

# -c (--count): count number of matches for each file
git grep -c merge
```

git grep can search across all branches.

```sh
# grep on revision list with --all: all the refs
git grep "string/regexp" $(git rev-list --all)

# same result with xargs command
# also sometimes useful to pipe result to a text file
git rev-list --all | xargs git grep "string/regexp" >> found.txt
```

But be careful with `rev-list --all` for large repo since it may take
a very long time to scan.
