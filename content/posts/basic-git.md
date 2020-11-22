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
% gut add file.txt
% git commit
[dev (root-commit) 5f518df] Add file.txt
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 file.txt
% git log
On branch dev
nothing to commit, working tree clean
```

### Log

And now `file.txt` state is saved by git. It's committed! You can observe the history of commits with the `git log` command.

```bash
% git log
# In editor:
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

git branch
git checkout -b
git checkout file
git merge

## Remote

Alright, we've made some changes in our local Git repository. But how do we share it to cooperate with other developers?

git remote
git push
git clone
git pull

## Everything Else

Here we conclude our short introduction of basic commands for Git. Git allows us to use a very vast variety of commands and command arguments, but a lot of them are useful only for very specific cases. Very often they're implemented in terms of basic commands we introduced in here and most of the programmers out there prefer to not bother much and just do the job in a bit more verbose, maybe inefficient, way.

If you wish to learn more about Git - consider starting from the [Git Book](https://git-scm.com/book/en/v2).
