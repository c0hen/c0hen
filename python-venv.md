---
layout: default
title: Python tips
description: Python-venv usage to install applications with all dependencies and assorted tips
tags: python beginner tips programming system distribution
---

## Recover a broken python installation (errors like: pycompile not found)

Might need to reinstall only python-minimal, reinstall other packages as needed.

```
apt-get install --reinstall python python-minimal python-setuptools
```

## Debian: use alternatives to set python version

1 is the lowest priority (last group on the line).

```
#update-alternatives --install /usr/bin/python python /usr/bin/python3 2
#update-alternatives --install /usr/bin/python python /usr/bin/python2 1
```

## Install applications using venv

Install 

```
python 
python-venv

```

Creation of the virtual environment:

```
$python3 -m venv django-venv
```

Create and end the session you'll be installing your app in:

```
$source bin/activate
$source bin/deactivate
```

Preparation in the session:

```
pip install --upgrade pip
```

Install your app:

```
pip install django
```

or 

```
pip install django~=1.10.4
```

for version matching.
