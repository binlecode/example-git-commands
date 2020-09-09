---
marp: true

# This is a test markdown file for Marp presentation slide building
# See: https://marp.app/
# Marp VS Code plugin: https://github.com/marp-team/marp-vscode
# Use plugin to export to pdf, html and other formats
# Github: https://github.com/marp-team/marp
#

# built-in themes: default, gaia, etc
#theme: gaia
# invert style bg-fg color
class: invert
---

# git commit amend basics

- command
- recent changes
- multiple commits

---

# git-commit-amend

The ```git commit --amend``` command is a convenient way to modify the **most recent** commit.

It lets you combine staged changes with the previous commit instead of creating an entirely new commit. It can also be used to simply edit the previous commit message without changing its snapshot. But, amending does not just alter the most recent commit, it replaces it entirely, meaning the amended commit will be a new entity with its own ref.

---

#### revise recent commit message
This is common, as premature commit happens all the time.
```bash
git commit --amend -m "an updated commit message"
```

---

#### change committed content
It is also common that some file is forgotten in previous commit.
```bash
git add <forgotten-file>
git commit --amend --no-edit
```
Note the ```--no-edit``` option is used to tell git there's no need to change the original commit messager, just add the file.


---

#### push to remote
If you haven’t pushed the last commit yet to your remote, a simple regular push is enough. 

Otherwise, you **must** push with ```-f``` option because you’ve rewritten your commit history.
```bash
git push -f origin <branch-name>
```


---

Note: Always, use commit amend option cautiously, and, **NEVER** rewrite the commit history of public/team branches (like master). This will truly mess your teammates work. I personally only allow commit rewrite in individual feature branches to regulate premature or mistaken commits before merging to main branches.


---

#### change even older or multiple commits
To modify older or multiple commits, ```--amend``` is no longer userful. Git rebase is the only way to combine a sequence of commits into a new base commit, which literally rewrite history. Check out [git-rebase](./git-rebase.md) page.

