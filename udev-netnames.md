---
layout: default
title: Udev network device names
---

Udev network device names by default change to BIOS/UEFI location based names in Debian Stretch.

Read Debian's documents and if you depend on device names staying the same (firewall, scripts etc.), you can follow the advice to set up /etc/udev/rules.d/76-netnames.rules and then (re)move /lib/systemd/network/99-default.link if you have it (in case you upgraded).

```sh
/usr/share/doc/udev/README.Debian 
```
