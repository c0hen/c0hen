---
layout: default
title: Useful commands
description: Useful command examples
tags: command shell
---

* Table of contents
{:toc}

### ncdu equivalent:

For when [ncdu](https://dev.yorhel.nl/ncdu) is not installed.
```sh
du -d1 -h | sort -h
```

#### find and play videos flexibly

Create a shell script with below contents, pass arguments:
1st argument - how long ago was the video file changed.
The rest of the arguments: path(s) to look for videos.
```sh
#!/bin/sh
find -L "${@:2}" \( -iname '*.mp4' -o -iname '*.webm' -o -iname '*.avi' \) -a -ctime -"$1" -print0 | xargs -0 mpv 
```
