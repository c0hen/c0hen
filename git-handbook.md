---
layout: default
title: Git handbook
permalink: /git-handbook/
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

 Display commit messages:

```sh
$git log
```

 Remove a file that recently became untracked in .gitignore from repo:

```sh
$git rm --cached foo
```

 Get a copy of a file discarding changes since last $git add

```sh
$git checkout foo
```

 Remove pushed commit from repo 

Don't do this in public ones, others may be using it already

```sh
$git reset --hard 40digit_commit_id
$git push --force
```
