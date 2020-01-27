---
layout: default
title: Useful commands and scripts
description: Useful command examples and scripts to modify to your liking.
tags: command shell script
---

**Some here are scripts, those begin with a shebang**
```sh
#!
```

* Table of contents
{:toc}

#### ncdu equivalent

For when [ncdu](https://dev.yorhel.nl/ncdu) is not installed.
```sh
du -d1 -h | sort -h
```

#### Faster alternatives written in Rust

fd - alternative to find.
debian package name fd-find, executable name on debian is
```sh
fdfind
```
ripgrep - alternative to grep.
debian package name ripgrep.
```sh
rg
```

#### pass

Unix style password management script, encrypts with gpg.

#### find and play videos flexibly

Using [mpv](https://mpv.io/), my preferred video player.

To use as is, create a shell script with below contents, arguments:

1st argument - how long ago was the video file last changed.

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
  # Below line for offline mirroring, test on each site.
  # wget  --mirror --convert-links --adjust-extension --page-requisites --no-parent --continue --recursive --execute robots=off --wait 1 --directory-prefix="$2" "$1"
  exit 0
fi
```

#### ImageMagick
Requires [ImageMagick](https://www.imagemagick.org/)

Resize .jpg pictures in current directory level to roughly 1500x2000 pixels in size.
```sh
mogrify -resize 1500x2000 *.{jpg,JPG}
```

Create a black background in required size (for example, to use in a video).
```sh
convert -size 1920x1080 xc:black bg.png
```

#### [FFMpeg](https://ffmpeg.org/)

Dump audio using [parallel](https://www.gnu.org/software/parallel/). Parallel uses 1 thread for each core by default.

Make sure to change audio format according to source.
```sh
parallel ffmpeg -i '{}' -map 0:1 -c:a copy '{.}.m4a' ::: /media/video/source_video_file.mkv
```
