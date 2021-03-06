---
title: "Git 102"
date: 2019-01-03T00:48:56+02:00
draft: false
description: "More about Git tool"
categories: ["Versioning"]
featuredImage: "img/git-102.jpg"
dropCap: true
displayInMenu: false
displayInList: true

resources: 
- src: "img/*.jpg"
---

# Branching
With every **commit** command, this happen:

- **Git** checksums each *staged* files and store them in a **snapshot** tree.
- Stores *committed* files (blobs) in **Git** repository
- Creates a **commit** object that has:
    - Metadata
    - A pointer to the **snapshot** tree
    - A pointer to the **commit** that came immediately before it

Each **commit** object contains a pointer to the *snapshot* of the *staged* content, and pointers to the **commit** or **commits** that directly came before this **commit** (its parent or parents). In case of a result of **merge** of two or more **branches**, multiple parents for a **commit** that results from.

A **branch** is a lightweight movable pointer to one of the **commits**. The default **branch** name is *master*, created with `git init` command. 

Basically, **Branching** is a diverge from the main line of development and without impacting the main line.

## Creating Branch
The command `git branch <branch-name>` will create a new **branch**. Simply a new movable pointer, pointing to the current **commit**. 

**HEAD** is a special pointer that points to which **branch** is currently in use. This can be seen with `git log --oneline --decorate` or `git branch` command.

## Switching Branch
When a new **branch** is creating, **HEAD** pointer does not point to it. To switch to new **branch** `git checkout <branch-name>` command is used.

> `git checkout -b <branch-name>` will create a new **branch** and switch to it.

When **switching** **branches**, files in  working directory will change. **switching** to an older branch, working directory will be reverted to look like it did the last time **committed** on that **branch**.

> Changes are isolated in separate **branches** and can switch back and forth between the **branches** and **merge** them, at some point.

**Git lgos** can show *Divergent* history of a **Git** repository with this command

`git log --oneline --decorate --graph --all`

> Creating a new **branch** is as quick and simple as writing 41 bytes to a file (40 characters and a newline).

# Merging

To merge a branch to another, usually the *Master* branch, `git merge <branch-to-merge>` command is used. The **HEAD** pointer determins into which **branch** to merge into. This can be done by switchin (**checkout**) to desired **branch**.

> If **working directory** or **staging area** has *uncommitted* changes that conflict with the branch being checking out, Git will not switch **branches**. 

**Fast-forward** merging happens when merging one **commit** with a **commit** that can be reached by following the first **commit**’s history, **Git** moves the pointer forward because there is no divergent work to **merge** together.

**Three-way** merging happens when the **commit** on the working **branch** is not a direct ancestor of the **branch** merging in, using the two *snapshots* pointed to by the **branch** tips and the common ancestor of the two.

Once the **brancd** is **merged**, it can be deleted.  
`git branch -d <branch-name>`

## Merge Conflicts
If changed the same part of the same file changed  differently in the two **branches** being merged, **Git** will not be able to **merge** them.

`git status` command can show which are the conflicts. Using the `git add <file-name>` command marks the conflicted file as resolved.

> `git mergetool` command launchs a graphical tool to sort the conflict.

## Branch Management
To list all **branches**, command `git branch` can be used. **Branch** with * character prefixes, indicates the current **checked out branch**.

To see the last commit on each branch, run `git branch -v` command.

Options `--merged` and `--no-merged` can filter the list to **branches**, that have or have not yet **merged** into the current **branch**. **Merged** **branches** can be safely deleted. While trying to delete **unmerged** **branch** will fail:

> For force deletion of **unmerged** **branch**, use `git delete -D <branch-name>` command.

To check the **merge** state with respect to some other **branch** without **checking** that other **branch** out first.

*Example*

> what is not **merged** into the master **branch**?  
`git checkout testing`  
`git branch --no-merged master`  

# Branching Workflows
## Long-Running Branches
Having several **branches** that are always open and used for different stages of development cycle; and can **merge** regularly from some of them into others.

Each **Long-Running Branch** is a work silos, where sets of **commits** graduate to a more stable silo when they’re fully tested.

## Topic Branches
It is a **short-lived** branch that is created and used for a single particular feature or related work. It allows to context-switch quickly and completely, as the work is separated into silos where all the changes in that **branch** have to do with that topic. Changes can be kept there for minutes, days, or months, and **merge** them in when they’re ready, regardless of the order in which they were created or worked on.

