---
layout: default
title: Rsync and find trickery
tags: rsync find cron filesystems server lvm tips
---

### School: file, surveillance and log server

```sh
sudo crontab -l
# executed using sh
# m h  dom mon dow   command
30 2 * * 1,2,3,4,5,6 /root/tools/backup_all.sh
00 1 * * 1 /root/tools/samba_archive.sh
# delete surveillance older than a month
15 2 * * 1,2,3,4,5,6 /usr/bin/find /media/video/ -iname '*.mp4' -mtime +30 -exec rm {} \;
10 2 * * 1,2,3,4,5,6 /usr/bin/find /srv/sambashared/ -iname '*considered*harmful*.exe' -delete
# move samba class folder contents to next year
0 4 30 8 * /root/tools/move_sambashared.sh
```

```sh
sudo cat /root/tools/move_sambashared.sh
#!/bin/sh
# Move sambashared files and directories to the next year's partition, delete files of the graduated class.
# test changes:
#i=1; while [ $i -lt 13 ]; do mkdir -p "$i"class/empty; touch "$i"class/"$i"; ((i++)); done

rm -rf --one-file-system /srv/sambashared/12class/*
i=12
while [ $i -gt 1 ]
do 
    j=$i
    i=$((i-1))
    SRC=/srv/sambashared/"$i"class/
    DST=/srv/sambashared/"$j"class
    rsync -aAXv --remove-source-files "$SRC" "$DST" && \
    find "$SRC" -mindepth 1 -type d -empty -delete
done
```

```sh
$cat /etc/fstab
# /etc/fstab: static file system information.
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
/dev/mapper/root /               ext4    relatime,errors=remount-ro 0       1
# /boot was on /dev/sda2 during installation
UUID=2784575b-454e-116f-8384-f20d84e22e6f /boot           ext4    noatime         0       2
# /boot/efi was on /dev/sda3 during installation
UUID=32B4-7775  /boot/efi       vfat    umask=0077      0       1
/dev/mapper/home /home           ext4    defaults,relatime        0       2
LABEL=1class /srv/sambashared/1class            ext4    defaults,relatime,acl        0       2
LABEL=2class /srv/sambashared/2class            ext4    defaults,relatime,acl        0       2
LABEL=3class /srv/sambashared/3class            ext4    defaults,relatime,acl        0       2
LABEL=4class /srv/sambashared/4class            ext4    defaults,relatime,acl        0       2
LABEL=5class /srv/sambashared/5class            ext4    defaults,relatime,acl        0       2
LABEL=6class /srv/sambashared/6class            ext4    defaults,relatime,acl        0       2
LABEL=7class /srv/sambashared/7class            ext4    defaults,relatime,acl        0       2
LABEL=8class /srv/sambashared/8class            ext4    defaults,relatime,acl        0       2
LABEL=9class /srv/sambashared/9class            ext4    defaults,relatime,acl        0       2
LABEL=10class /srv/sambashared/10class            ext4    defaults,relatime,acl        0       2
LABEL=11class /srv/sambashared/11class            ext4    defaults,relatime,acl        0       2
LABEL=12class /srv/sambashared/12class            ext4    defaults,relatime,acl        0       2
/dev/mapper/usr /usr            ext4    defaults,relatime        0       2
/dev/mapper/var /var            ext4    defaults,relatime        0       2
LABEL=debcache /var/cache/squid-deb-proxy            ext4    defaults,relatime        0       2
LABEL=video /media/video          ext4    defaults,relatime        0       2
/dev/mapper/swap1 none            swap    sw              0       0
# 
LABEL=archive0 /media/archive0          ext4    defaults,relatime,noauto        0       0
LABEL=archive1 /media/archive1          ext4    defaults,relatime,noauto        0       0
```
