# Git-Notes

### Show status

`--short` option to give a more brief view

```bash
git status --short
```

### List branches

```bash
git branch # list all local branches
git branch -r # list all remote branches
git branch -a # list all local and remote branches
```

### To pull

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

To clear all stash

```bash
git stash clear
```

To remove a single stash, if no `<stash>` is given, it will remove the latest one

```bash
git stash drop <stash>
```

### Tagging

List your tags

```bash
$ git tag
v0.1
v1.3
```

You can also search for tags with a particular pattern. The Git source repo, for instance, contains more than 500 tags. If you’re only interested in looking at the 1.8.5 series, you can run this:

```bash
$ git tag -l "v1.8.5*"
v1.8.5
v1.8.5-rc0
v1.8.5-rc1
v1.8.5-rc2
v1.8.5-rc3
v1.8.5.1
v1.8.5.2
v1.8.5.3
v1.8.5.4
v1.8.5.5
```

Creating tags

Git uses two main types of tags: **lightweight** and **annotated**.

- A lightweight tag is very much like a branch that doesn’t change – it’s just a pointer to a specific commit.
- Annotated tags, however, are stored as full objects in the Git database. They’re checksummed; contain the tagger name, email, and date; have a tagging message; and can be signed and verified with GNU Privacy Guard (GPG). It’s generally recommended that you create annotated tags so you can have all this information; but if you want a temporary tag or for some reason don’t want to keep the other information, lightweight tags are available too.

**Annotated Tags**

Creating an annotated tag in Git is simple. The easiest way is to specify -a when you run the tag command:

```bash
$ git tag -a v1.4 -m "my version 1.4"
$ git tag
v0.1
v1.3
v1.4
```

The -m specifies a tagging message, which is stored with the tag. If you don’t specify a message for an annotated tag, Git launches your editor so you can type it in.

You can see the tag data along with the commit that was tagged by using the git show command:

```bash
$ git show v1.4
tag v1.4
Tagger: Ben Straub <ben@straub.cc>
Date:   Sat May 3 20:19:12 2014 -0700

my version 1.4

commit ca82a6dff817ec66f44342007202690a93763949
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Mon Mar 17 21:52:11 2008 -0700

    changed the version number
```

That shows the tagger information, the date the commit was tagged, and the annotation message before showing the commit information.

**Lightweight Tags**

Another way to tag commits is with a lightweight tag. This is basically the commit checksum stored in a file – no other information is kept. To create a lightweight tag, don’t supply the -a, -s, or -m option:

```bash
$ git tag v1.4-lw
$ git tag
v0.1
v1.3
v1.4
v1.4-lw
v1.5
```

This time, if you run git show on the tag, you don’t see the extra tag information. The command just shows the commit:

```bash
$ git show v1.4-lw
commit ca82a6dff817ec66f44342007202690a93763949
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Mon Mar 17 21:52:11 2008 -0700

    changed the version number
```

**Sharing Tags**

By default, the git push command doesn’t transfer tags to remote servers. You will have to explicitly push tags to a shared server after you have created them. This process is just like sharing remote branches – you can run `git push origin [tagname]`

```bash
$ git push origin v1.5
Counting objects: 14, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (12/12), done.
Writing objects: 100% (14/14), 2.05 KiB | 0 bytes/s, done.
Total 14 (delta 3), reused 0 (delta 0)
To git@github.com:schacon/simplegit.git
 * [new tag]         v1.5 -> v1.5
 ```
 
 If you have a lot of tags that you want to push up at once, you can also use the --tags option to the git push command. This will transfer all of your tags to the remote server that are not already there.
 
 ```
 $ git push origin --tags
Counting objects: 1, done.
Writing objects: 100% (1/1), 160 bytes | 0 bytes/s, done.
Total 1 (delta 0), reused 0 (delta 0)
To git@github.com:schacon/simplegit.git
 * [new tag]         v1.4 -> v1.4
 * [new tag]         v1.4-lw -> v1.4-lw
 ```
