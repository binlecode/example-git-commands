

## three levels of config

Git has config at three levels: system, global and repo, each level overrides values at the previous level.

- System level: applied to every user on the system and all their repositories
- Global level: values specific personally to you, the user
- Repository level: specific to that single repository

To see combined config:

```bash
git config --list --show-scope --show-origin
```

If this is run under a particular repo, then all combined configuration will be shown.
If this is not run under a repo, then sytem and global combined configuration will be shown.


To show system level config

```bash
git config --system --list
```

This config

To show configuration at a global level:

```bash
git config --global --list
```

This config is usually saved in ~/.git/config file


## credential config

To find out `credential.helper` setting:

```bash
git config --get-all --show-origin credential.helper
```

This will list all configuraiton files containing that setting.

To save git password to a file:

```bash
git config --global credential.helper 'store'

# at a specific repo
git config credential.helper 'store'
```

The password will be saved in `~/.git-credentials` file in the format like:
`https://GITUSER:GITPASSWORD@DOMAIN.XXX`.


## multiple github accounts

With multiple github accounts in macos, the default keychain can only keep one, 
which will block the other account. 
This is because by default the system level `credential.helper` is `osxkeychain`.
This should be unset (or comment out in system level gitconfig file).

In addition to that, the global gitconfig file should also unset or comment out
`credential.helper` setting.

Instead, unfortunately, only one account's credential could be saved in a file
store. To prevent cross-account auth rejection, each repo belonging to that 
account can set `credential.helper` to `store`, 
those repos will look for credential (of that account) in `~/.git-credentials` file.


Since macos has osxkeychain, we can use that for the other account.
For repos belonging to the other account, set `credential.helper` to `osxkeychain`.

```bash
git config credential.helper 'osxkeychain'
```

