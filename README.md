# Git-Notes

### Show commit difference

`git diff COMMIT^ COMMIT` will show you the difference between that COMMIT's ancestor and the COMMIT

```bash
git diff 858f43e72f77fae74301eb243de7f6180510534c^ 858f43e72f77fae74301eb243de7f6180510534c
```

Alternatively, git show COMMIT will do something very similar. (The commit's data, including its diff.) 

```bash
git show 858f43e72f77fae74301eb243de7f6180510534c
```

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

### remote

```bash
git remote -v # show all remotes
git remote update --prune # update remote ref in current repo
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
### reset, revert

`git reset <commit-SHA>` takes you to `<commit-SHA>` and delete all commit after `<commit-SHA>`

`git revert <commit-SHA>` undoes a commit by creating a new commit. This is a safe way to undo changes, as it has no chance of re-writing the commit history

**NOTE:** 

You have

`A--B`

and you want

`A--B--C`

where the state of C is identical to the state of A, correct?  Then
you should run:

`git revert B`

The commit(s) you pass to `git revert` are the commits you want to
undo, not the state that you want to roll back to.

if you have multiple commits you want to `revert`, for example:

In case of `A -> B -> C -> D -> HEAD` and you want `B` `C` `D` `HEAD` to be undo but still keep the commits, you can do

```bash
$ git revert --no-commit D
$ git revert --no-commit C
$ git revert --no-commit B
$ git commit -m "the commit message"
```

or 

```
git revert master~3..master # revert all commit in the range of B and HEAD including B and HEAD. Note master~3 is pointing to B
```

`--no-commit`

Usually the command git revert automatically creates some commits with commit log messages stating which commits were reverted.

To avoid automatic commit there's the option `-n` (or `--no-commit`).

But after this command, the reverted files are in the staged area. I can unstage them by using the command `git reset HEAD`

Alternate solution to `git revert` would be to `checkout` contents of commit `A`, and commit this state

### `~`, `^` explanation

`ref~` is shorthand for `ref~1` and means the commit's first parent. `ref~2` means the commit's first parent's first parent. `ref~3` means the commit's first parent's first parent's first parent. And so on.

`ref^` is shorthand for `ref^1` and means the commit's first parent. But where the two differ is that ref^2 means the commit's second parent (remember, commits can have two parents when they are a merge).

The `^` and `~` operators can be combined.

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
 
 ### Show current tag

```bash
 git describe --tags
 ```
 
### Moving a tag

```bash
# -f
# --force
# Replace an existing tag with the given name (instead of failing)

git tag -f
```

You probably want to use -f in conjunction with -a to force-create an annotated tag instead of a non-annotated one.

**Example**

Delete the tag on any remote before you push

```bash
git push origin :refs/tags/<tagname>
```

Or, more expressively, use the `--delete` option: 

```bash
git push --delete origin tagname
```

Replace the tag to reference the most recent commit

```bash
git tag -fa <tagname>
```

Push the tag to the remote origin

```bash
git push origin master --tags
```

### Symbolic ref HEAD

[good resource for head and rebase](http://stackoverflow.com/questions/5772192/how-can-i-reconcile-detached-head-with-master-origin)

**HEAD** is the symbolic name for the currently checked out commit. When HEAD is not detached (the “normal”1 situation: you have a branch checked out), HEAD actually points to a branch’s “ref” and the branch points to the commit. HEAD is thus “attached” to a branch. When you make a new commit, the branch that HEAD points to is updated to point to the new commit. HEAD follows automatically since it just points to the branch.

-- `git symbolic-ref HEAD` yields `refs/heads/master`. 
	
The branch named “master” is checked out.

-- `git rev-parse refs/heads/master` yield `17a02998078923f2d62811326d130de991d1a95a`. 

That commit is the current tip or “head” of the master branch.

-- `git rev-parse HEAD` also yields `17a02998078923f2d62811326d130de991d1a95a`

This is what it means to be a “symbolic ref”. It points to an object through some other reference.

(Symbolic refs were originally implemented as symbolic links, but later changed to plain files with extra interpretation so that they could be used on platforms that do not have symlinks.)

We have `HEAD` → `refs/heads/master` → `17a02998078923f2d62811326d130de991d1a95a`

When HEAD is detached, it points directly to a commit—instead of indirectly pointing to one through a branch. You can think of a detached HEAD as being on an unnamed branch.

-- `git symbolic-ref HEAD` fails with fatal: `ref HEAD is not a symbolic ref`

-- `git rev-parse HEAD` yields `17a02998078923f2d62811326d130de991d1a95a`

Since it is not a symbolic ref, it must point directly to the commit itself.

We have `HEAD` → `17a02998078923f2d62811326d130de991d1a95a`

The important thing to remember with a detached HEAD is that if the commit it points to is otherwise unreferenced (no other ref can reach it), then it will become “dangling” when you checkout some other commit. Eventually, such dangling commits will be pruned through the garbage collection process (by default, they are kept for at least 2 weeks and may be kept longer by being referenced by HEAD’s reflog).

1 It is perfectly fine to do “normal” work with a detached HEAD, you just have to keep track of what you are doing to avoid having to fish dropped history out of the reflog.

### checkout remote branch

You need to create a local branch that tracks a remote branch. The following command will create a local branch named daves_branch, tracking the remote branch origin/daves_branch. When you push your changes the remote branch will be updated.

```bash
git checkout --track origin/daves_branch
```

`--track` is shorthand for `git checkout -b [branch] [remotename]/[branch]` where [remotename] is origin in this case and [branch] is twice the same, daves_branch in this case.

### Delete remote branch

```bash
# This will delete the remote branch
git push origin :<branch-name>
```

Then delete the local one

```bash
# This will delete the local branch
git branch -d <branch-name>
```

### Change remote url

```bash
git remote set-url origin git@gitserver.com:user/repo_name.git

git remote -v

# output
# origin   ssh://git@gitserver.com:user/repo_name.git (fetch)
# origin   ssh://git@gitserver.com:user/repo_name.git (push)
```

What the `git remote set-url` command actually does is update the repository `.git/config` file with a new URL to the remote repository.

```bash
# .git/config

...

[remote "origin"]
url = git@gitserver.com:user/repo_name.git
fetch = +refs/heads/*:refs/remotes/origin/*
```
