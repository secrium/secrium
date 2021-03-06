---
title: "Git 101"
date: 2019-01-03T00:48:49+02:00
draft: false
description: "Introduction to version control with Git tool"
categories: ["Versioning"]
featuredImage: "img/git-101.jpg"
dropCap: true
displayInMenu: false
displayInList: true

resources: 
- src: "img/*.jpg"
---

# What is Git?
Git is Distributed Version Control System (**DVCS**). Where from security point of view, it provides:

- **Audit trail**, answering *who* did *what*, *why*, and *when*
- **Recovery**, allowing to revert to previous working state
- **Backup**, where each client has a full back of repository and its history
- **Integrity**, everything is check-summed with [SHA-1](https://en.wikipedia.org/wiki/SHA-1)
- **non-repudiation**, where every change has a valid owner
- **Separation of Duty**, where *author* introduces the change and *committer* accepts the change

It supports a non-linear development (**branches**), simple, very fast, and everything stored as **stream of snapshots**. Every snapshot has a reference, which has a copy of changed files and/or links to non-changed files.

# Git Structure
Git structure consist of three concepts

- **Git Directory**, stores the metadata and object database of a project. 
- **Working Tree**, a specific version checkout of project's files, pulled out from the compressed database in the Git directory and placed on disk for use.
- **Staging area**, a file hosting information about what will go into next commit.

## Git Workflow

- Files get modified in **working tree**
- Selected files added to **staging area** for next commit
- Commit create a snapshot of files in **staging area** and stores the snapshot permanently to **Git directory**

# Git Initial Setup
Install Git from [here](https://git-scm.com/downloads). Git is configured with `git config` command, with three different attributes:

- `--system`, applies settings to all users and repositories
- `--global`, applies settings to a specific user and user's reporsitories 
- `--local`, applies settings to a specific reporsitory 

> `--local` overrides `--global` & `--system`, where `--global` overrides `--system` 

## Setup Idenity
Done only once and used by every Git commit to track *who* did the changes

`git config --global user.name "Firs Last"`  
`git config --global user.email "first.last@example.com"`

## Setup Editor
Optionally, the default text editor used by Git messages can be changed.

`git config --global core.editor "path-to-executable"`

> To configure VSCode, this command can be used  
`git config --global core.editor "code --wait"`

## Check Settings
|Description|Command|
|---|---|
|To list all configurations (all scopes) settings|`git config --list`|  
|To check a specific key configuratio|`git config <key.pair>`|
|To query the origin key configuration's value|`git config --show-origin <key.pair>`|

# Starting with Git
A repo can be **initialized** or **cloned**.

## Initializing Repository
To turn a directory to a Git repository, run this command within the targeted directory.  
`git init`  

A new **.git** subdirectory get created, containing a Git repository skeleton.

## Cloning a Repository
Cloning makes a local copy of a remote Git repository (all data and its history). This is done via this command.  

**using HTTPS transfer protocol**  
`git clone https://site-name.com/user/repo-name`  
**using SSH transfer protocol**  
`git clone git@site-name.com:user/repo-name`

> Adding addtional argument with directory name at the end of the command, will clone the remote repository to that directory.

The command will:

- Create a directory called **repo-name**
- Initialize a **.git** directory inside **repo-name** directory
- **Pull** down all data of remote repository
- Check out the latest version of all files in **working tree**. 

> Files in **working tree** are in *unmodified* state and *tracked*.

## Git Status
Each file in working directory can be in one of two states:

- **Tracked**, file in the last snapshot, which can be:
    - *unmodified*
    - *modified*
    - *staged*
- **Untracked**, file not in the last snapshot and not in staging area

**Commands**  

- `git status` will show the files state
- `git add file-name` will track new file and add it to **staging** area. Or add a *modified* file to **staging** area (*staged*)  
- `git add .` will do the same as previous command for all files, except files in **.gitignore** file
- `git status -s` or `git status --short` will show status for each file in this format

|**staging area**|**working tree**|*meaning*|
|---|---|---|
|M||*modified* & *staged*|
||M|*modified* & **not** *staged*|
|M|M|*modified* & *staged* and *modifed* again|
|A||new file added (*staged*)|
|?|?|*untracked* file|

To view changes in files, not only the status, `git diff` command can be used shows the patch of change (the exact lines added and removed)

**Commands**  
`git diff` shows *unstaged* changes  
`git diff --staged` or `git diff --cached` shows *staged* changes to be commited 

## Ignoring Files
In some cases, some file should be excluded from being tracked by Git. Files with secrets or just log files. Ignored files or their pattern needs to be added to **.gitignore** file.  Setting up a **.gitignore** file in the begining avoids accidentally commit files that should not be Git repository.

A single **.gitignore** file in repository root directory, applies recursively to the entire repository. Additional **.gitignore** files can be created in subdirectories. The rules in these nested **.gitignore** files apply only to the files under the directory where they are located. 

**Patterns**

- Ignored lines
    - Blank lines
    - Comments lines starting with #
- Applied recursively throughout the entire working tree
    - Start with a forward slash (/) to avoid recursivity
    - End with a forward slash (/) to specify a directory
- Negate a pattern by starting it with an exclamation point (!)
- Glob patterns
    - An asterisk (*) matches zero or more characters
    - [abc] matches any character inside the brackets
    - A question mark (?) matches a single character
    - Brackets enclosing characters separated by a hyphen ([0-9]) matches any character between them
    - Two asterisks to match nested directories; a/**/z

*Example*

> \# ignore all .a files  
> *.a  
> \# but do track lib.a, even though you're ignoring .a files above  
> !lib.a  
> \# only ignore the TODO file in the current directory, not subdir/TODO  
> /TODO  
> \# ignore all files in the build/ directory  
> build/  
> \# ignore doc/notes.txt, but not doc/server/arch.txt  
> doc/*.txt  
> \# ignore all .pdf files in the doc/ directory and any of its subdirectories  
> doc/**/*.pdf  

## Committing Changes
Only files in staging area can be committed to **Git directory**, anything that is still unstaged will not be committed. 

`git commit` command launches default editor to add a title and description (separated by blank line) to the commit message.

Every commit save a snapshot to **Git directory**, which can revert to or compare to later.

> Adding the `-a` option to `git commit` command makes Git automatically stage every file that is already tracked before doing the commit.

> Adding the `-m "message text"` option to `git commit` command makes Git automatically add message to commit's titel without opening the default editor. Adding it twice, will count the seccond message as commit's descrition.

*Example*

> `git commit -a -m "title" -m "description"`

## Removing Files
File removal from **Git** is simply removing from **staging area**, followed by commit. this is done with `git rm` command, which also removes the file from working directory.

> `git rm -f filename` command, with force option, is used in case the file is *staged* and *modified*. This prevent accidental removal of data that hasn’t yet been recorded in a snapshot and cannot be recovered from Git.

To remove accidentally *staged* file from **staging area**, `--cached` option can be used

`git rm --cached filename`

> Files, directories, and file-glob patterns can be passed to `git rm` command. 

## Moving Files
**Git** is moving is equivalent to rename operation.

`git mv file_oldname file_newname`

Which can be done also by running

`mv file_oldname file_newname`  
`git rm file_oldname`  
`git add file_newnam`  

## Review Changes
`git log` is a power command to view the history of changes within a repository. The command output is in reverse chronological order and has SHA-1 checksum, the author’s name and email, the date written, and the commit message.

**Options**

|command options|description|
|---|---|
|`-p`, `--patch`|shows the difference (the patch output) introduced in each commit (*output: add a diff directly following each entry*)|
|`--stat`|lists abbreviated stats for each commit (*output: how many files were changed, and how many lines in those files were added and removed, a summary of the information at the end*)|
|`--shortstat`|display only the changed/insertions/deletions line from `--stat`|
|`--name-only`|list of files modified after the commit information|
|`--name-status`|list of files affected with added/modified/deleted information|
|`--abbrev-commit`|only the first few characters of the SHA-1 checksum instead of all 40|
|`--relative-date`|date in a relative format (*e.g, “1 month ago”*)|
|`--graph`|an ASCII graph of the branch and merge history beside the log output|
|`--pretty`|an alternate format (*options: oneline, short, full, fuller, and format (specify format)*)|
|`--oneline`|shorthand for `--pretty=oneline --abbrev-commit` used together|

### Limiting Log
`git log` has limiting options to show only a subset of commits.

**Options**

|Option|Description|
|---|---|
|`-<n>`|only the last n commits|
|`--since`, `--after`|commits made after the specified date|
|`--until`, `--before`|commits made before the specified date|
|`--author`|commits with specified author|
|`--committer`|commits with specified committer|
|`--grep`|commits with a commit message containing the string|
|`-S`|commits adding or removing code matching the string|
|`--no-merges`|prevent the display of merge commits|

> Using multiple instances `--author` and `--grep` limita the output to commits that match **any** of the `--author` patterns and any of the `--grep` patterns. Adding `--all-match` option limits the output to just those match all `--grep` patterns.

> The difference between *author* and *committer* is that the *author* is the person who originally wrote the work, whereas the *committer* is the person who last applied the work

> A path filter limit the log output to spcified directories or files. It is always the last option and preceded by double dashes (`--`) to separate the paths from the options.

*Example*

```bash
git log --pretty="%h - %s" --author=name --since="2017-10-01" --before="2017-11-01" |
--no-merges -- t/
```

## Undo
### Redoing Last Commit
Redo last commit with `--amend` option. The option replaces last commit with entirely a new. The old commit will not show up in repository history. 

To redo last commit with additional changes, changes need to be *staged* before then `git commit --amend`

If there is no changes since last commit, the new snapshot will be the same, only the commit message would be changed with the default editor.

### Unstaging a Staged File
If a file was *staged* by mistake, it can be unstaged via `git reset HEAD <file>` command. This command also visible in `git status` command as tip on how to unstage a *staged* file.

### Unmodifying a Modified File
To revert a *modified*  file to what it was in the last commit can be achived via this command `git checkout -- <file>...`, which will . Again, This command also visible in `git status` command as tip on how to discard changes in working directory for a *modified* file.

> File status will be changed to *unmodified* after command

# Remote Reporsitories
Remote repositories are versions of a Git Repositories that are hosted on the Internet or network, allowing collaboratiion with others.

## View Remotes
The command `git remote` lists the shortnames of each remote handle specified. Specifing the option `-v`,  shows the URLs that Git has stored for the shortname to be used when reading and writing to that remote.

> When a repository is cloned, there should be at least *origin*, the default name Git gives to the server of where cloned from.

*Example*  

> $ `git remote -v`  
origin	https://github.com/username/reponame (fetch)  
origin	https://github.com/username/reponame (push)  

## Adding Remote Repositories
To explicitly add a new remote Git repository as a shortname, this command will do the job
`git remote add <shortname> <url>`

> remote *shortname* makes **fetch**, **pull**, and **push** commands easier to work with, than full length *URL*.

## Fetching and Pulling 
To get data from remote repositories, two commands are used; **Fetch** and **Pull** you can run:

`git fetch <remote>` command only downloads the data to local repository. it doesn’t automatically merge it with any of local work or modify it. Merge can bedone manually.

`git pull <remote>` command fetch and then automatically merge that remote branch into local current branch.

> Current branch should be setup to track a remote branch, for **pull** to work.

## Pushing
To push newly **committed** work to a remote repository (*upstream*), this command can be used

`git push <remote> <branch>`

> For the **push** command to work, write access on on the remote repository should be valid. 

Inspecting a Remote

To see more information about a particular remote, `git remote show <remote>` command will do that for a particular shortname.

It lists the URL for the remote repository and the tracking branch information:

- Which branch is automatically pushed to with `git push` command while on certain branches
- Which remote branches on the server not yet exist locally
- Which remote branches exists locally that have been removed from the server
- Multiple local branches that are able to merge automatically with their remote-tracking branch when run `git pull` command

## Renaming and Removing Remotes
To renamed a remote's shortname, `git remote rename <old> <new>` command is used. It will change all  remote-tracking branch names, too. 

To remove a remote, run `git remote remove <remote-name>` command. Or shorter command `git remote rm (remote-name>`. All remote-tracking branches and configuration settings associated with removed remote are also deleted.

# Tagging
Tags are used as an important and specific points of a repository. 

Listing the available tags is done with `git tag` command with optional `-l` or `--list` options. The tags are listed in alphabetical order, where the order in is not important.

Passing particular pattern to `git tag -l` will return on tags that match the pattern.

*Example*

> $ git tag -l "v1.*"  
v1.0.1  
v1.1  

## Creating Tags
Git supports two types of tags:

- Lightweight
- Annotated

### Lightweight Tags
Similar to a **Branch** that is static, not changing. A simple pointer to a specific commit checksum saved in a file. Recommended as temporary tag.

Creating a lightweight tags is done by simple passing a tag name to `git tag` command

`git tag <tag-name>`

### Annotated Tags
A full copy of objects are stored in the **Git** database, along with checksum, taggers details, date, message and optionally signed and verified with GNU Privacy Guard (GPG). 

Creating an annotated tag is done be addtion the option `-a` with tag command.

`git tag -a <tag-name> -m "tag-message"`

To view a tag with its data (**commit**)

`git show <tag>`

## Tagging Later
In some cases, there might be need to take an old **commit**. It is possible to add a tag to a previous **commit**, by using the commit checksum (or part of it) at the end of the `git tag`.

*Example*

> $ git tag -a v1.0 8ec1202

## Sharing Tags
To **push** **tags** to remote server, it has to explicitly pushed, as done with branches.

`git push origin <tag-name>`

To **push** all **tags** in repository at once.

`git push origin --tags`

## Checking out Tags
To view the files of repository at specifc tag, `git checkout <tag-name>` comamnd is used.

> Repository goes into *detached HEAD* state, which has some bad side effects

In “detached HEAD” state, iAny changes *commited* in *detached HEAD* state, the tag will stay the same, but the new commit will not belong to any branch and will be unreachable, except by the exact commit hash.

To create a **branch** based on a **tag** witg this command:

`git checkout -b <branch-name> <tag-name>`

# Git Aliases
To make using **Git** experience simpler, aliases can be used to replace long **Git** commands with customizable and short ones.

*Examples* 

> `git config --global alias.unstage 'reset HEAD --'` replaces  
`git unstage <file>` with  
`git reset HEAD -- <file>`

> `git config --global alias.last 'log -1 HEAD'`  
lists last commit easily


To run an external command, rather than a Git subcommand, The command should start with **!** character. 

*Example*  

> `git config --global alias.visual '!gitk'
