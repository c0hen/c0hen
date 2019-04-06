---
layout: default
title: Useful commands and scripts
description: Useful command examples and scripts to modify to your liking.
tags: command shell script
---

* Table of contents
{:toc}

#### ncdu equivalent:

For when [ncdu](https://dev.yorhel.nl/ncdu) is not installed.
```sh
du -d1 -h | sort -h
```

#### find and play videos flexibly

Using [mpv](https://mpv.io/), my preferred video player.

Create a shell script with below contents, pass arguments:

1st argument - how long ago was the video file changed.

The rest of the arguments: path(s) to look for videos (glob, like 
```sh
/media/videos/201*
```
can be used as argument).

```sh
#!/bin/sh
find -L "${@:2}" \( -iname '*.mp4' -o -iname '*.webm' -o -iname '*.avi' \) -a -ctime -"$1" -print0 | xargs -0 mpv 
```

#### Rude wget robot to recursively download a site

```sh
#!/bin/sh
# shows the correct script path even if called from another script
if [ -z "$1" -o -z "$2" ]; then
  echo "Usage: $(basename $(readlink -nf $0)) site_to_download_recursively destination_directory" 
  exit 1
else
  wget --continue --recursive --execute robots=off --wait 1 --directory-prefix="$2" "$1"
  exit 0
fi
```

#### Resize pictures
Requires [ImageMagick](https://www.imagemagick.org/)
Resize .jpg pictures in current directory level to roughly 1500x2000 pixels in size.
```sh
mogrify -resize 1500x2000 *.{jpg,JPG}
```
