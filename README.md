# Git-Notes

### `git status`

`--short` option to give a more brief view

```
git status --short
```

### `git pull`

`git pull` is really equivalent to running `git fetch` and then `git merge`. These are like a cache of the state of branches in the remote repository that are updated when you do git fetch (or a successful git push).

```bash
git pull origin master
```

### List ignored files

```bash
git status --ignored
```

### List files except ignored files

```bash
git ls-files
```

### Stash current work

Often, when you’ve been working on part of your project, things are in a messy state and you want to switch branches for a bit to work on something else. The problem is, you don’t want to do a commit of half-done work just so you can get back to this point later. The answer to this issue is the git stash command.

Stashing takes the dirty state of your working directory — that is, your modified tracked files and staged changes — and saves it on a stack of unfinished changes that you can reapply at any time.

```bash
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   buildout.cfg
	modified:   profiles/versions.cfg
```

then

```bash
$ git stash
Saved working directory and index state WIP on master: 78f45fe Added config changes to be deployed in dev
HEAD is now at 78f45fe Added config changes to be deployed in dev
```

then

```bash
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean
```

if

```bash
$ git stash list
stash@{0}: WIP on master: 78f45fe Added config changes to be deployed in dev
stash@{1}: WIP on master: 78f46de Added config changes to be deployed in dev
```

then

`git stash pop` is going to pop `stash@{0}` by default

```bash
$ git stash pop
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   buildout.cfg
	modified:   profiles/versions.cfg
```

if you want to apply another stash rather than stash@{0},

do `git stash apply`

```bash
git stash apply stash@{1}
```

to show changes in stash

```bash
git stash show -p stash@{0}
```