> Creating **branches** and **merging** are completely local.

# Remote Branches
**Remote references** are references (*pointers*) in a remote repositories, including **branches**, **tags**, and more. `git ls-remote <remote>`, or `git remote show <remote>` list all **remote references**. 

**Remote-tracking** **branches** are references to the state of remote **branches**. They’re local references that cannot be moved; **Git** moves them whenever there is any network communication, to make sure they accurately represent the state of the remote repository. 

**Remote-tracking** **branches** take the form of `<remote>/<branch>`. 

To synchronize work, `git fetch <remote>` commandlooks up which server [**remote**] is, fetches any data from it that don’t exists locally, and updates local database, moving [**remote**]/master pointer to its new, more up-to-date position.

## Pushing
Local **branches** are not automatically synchronized to the remotes with write access. It needs to explicitly **push** the **branches**. `git push <remote> <branch>` command **push** a specific **branch** to a specific **remote**. Running `git push <remote> <branch>:<diff-branch>` command will do the same, except the remote **branch** will not be named the same as the local one.

> **Pushing** over HTTPS, **Git** server will ask for username and password for authentication for every **push**. To avoid this setup *credential cache*, `git config --global credential.helper cache`.

The `fetch` command fetches only **remote/branch** pointer that cannot be modified.

To be able to work on a remote/branch, it needs to be merged into current working branch, `git merge <remote>/<branch>`.

To base (*start*) a local **branch** off a remote-tracking **branch**, simply with this command

`git checkout -b <branch> <remote>/<branch>`

## Tracking Branches
**Checking** out a local **branch** from a **remote-tracking branch** automatically creates a **tracking branch**, and the **branch** it tracks is  an **upstream branch**. 

**Tracking branches** are local **branches** that have a direct relationship to a **remote branch**. This allows `git pull`, to automatically knows which server (**remote**) to **fetch** from and which **branch** to merge in.

Running `git checkout -b <branch> <remote>/<branch>`. or the shorthanded command `git checkout --track <remote>/<branch>` will create a branch that is set up to track `<remote>/<branch>` 

If the **local branch** name to **checkout** does not exist and exactly matches a name on only one remote, **Git** will create a tracking branch using a shortcut command; `git checkout <branch>`.

To set a **remote branch** to an existing **local branch**, or to change the **upstream branch** tracked, the `-u` or `--set-upstream-to` option explicitly used.

`git branch -u <remote>/<branch>`
Branch serverfix set up to track remote branch serverfix from origin.
Note
Upstream shorthand
When you have a tracking branch set up, you can reference its upstream branch with the @{upstream} or @{u} shorthand. So if you’re on the master branch and it’s tracking origin/master, you can say something like git merge @{u} instead of git merge origin/master if you wish.

To list **tracking branches** set up, The option `-vv` is used with `git branch` command. It will  list out **local branches** with more information including what each **branch** is tracking and if  **local branch** is *ahead*, *behind*, or *both*; since last **fetch**. 

> For better totally up to date ahead and behind numbers, run `git fetch --all` before `git branch -vv`.

## Pulling
While the git fetch command will fetch down all the changes on the server that you don’t have yet, it will not modify your working directory at all. It will simply get the data for you and let you merge it yourself. However, there is a command called git pull which is essentially a git fetch immediately followed by a git merge in most cases. If you have a tracking branch set up as demonstrated in the last section, either by explicitly setting it or by having it created for you by the clone or checkout commands, git pull will look up what server and branch your current branch is tracking, fetch from that server and then try to merge in that remote branch.

Generally it’s better to simply use the fetch and merge commands explicitly as the magic of git pull can often be confusing.

## Deleting Remote Branches
Suppose you’re done with a remote branch – say you and your collaborators are finished with a feature and have merged it into your remote’s master branch (or whatever branch your stable codeline is in). You can delete a remote branch using the --delete option to git push. If you want to delete your serverfix branch from the server, you run the following:

`$ git push origin --delete serverfix`  
To https://github.com/schacon/simplegit

 - [deleted]         serverfix

Basically all this does is remove the pointer from the server. The Git server will generally keep the data there for a while until a garbage collection runs, so if it was accidentally deleted, it’s often easy to recover.
