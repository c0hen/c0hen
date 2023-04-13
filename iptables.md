---
layout: default
title: Iptables
description: Iptables cheatsheet
tags: iptables tips system
---

## Fully flush iptables

```sh
#/bin/sh
IPT=/usr/bin/iptables
$IPT -F
$IPT -X
$IPT -t security -F
$IPT -t security -Z
$IPT -t security -X
$IPT -t raw -F
$IPT -t raw -Z
$IPT -t raw -X
$IPT -t nat -F
$IPT -t nat -Z
$IPT -t nat -X
$IPT -t mangle -F
$IPT -t mangle -Z
$IPT -t mangle -X
$IPT -P INPUT ACCEPT
$IPT -P FORWARD ACCEPT
$IPT -P OUTPUT ACCEPT
```
