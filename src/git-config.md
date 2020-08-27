

## three levels of config

Git has config at three levels: system, global and repo, each level overrides values at the previous level.

- System level: applied to every user on the system and all their repositories
- Global level: values specific personally to you, the user
- Repository level: specific to that single repository

To see combined config:

```bash
git config --list --show-scope
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


To find how a particular setting is specified in the layered configuration, 
for example, to check `credential.helper` setting:

```bash
git config --get-all --show-origin credential.helper
```

This will list all configuraiton files containing that setting.

