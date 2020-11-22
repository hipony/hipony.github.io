+++
baseURL = "https://hipony.github.io/blog"
title = "Git 101"
date = 2020-11-20T21:10:51+03:00
tags = ["git"]
categories = []
draft = true
+++

```bash
% git commit -am 'Hello, World!'
```

Git is a version control system that everyone in the industry expects you to know. Although its interface is very counter-intuitive and confusing for beginners - the underlying model is fairly simple.

So here we'll look into basic Git commands which will be enough for you to go by in any project.

<!--more-->

## Requisites

I proceed further with an assumption that you already installed Git via whatever means are common on your system, would it be a system package manager or [a direct download link](https://git-scm.com/download/), and that we'll use a terminal to get a better feeling of Git commands.

> On Windows, Git comes with a shell executable which I suggest you to use.

While there is a lot of graphical interfaces for Git and you could consider installing them later on - it's always useful to be familiar with the command line. A lot of great tools come with only a command line interface and even for yourself - it's much easier to just write a console application.

## Help

We start from the most important command in any tool. The `--help` command.

When you forget how to work with Git you can always call the `git --help` to see the list of all commands with a short description. To see a more verbose description for a specific command we can call `git <command> --help`, for example - `git clone --help`.

Be sure to remember about this command when you're uncertain how something does work.

## Creating a Git Repository

Great! We're all set to start experimenting with Git. Let's begin from creating a Git repository. For that, we'll need a folder which we want to start to version control. It doesn't matter how the folder is called because the identity of the project is stored in the embedded `.git` folder.

### Init

To start tracking a project we'll use the `git init` command _inside_ a folder. It'll create an embedded `.git` folder which will store all our version history.

Here is an example of creating a folder and create a Git repository in it (I'm using the `%` character to indicate the start of a command):

```bash
% mkdir git-101 && cd git-101 # Create the folder
% git init                    # Initialize Git repositroy
Initialized empty Git repository in .../git-101/.git/
% ls -a                       # Check the folder contents
.git
```

To remove Git from tracking the current folder - it's enough to just remove the inner `.git` folder. But be careful since it'll remove all the history too.

### Default Branch

By default, Git will initialize a _branch_ called `master`. Imagine if in a video game your save files would never get overriden, but preserve all previous save files in its slot and allow to to go back any time. And would allow to _branch_ out to different save files based on the choices you made in the game story.

We'll talk more about branches a bit later.

There are multiple different ways to change the name of the created by default branch.

- Use the `-b <name>` command line argument

- Configurate a global default branch name with: `git config --global init.defaultBranch <name>`, we'll talk about `git config` a bit later.

For now, let's try to change the branch name to `dev`:

```bash
% rm -rf .git     # Delete a Git repository
% git init -b dev # Create a Git repository with a `dev` default branch
Initialized empty Git repository in ../git-101/.git/
```

## Tracking Files

In a Git repository each file can be in one of the two states: `tracked` or `untracked`. All files in a new repository are `untracked` by default. Let's see how to start tracking their versions.

### Add

To check the state of our repository we can use the `git status` command.

```bash
% git status
On branch dev

No commits yet

nothing to commit (create/copy files and use "git add" to track)
```

There is quite a lot of information! What we're interested for now is the suggestion

> create/copy files and use "git add" to track

Since our directory is new and empty - there is nothing to track. Let's try to create an empty text file and check again.

```bash
% touch file.txt # Create an empty file called `file.txt`

% git status
On branch dev

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        file.txt

nothing added to commit but untracked files present (use "git add" to track)
```

The suggestion has changed! We can check that our new `file.txt` is `untracked`. Add Git helpfully suggests to use the `git add` command. Let's try that and see what changes with `git status`.

```bash
% git add file.txt # Start tracking the file

% git status
On branch dev

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   file.txt
```

Now our file is `tracked` and Git will follow any changes we make to this file. But Git will not keep the history of changes to the file unless we make a "save" which is called a _commit_ in Git terminology.

Until we make a commit we can track and untrack files any number of times - it'll not be reflected in the Git history. In case we added a file by accident - we can untrack the file by writing `git rm --cached <file>` as suggested. Let's do that:

```bash
% git rm --cached file.txt # Untrack the file

% git status
On branch dev

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        file.txt

nothing added to commit but untracked files present (use "git add" to track)
```

### Config

Before we proceed to commiting, Git requires some configuration on your machine. To do that we will use the `git config` command.

#### Your Identity

You need to fill in your name and email which will be used to mark commit messages that you make so people will know from whom which commit came from. Git designed around multiple working on the same project at once!

```bash
% git config --global user.name "John Doe"
% git config --global user.email johndoe@example.com
```

#### Your Editor

By default, Git will pick up the system's default editor and quite often it's Vim.

While quitting Vim is a very common joke among programmers because it's an editor with character - there is very little to laugh at when you only want to learn a version control tool. Its shortcuts and workflows are vastly different from modern day editors and IDEs, so for many folks it could be a challenge to figure out how to even write a commit message.

To change the editor to the one we're accustomed with we can write these lines:

```bash
% git config --global core.editor emacs
# OR
% git config --global core.editor "C:/Program Files/Notepad++/notepad++.exe"
```

> **NOTE** that Git on Windows requires a full path to the editor

You can read more about `git config` in the [Git Book](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup).

### Commit

Let's try to "save" our file in the Git history. When you enter the `git commit` command - it'll open the editor specified by your configuration to enter a "commit message". There are different conventions on what to write in a commit message, but I suggest to use a present simple form starting from a verb. Let's name our commit "Add file.txt".

```bash
% git add file.txt # Prepare files for the commit

% git commit       # Do the commit!
[dev (root-commit) 5f518df] Add file.txt
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 file.txt
% git log
On branch dev
nothing to commit, working tree clean
```

> We can also specify whole directores such as `git add .` (prepare all files in the current directory for a commit) for convenience. But know for sure that we will never need certain files in our tracking history - such as build artifacts. [For that we use the `.gitignore` file](https://git-scm.com/book/en/v2/Git-Basics-Recording-Changes-to-the-Repository).

### Log

And now `file.txt` state is saved by git. It's committed! You can observe the history of commits with the `git log` command.

```bash
% git log
commit 5f518df3552f322fbf21534b7a8cbc64d638338e (HEAD -> dev)
Author: Alexandr Timofeev <alexandr.p.timofeev@gmail.com>
Date:   Sun Nov 22 20:18:54 2020 +0300

    Add file.txt
```

> Try out `git log --help` to see additional arguments that you can pass!

Now let's make a change in the file. For example we'll write `Hello, World!` in the file.

```bash
% echo 'Hello, World!' > file.txt # Write 'Hello, World!' into the `file.txt`

% git status
On branch dev
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   file.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

### Diff

To inspect changes that we make we can use the `git diff` command. You can specify `git diff <file>` if you only want to see changes in specific files instead of all.

```bash
% git diff
diff --git a/file.txt b/file.txt
index e69de29..8ab686e 100644
--- a/file.txt
+++ b/file.txt
@@ -0,0 +1 @@
+Hello, World!
```

When we're finished with the changes - we can make another commit. This time we'll use a shorter version using command line arguments. The `a` argument will add all `modified` (not `new`!) files to the commit and the `m` argument will allow to write the commit message inline in the terminal instead of the editor.

```bash
% git commit -am 'Hello, World!'
[dev 059f478] Hello, World!
 1 file changed, 1 insertion(+)

% git log
commit 059f47894ab061fe195f7302471902bdb7f178df (HEAD -> dev)
Author: Alexandr Timofeev <alexandr.p.timofeev@gmail.com>
Date:   Sun Nov 22 20:38:42 2020 +0300

    Hello, World!

commit 5f518df3552f322fbf21534b7a8cbc64d638338e
Author: Alexandr Timofeev <alexandr.p.timofeev@gmail.com>
Date:   Sun Nov 22 20:18:54 2020 +0300

    Add file.txt
```

## Branches

Now let's talk about _branches_. Git is designed around distributed development process, allowing you to utilize its full power offline on your local machine. Even if you don't need to share your work - Git is still extremely useful to keep track of working versions that you make, experimenting on side-features or returning back to the last working state.

Each branch represents a separate history of commits. In fact, the _branch_ itself is merely a pointer to the last commit in a list of commits, each pointing to the previous one. That way you can have multiple branches that represent exactly the same commits at the same time.

### Switch

Let's create a branch!

```bash
% git branch            # See existing branches
* dev

% git log --oneline     # See the log
059f478 (HEAD -> dev) Hello, World!
5f518df Add file.txt

% git branch -c feature # Create a branch called feature

% git branch            # See existing branches
* dev
  feature

% git switch feature    # Switch to the feature branch

% git branch            # See existing branches
  dev
* feature

% git log --oneline     # See the log
059f478 (HEAD -> feature, dev) Hello, World!
5f518df Add file.txt
```

Here we get to know the `git branch` command which is responsible for operations on branches. Don't forget to `git branch --help` it!

By default, `git branch` will show us the list of existing branches and the one we're currently at with the `*` symbol.

We can create a copy of the _current_ branch pointer (not the commits themselves), which is the `dev` branch in our case, by calling the `git branch -b <name>` command. In our case we create a branch named `feature`. We can check that it was created with the `git branch` command.

But note that we're still on the `dev` branch. To change the branch you can use the `git switch <name>` command. In fact there is a shortcut that creates the branch if it's missing - `git switch -c <name>`.

Let's create another branch called `another`.

```bash
% git switch -c another # Create and switch to a new branch
Switched to a new branch 'another'

% git branch            # Check that we indeed moved to a new branch
* another
  dev
  feature

% git log --oneline     # Now we have three pointers to the latest commit!
059f478 (HEAD -> another, feature, dev) Hello, World!
5f518df Add file.txt
```

### Restore

Now that we're in an `another` branch - our work from here on will have a separate history from other branches. Let's do some changes!

```bash
% touch another.txt         # Create another file

% echo 'Oh, No!' > file.txt # Overwrite the contents of the `file.txt`

% git status
On branch another
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   file.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        another.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

But before we commit - we decided that we no longer need the changes in the `file.txt`. To bring back the latest saved state of the file - let's use the `git restore file.txt` as git friendly suggests us!

```bash
% git restore file.txt

% git status
On branch another
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        another.txt

nothing added to commit but untracked files present (use "git add" to track)
```

Note that we can't restore an `untracked` file:

```bash
% git restore another.txt
error: pathspec 'another.txt' did not match any file(s) known to git
```

What if we already prepared files for the commit?

```bash
% echo 'Oh, No!' > file.txt    # Overwrite the file again

% git add another.txt file.txt # Prepare files for the commit

% git status
On branch another
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   another.txt
        modified:   file.txt

% git restore file.txt         # Attempt to restore the file

% git status                   # It (silently) failed!
On branch another
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   another.txt
        modified:   file.txt
```

Files that are prepared for the commit, but not yet committed are called `staged` files. To restore them we need to add the `--staged` argument as Git suggests. But it'll only remove the file from the staging area, it'll not restore the previous committed state.

```bash
% git restore --staged file.txt

% git status # File is still in the modified state!
On branch another
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   another.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   file.txt

```

We can restore file all at once with a `git restore --staged --worktree file.txt` or `git restore -S -W file.txt` for short.

```bash
% git add file.txt # Add the file back in

% git status
On branch another
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   another.txt
        modified:   file.txt

% git restore -S -W file.txt

% git status
On branch another
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   another.txt
```

Alright, but what if _made_ a commit which we don't like?

```bash
% git commit -m 'Add another.txt'
[another c96fdd3] Add another.txt
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 another.txt

% git log --oneline
c96fdd3 (HEAD -> another) Add another.txt
059f478 (feature, dev) Hello, World!
5f518df Add file.txt
```

> Note how the `another` pointer moved, but old ones are pointing to the old commit.

In case of such mistakes we can do two things, either create a new commit that erases our changes with `git revert HEAD` or to erase the commit altogether with `git reset HEAD~1`. Note that this is the `HEAD` from the `git log` message, it's an alias to the current branch's pointer name.

Be warned with the `git reset` - you should only erase a Git history if you're sure that no one has a copy of it. For example, when doing a feature locally before publishing it on a _remote_ repository. After that - just add a commit, since people could start to depend on the commit that you want to erase.

```bash
% git revert HEAD         # Add a commit reverting changes done by the previous one
Removing another.txt
[another a698d86] Revert "Add another.txt"
 1 file changed, 0 insertions(+), 0 deletions(-)
 delete mode 100644 another.txt

% git log --oneline
c7e6a0d (HEAD -> another) Revert "Add another.txt"
c96fdd3 Add another.txt
059f478 (feature, dev) Hello, World!
5f518df Add file.txt

% git reset HEAD~1        # Notice that changes done by the revert commit are still there
Unstaged changes after reset:
D       another.txt

% git log --oneline       # But the commit is gone!
c96fdd3 (HEAD -> another) Add another.txt
059f478 (feature, dev) Hello, World!
5f518df Add file.txt

% git restore another.txt # Manually restore the file

% git status
On branch another
nothing to commit, working tree clean
```

Instead of just `git reset HEAD~1` we can use a more destructive form - `git reset HEAD~1 --hard`, which will restore all the files changed by the commits we reset.

```bash
% git revert HEAD # Revert again
Removing another.txt
[another 5a1efc0] Revert "Add another.txt"
 1 file changed, 0 insertions(+), 0 deletions(-)
 delete mode 100644 another.txt

% git reset HEAD~1 --hard
HEAD is now at c96fdd3 Add another.txt

% git status      # All is gone!
On branch another
nothing to commit, working tree clean
```

Be careful with that though.

> Remember the golden rule! Do **not** erase the published history. **Never**.

#### Merge

It's time to pull our changes together.

##### Fast-forward

```bash
% git log --oneline
c96fdd3 (HEAD -> another) Add another.txt
059f478 (feature, dev) Hello, World!
5f518df Add file.txt

% git switch dev    # First, switch back to the branch into which we want to merge to
Switched to branch 'dev'

% git merge another # Do the merge
Updating 059f478..c96fdd3
Fast-forward        # Note the `Fast-forward` here!
 another.txt | 0
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 another.txt

% git log --oneline
c96fdd3 (HEAD -> dev, another) Add another.txt
059f478 (feature) Hello, World!
5f518df Add file.txt
```

The pointer to the `dev` branch has moved to the position of the `another` branch. It happened because the `dev` was a direct ancestor of the branch that we want to merge in so Git just moves the pointer which is called "fast-forwarding".

##### Merge Commit

But let's say our history in the `dev` branch diverged a bit from the `feature` branch that we want to merge. Let's create some changes on the `feature` branch and merge them.

```bash
% git switch feature
Switched to branch 'feature'

% touch feature.txt  # Create a file

% git add feature.txt

% git commit -m 'Add feature.txt'
[feature 5379073] Add feature.txt
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 feature.txt

% git log --oneline  # We no longer see the `dev` pointer in here!
5379073 (HEAD -> feature) Add feature.txt
059f478 Hello, World!
5f518df Add file.txt

% git switch dev
Switched to branch 'dev'

% git merge feature  # Now we see something different
Merge made by the 'recursive' strategy.
 feature.txt | 0
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 feature.txt

% git log --oneline  # This looks confusing
e598ff0 (HEAD -> dev) Merge branch 'feature' into dev
5379073 (feature) Add feature.txt
c96fdd3 (another) Add another.txt
059f478 Hello, World!
5f518df Add file.txt

% git log --graph --oneline # Check the `git log --help`!
*   e598ff0 (HEAD -> dev) Merge branch 'feature' into dev
|\
| * 5379073 (feature) Add feature.txt
* | c96fdd3 (another) Add another.txt
|/
* 059f478 Hello, World!
* 5f518df Add file.txt
```

Now, since Git can't simply move the pointer - it automatically resolved all the differences between two branches and create a _merge commit_. Some Git conventions rely on _merge commits_ to keep the history pretty but we'll not discuss it today.

##### Merge Conflicts

Sometimes Git's automatic merge resolving tool fails because two branches changed the same files at some point. Such conflicts require a manual intervention. You can read about ways to resolve such conflict in [the Git Book](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging).

## Remote

Alright, we've made some changes in our local Git repository. But how do we share it to cooperate with other developers?

git remote
git push
git clone
git pull

## Everything Else

Here we conclude our short introduction of basic commands for Git. Git allows us to use a very vast variety of commands and command arguments, but a lot of them are useful only for very specific cases. Very often they're implemented in terms of basic commands we introduced in here and most of the programmers out there prefer to not bother much and just do the job in a bit more verbose, maybe inefficient, way.

If you wish to learn more about Git - consider starting from the [Git Book](https://git-scm.com/book/en/v2).
