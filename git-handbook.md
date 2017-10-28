---
layout: default
title: Git handbook
---

Thoroughly assemble your .gitignore - files excluded from the remote repository.
SSH keys can be used to access Github
<https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/>

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

#### Remove a file that recently became untracked in .gitignore from repo

```sh
$git rm --cached path/to/file
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
