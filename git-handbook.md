---
layout: default
title: Git handbook
description: Git handbook for a begginner, by a git beginner.
tags: git beginner tips
---

* Table of contents
{:toc}

Thoroughly assemble your .gitignore - files excluded from the remote repository.
SSH keys can be used to access Github <https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/>

**Try to use small changes/commits and concise messages.**

### Basic flow:

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

[Try and learn it on Github](https://try.github.io/levels/1/challenges/1)

#### Display commit messages

```sh
$git log
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
git reset --soft HEAD^
git reset HEAD path/to/unwanted_file
git commit -c ORIG_HEAD
```

#### Get a copy of a file discarding changes since last $git add

```sh
$git checkout path/to/file
```

#### Remove pushed commit from repo 

Don't do this in public ones, others may be using it already

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

#### Rebase when pulling and before pushing

```
$git pull --rebase
```

To automatically rebase before a pull add this to git config:

```
[pull]
        rebase = true
```

Rebasing allows to merge related commits and keep mistakes from littering the project's history.

```sh
$git rebase -i
$git push
```

### Branches

#### Create a branch (still on master) and switch to it (on new branch).

```sh
$git branch social
$git checkout social
```

#### Create a branch and switch to it

```sh
$git checkout -b social
```

#### List remote branches

```sh
git branch -r
```

#### Fetch all branches from remote called origin

```sh
git fetch origin
```

#### Pull all tracked branches

```sh
git pull --all
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
