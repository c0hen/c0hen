---
layout: default
title: Git handbook
description: Git handbook for a begginner, by a git beginner.
tags: git beginner tips
---

* Table of contents
{:toc}

Thoroughly assemble your .gitignore - files excluded from the remote repository.
SSH keys can be used to access [Github](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)

**Try to use small changes/commits and concise messages.**

HEAD and branches, including master, can be pictured as labels on an object. Git commands make more sense when looking at git from the inside.
A good talk on git internals by [Michael Schwern at Linux.conf.au 2013](https://www.youtube.com/watch?v=eQFZ_MPTVpc).

### Terms

master - the first branch created by default with git init, not special
HEAD - the pointer to the branch you're currently on (have checked out)
commit - saved state with an ID consisting of a SHA-1 checksum of all information of the state. Immutable constant - repo breaks if this is changed. Git does not change history unless forced.
reference - a commit ID, label or tag

### Start or get a repository

```sh
$git init myrepo.git
$git clone https://github.com/c0hen/c0hen.github.io
```

### Very basic flow with remote:

git pull is git fetch && git merge

```sh
$git pull
$ls .
work.txt work.md work.tex
$git log
$git status
$touch legionkitteh
$git add .
$git status
$git git commit -a -m'Short commit message committing all changes'
$echo 'It's dangerous to go alone! Take this.' >> legionkitteh
$git add legionkitteh
$git commit --amend
$git status
$git push
```

```sh
$git checkout thing maybe_more_things
```
means "switch to", "make active in current context", for example get a file from the status of a commit Bravo and put it into the git directory as it was at the time of commit Bravo.

[Try and learn it on Github](https://try.github.io/levels/1/challenges/1)

#### Display commit messages

```sh
$git log
```

#### Visual representation of the repository with messages, show a graph

```sh
$git log --graph --decorate --all
```

#### Show diff of a file already added to be commited

```sh
$git diff --cached path/to/file
```

#### Remove a file that recently became untracked in .gitignore from repo

```sh
$git rm --cached path/to/file
```

#### Move mistakenly commited file back to staging area, don't remove local file

```sh
$git reset --soft HEAD^
$git reset HEAD path/to/unwanted_file
$git commit -c ORIG_HEAD
```

#### Get a copy of a file discarding changes since last $git add

```sh
$git checkout path/to/file
```

#### Squash 2 pushed commits

Using --force may overwrite refs other than the current branch, including local ones. man git push

```sh
$git rebase -i origin/master~1 master
$git push origin +master
```

#### Remove pushed commit from repo 

Resets local files in repo. --hard means check out the change in addition to reset.
Don't do this in public ones! Others may be using it already.

```sh
$git reset --hard 40digit_commit_id
$git push --force
```

#### Checkout a single file from remote

```sh
$git fetch
$git checkout origin/master -- path/to/file
```

The fetch will download all the recent changes, but it will not put it in your current checked out code (working area).

The checkout will update the working tree with the particular file from the downloaded changes (origin/master).

#### Checkout file deleted by previous commit

The deleting commit for checking out is retrieved by rev-list. The commit before it is referenced by ^ .

```sh
git checkout $(git rev-list -n 1 HEAD -- "path/file")^ -- "path/file"
```

#### Rebase when pulling and before pushing

```
$git pull --rebase
```

Take care to understand each rebase.
Rebasing allows to merge related commits and keep mistakes from littering the project's history.

```sh
$git rebase -i
$git push
```
To automatically rebase before a pull add this to git config:

```
[pull]
        rebase = true
```

#### Remove a manually deleted file from tree

```sh
$git ls-files --deleted -z | xargs -0 git rm
```

### Branches

### New feature testing workflow

#### Create a branch (still on master) and switch to it (on new branch).

```sh
$git branch social
$git checkout social
```

#### Merge changes from the master branch and run tests

```sh
$git merge master
```
Run your tests. Fix as necessary and retest until code ok. Add and commit as needed.

#### Commit branch to master

```sh
$git checkout master
$git merge social
```

#### Create a branch and switch to it

```sh
$git checkout -b social
```

#### List remote branches

```sh
$git branch -r
```

#### Fetch all branches from remote called origin

```sh
$git fetch origin
```

#### Pull all tracked branches

```sh
$git pull --all
```

#### Find upstream of all branches:

```sh
while read branch; do
  upstream=$(git rev-parse --abbrev-ref $branch@{upstream} 2>/dev/null)
  if [[ $? == 0 ]]; then
    echo $branch tracks $upstream
  else
    echo $branch has no upstream configured
  fi
done < <(git for-each-ref --format='%(refname:short)' refs/heads/*)
```

#### Delete a remote branch

```sh
$git push --delete origin my_branch
```

### Working with remote repositories

#### Check remote repository before fetching it

```sh
$git ls-remote remotename
```

#### Add git remote with SSH URL

Add an existing git remote, check that it was added by listing remotes.
```sh
$git remote add remotename gituser@secondary.server:/path/to/myrepo.git 
$git remote -v
```

#### Add git remote with SSH specified key login

Can be set per repository. See man git-config

```sh
$cd myrepo.git
$git config core.sshCommand "ssh -i ~/.ssh/id_git -F /dev/null"
```

#### Multiple remotes with multiple ssh keys in one repository - external transport example

Create a bare remote git repository and set up access via ssh, with a per-repo key.
Add write permissions by group for accountable sharing.

On the remote:
```sh
$aptitude install git
$adduser gituser
$addgroup gitgroup
$su gituser
$cd
$git --bare --shared=group init myrepo.git
$chgrp gitgroup myrepo.git
```

On the machine you have your current latest repo and plan to push from:
```sh
$cd myrepo.git
```
Allow external protocol, ssh transport with separate key.
Only from user initiated commands, not from git initiated automated commands.
See man git-config, one SSH key is simpler and allows avoiding configuring
the external ssh transport.
```sh
$git config protocol.allow.ext user
```
Copy your ssh key to the secondary server, test login.
```sh
$ssh-copy-id -i ~/.ssh/id_secondary gituser@secondary.server 
$ssh gituser@secondary.server
$exit
```
Add the remote with the short name "secondary" and ssh URL
```sh
$git remote add secondary 'ext::ssh -i ~/.ssh/id_secondary gituser@secondary.server %S /home/gituser/myrepo.git'
```
List remotes
```sh
$git remote -v
```
Push your up to date local repo to the remote secondary repo
```sh
$git push secondary
```
For further git configuration to allow remote checking via external transport etc, see man git-config.

