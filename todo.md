---
title: Todo
layout: default
published: false
---

- [ ] _sitemap/assets/css/style.css included in site.pages

- [ ] kvm.md network.
bring up bridge manually ?? missing interface
```sh
ip link set up dev br0
ip route add default via 192.168.1.1 dev br0 onlink
```

or

virt-install --connect=qemu:///system \
	--name "$1" \
	--description "KVM $1" \
	--os-type=linux \
	--os-variant=debian9 \
	--ram=4096 \
	--vcpus=4 \
	--graphics none \
	--location http://httpredir.debian.org/debian/dists/stretch/main/installer-amd64/ \
	--network bridge=br0 \
	--initrd-inject=/home/diabloid/virt/preseed.cfg \
	--extra-args 'auto' \
	--disk=pool=virt,size=20,format=qcow2,cluster_size=2M,preallocation=metadata,bus=virtio
#	--extra-args 'console=ttyS0,115200n8 serial'
### The name preseed.cfg is mandatory for debian installer d-i to pick it up

