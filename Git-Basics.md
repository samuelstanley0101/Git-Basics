# Git Basics

*A Guide by Samuel Stanley*

This document should explain some basics of working with Git in regards to managing versions of your files, syncing changes across devices, and downloading other peoples' code. **There is a summary at the bottom if you just need a quick command reference**.

## Table of contents

1. [What is Git?](#what-is-git)
2. [Set up your Git environment](#set-up-your-git-environment)
     - [Install Git](#install-git)
     - [Create a repository](#create-a-repository)
3. [(Optional) Set up GitHub](#optional-set-up-github)
     - [Install the GitHub CLI](#install-the-github-cli)
     - [Add your public key to GitHub](#add-your-public-key-to-github)
     - [(Optional) Set up the ssh-agent](#optional-set-up-the-ssh-agent)
4. [Getting other people's code](#getting-other-peoples-code)
     - [Clone a repository](#clone-a-repository)
     - [Git pull](#git-pull)
5. [Editing files](#editing-files)
     - [Git add](#git-add)
     - [Git commit](#git-commit)
     - [Git push](#git-push)
6. [Branches](#branches)
     - [List all branches](#list-all-branches)
     - [The main or master branch](#the-main-or-master-branch)
     - [Create a branch](#create-a-branch)
     - [Switch to a different branch](#switch-to-a-different-branch)
     - [Merge a branch](#merge-a-branch)
     - [Remote branches](#remote-branches)
     - [Delete a branch](#delete-a-branch)
7. [Merge conflicts](#merge-conflicts)
8. [Summary](#summary)

## What is Git?

Git is a versioning system, which means that it tracks changes made to a project. This is essential when working with other people on a project. It is vitally important to know that Git does not store versions of files - **it stores individual changes made to files in the repository.** Different versions of files are then constructed from the history of changes.

Git stores files in **repositories**, sometimes abreviated to **repos**. Each Git repository, which is in fact just a normal directory, has a hidden folder named `.git` which stores all the changes made to files, all the remote connections to other repositories, and any other information Git needs to function.

## Set up your Git environment

### Install Git

Many computers may come with Git pre-installed - you can check if it's already on your computer using `git --version`. If it's not already on your computer then you can use your package manager to install it. For example, if you're on Ubuntu then you would run `sudo apt install git` to install it, or if you're on a Mac you might run `brew install git` (assuming you've [installed Homebrew](https://brew.sh)).

### Create a repository

*If you're working with other people then this probably shouldn't be your first step unless you're the one creating your project*.

```bash
git init [path-to-directory]
```

This command initializes a new repository at the path you specify. **If you don't specify a path then it'll just create a repository in your current directory**. Remember that a Git repository is just a normal directory with a hidden `.git` folder, so all this does is add that folder to whatever directory you specify and, if the directory doesn't already exist, creates it. **This does not delete any of your files**.

**Examples**:

```bash
# this creates a repository at my current directory
git init
# this creates a repository in the folder in my current directory called my-project
# if the directory my-project doesn't exist then git creates one
git init my-project
# this creates a repository at ~/Development/my-other-project
# if the directory doesn't exist then git creates it
git init ~/Development/my-other-project
```

## (Optional) Set up GitHub

GitHub is the most popular site to host repositories on. It's owned by Microsoft and is not associated with the Software Freedom Conservancy, who manages Git, but is where you'll find most Open Source projects and offers free accounts. You can sign up [here](https://github.com).

### Install the GitHub CLI

GitHub offers its own CLI (Command Line Interface) for you to manage your repositories through the terminal if that is what you prefer. **If you're using GitHub's pull request system to work with other people then this offers a convenient way to create pull requests without leaving your code editor**.

### Add your public key to GitHub

Often you'll work with private repositories on GitHub, whether they be your own or those of classmates you work on projects with. To access these repositories, you need to be able to access your account from the command line. You can do this using the GitHub CLI, but it's better to do it using pure Git methods since these will transfer to non-GitHub systems as well.

To access your account you need to use an **SSH key**, which is a kind of authentication used for SSH connections. This is more secure than a username and password, which is why GitHub requires it. Below are the steps to use an SSH key to authenticate:

**Generate a new SSH key**:

```bash
# -f: specifies the file to save the key as. name it whatever you want,
#     but it should always go in the ~/.ssh directory. I named my key
#     "~/.ssh/github_key"
# -C: specifies a comment that goes at the end of the public key file.
#     this is mostly just to help you remember the purpose of the key
ssh-keygen -f ~/.ssh/[key file name] -C "comment"
# remember to save the password to your key
```

**Add the SSH key to your GitHub account**:

1. Two files will have been generated, one just called **[key file name]** and the other called **[key file name].pub**. Type `cat ~/.ssh/[key file name].pub` to print your **public** key to the terminal and copy the resulting output.
2. Go to https://github.com/settings/keys or go to your GitHub profile > SSH and GPG keys.
3. Click **New SSH key**, add a title that describes the computer your key is saved on, and paste your public key into the **Key** section.
4. Click **Add SSH key** and authenticate if needed.

**Never put your private key (the one without a file extension) anywhere but on your computer**. Anyone with that key (and its password) has access to your GitHub account.

### (Optional) Set up the ssh-agent

Although not necessary, setting up the ssh-agent will allow you to only type in the passphrase for your SSH key once per terminal session, which can be very convenient for performing lots of remote actions. Instructions can be found [here](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent?platform=linux). Once set up you will need to manually re-activate the ssh-agent every terminal session, which can be done with `eval $(ssh-agent)` followed by `ssh-add`.

## Getting other people's code

### Clone a repository

To download someone else's repository we say we **clone** the **remote** repository.

```bash
git clone [address-of-repository] [directory-to-clone-to]
```

This creates a copy of the repository you specified in the directory you gave. If you don't give a directory then a new directory is created at your current directory with the name of the repository. This copy stores the address it came from and, as we'll see later, can update itself when you tell it to.

The address of the repository can simply be its web address (Ex. `https://github.com/eza-community/eza`), but this can only be relied upon if the repository is public. Although authentication can be done over HTTPS, it is not quite as secure as authentication over SSH. In fact, GitHub has dropped support for username and password authentication over HTTPS and requires [Personal Access Tokens](https://stackoverflow.com/questions/68775869/message-support-for-password-authentication-was-removed) instead to use HTTPS.

**The preferred address to use, then, is the SSH address**. This has the syntax of `git@[hosting-site]:[username]/[repository-name]` (Ex. `git@github.com:yt-dlp/yt-dlp`). To add authentication for this method, you can [add your public key to GitHub](#add-your-public-key-to-github).

**Examples**:

```bash
# this clones the repository to a directory called eza from the current directory
git clone https://github.com/eza-community/eza
# this clones the repository to a directory called downloader at the home directory
git clone git@github.com:yt-dlp/yt-dlp ~/downloader
```

### Git pull

To download any recent changes to a repository, navigate to your copy of the repo. **Any further instructions will assume you are in the directory housing your repository**.

```bash
git pull
```

This **pulls** any recent changes from the remote repository to your computer. This command is actually a combination of two different commands: `git fetch`, which downloads the changes and puts them in the branch `origin/main`, and `git merge origin/main`, which merges the changes from `origin/main` into the `main` branch (more on branches later). This is assuming that everything you're doing is with the main branch, which you can think of as the default.

## Editing files

Git does not require any special commands for you to start editing your files - you can mostly treat the repository as a normal directory in that sense. What you do need commands for is saving specific versions of those files.

### Git add

**By default Git does not track changes to the files in the directory for a repository**. You must tell Git which files you want to track.

```bash
git add [file-1] [file-2] ...
```

Adding a file tells Git, "this is a file I've finished changing and want to queue to be saved". **This does not save this version of that file**, you must commit your changes for that. We call this **staging your changes**, as in you are setting the stage to commit your changes.

If you want to add all your files at once you can use `git add --all` or `git add -A`.

### Git commit

Once you have staged your changes by adding what files you want, you **commit** your changes, creating a concrete version of your files at a specific point in time.

```bash
git commit -m [message]
```

This creates a commit from the files you've added, leaving any others as they are. You can think of it as creating a node in a list, where each node is a commit. **Git does not autosave versions for you - you must be the one to decide when to create a new version**.

Although not techically required, every commit should have a message describing what changes were made, usually only in a few words (Ex. `Added out of bounds test`).

### Git push

Once you've committed your changes you can **push** them back to the remote repository.

```bash
git push
```

**You should always pull recent changes before pushing to the remote repo** so that you are not trying to overwrite anyone else's work. You may not push after every commit, depending on your team's strategy.

## Branches

One of Git's most important features is **branches**, which are a way of developing different versions of the same files at the same time and then merging the versions later. If a normal chain of commits is like a linked list of individual nodes then using branches is like having a tree of commits, except its branches eventually join back together.

### List all branches

```bash
git branch
```

### The main or master branch

Most repositories have one central branch called either `main` or `master`. When the first commit is made in a repository, the main branch is created automatically. Any other branches are descendants of this branch.

### Create a branch

```bash
git branch [branch-name]
```

This creates a branch that is an exact copy of the most recent commit in your current branch. Your current branch could be the main branch or it could be a different branch - it works the same way. Almost anything you could do on the main branch you can do on a different branch too.

### Switch to a different branch

```bash
git switch [branch-name]
```

Fairly straightforward. This switches your current branch to the branch you specify. If you have uncommited changes in your current branch Git may not let you switch until you commit or stach them.

### Merge a branch

```bash
git merge [branch-name]
```

This attempts to combine your current branch and the branch you name into a new commit on your current branch. There are two types of merges:

1. **Fast-Forward Merge:** Any two branches will always share some common ancestor. If your branch hasn't changed since that common ancestor, then it's very easy to combine the two; Only one branch has any changes! Your branch just becomes the branch you were merging with - same data and everything. If you imagine the most recent commit on your branch as a pointer to a node in a tree, your branch just now points to the same node as the branch you were merging with. **Fast-Forward Merges cannot have merge conflicts.**
2. **Merge Commit:** If both branches have been changed since their most recent common ancestor, then Git creates a new commit on your branch with the changes from the most recent commits of both branches. Git does its best to combine the changes smoothly, but sometimes it runs into a **merge conflict** (more details below).

### Remote branches

At times you may want to pull from a branch other than the main branch, which Git allows you to do.

```bash
# see all available remote branches
git branch -r  # -r is short for --remote
# duplicate the remote branch locally
git branch -t [local-branch-name] origin/[remote-branch-name]  # -t is short for --track
```

Notice that you choose the name of the local branch. It's often a good idea to keep its name the same as the remote branch, but you can change it if you want. The new branch will be fully linked to the remote branch - you can pull and push to it to your heart's content. You can switch to it just like any other branch.

### Delete a branch

```bash
git branch -d [branch-name]
```

This won't delete a branch if it has un-merged commits. Use `-D` if you don't care about that.

## Merge Conflicts

Occasionally you may encounter merge conflicts when using `git merge` or `git pull`. These may seem daunting to deal with, but in reality they're not that difficult.

When Git detects a merge conflict, it pauses the merging process and adds some text to the places in the file with conflicts. You can manually resolve these conflicts by removing this text, editing the file to combine the code however you want, running `git add` on the previously conflicted files, and running `git merge --continue`. However, there are also many tools to help you resolve these conflicts, some of which may be baked into your code editor. VS Code, for example, has a great merge editor that makes this process much easier (tutorial [here](https://www.youtube.com/watch?v=HosPml1qkrg))

## Summary

### Getting other people's code

```bash
# clone a repository
git clone [address-of-repository] [directory-to-clone-to]
# pull recent changes from the remote repository
git pull
```

### Editing files

```bash
# stage changes for commit
git add [file-1] [file-2] ...
git add -A  # -A is short for --all
# commit staged changes
git commit -m [message]
# push committed changes to the remote repository
git push
```

### Branches

```bash
# list all branches
git branch
# create a new branch
git branch [branch-name]
# switch to another branch
git switch [branch-name]
# merge changes from another branch into the current branch
git merge [branch-name]
# see all remote branches
git branch -r  # -r is short for --remote
# create a local copy of a remote branch
git branch -t [local-branch-name] origin/[remote-branch-name]  # -t is short for --track
# delete a local branch
git branch -d [branch-name]  # Use -D to force delete if the branch has unmerged changes
```
