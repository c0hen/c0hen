---
layout: default
title: KVM
description: Kernel Virtual Machine and QEMU notes
tags: kvm qemu virsh
---

* Table of contents
{:toc}

## Kernel Virtual Machine (KVM) management

Graphical manager - virt-manager

## KVM bridged with physical machines

The most common configuration. Edit host's /etc/network/interfaces

```sh
#set up a bridge and give it a static ip
allow-hotplug br0
iface br0 inet static
        address 192.168.1.2
        netmask 255.255.255.0
        network 192.168.1.0
        broadcast 192.168.1.255
        gateway 192.168.1.1
        bridge_ports eth0
        bridge_stp off
        bridge_fd 0
        bridge_maxwait 0
        nameserver 8.8.8.8
        #post-up /etc/network/if-up.d/tcbr0.sh
```
and bring it up using ifup, in which case:

check that gateway is not mentioned twice in /etc/network/interfaces

careful, may need to flush host IP, routes, etc to bring up the bridge with ifup on debian.

```sh
ip addr flush dev eth0
ifup br0
```

Contents of my traffic shaping script tr_br0.sh, commented out in the above example interfaces file. Make sure to make it executable if planning to use it.
Using Fair Queueing Controlled Delay. Some reasoning behind this from the [Arch Linux wiki.](https://wiki.archlinux.org/index.php/advanced_traffic_control)
```sh
#!/bin/sh
#tc qdisc del root dev br0
#tc qdisc show dev br0
/sbin/tc qdisc add dev br0 root fq_codel
```

## virsh (debian package libvirt-clients)

### List VMs

```sh
virsh list --all
```

### For offline configuration

[Source](https://serverfault.com/questions/403561/change-amount-of-ram-and-cpu-cores-in-kvm)

#### edit the XML in VM config:
```sh
virsh edit vm_name
```

#### or individual, easily scriptable commands:

##### To increase the number of CPUs

```sh
virsh setvcpus vm_name vcpu_count --config --maximum
virsh setvcpus vm_name vcpu_count --config
```

##### To increase the memory size

```sh
virsh setmaxmem vm_name memsize --config
virsh setmem vm_name memsize --config
```

### For online configuration

You can set the vCPU and memory while the VM is running with \-\-current instead of \-\-config, but the new numbers have to be within the maximum values already set. You can not set these maximum numbers while the VM is running. You will have to shutdown the VM.
```sh
virsh shutdown vm_name
```
Change the max memory or CPU count and start it again.
```sh
virsh start vm_name
```

## Creating a VM

### vm-install

Menu driven CLI utility

### virsh

Use 
```sh
--location
```
to use 
```sh
--extra-args
```
or

use 
```sh
--cdrom=/home/isos/debian9.iso
```

```sh
# virt-install \
-n debian9 \
--description "VM with debian 9" \
--os-type=linux \
--os-variant=debian9 \
--ram=2048 \
--vcpus=2 \
--disk path=/var/lib/libvirt/images/debian9.img,bus=virtio,size=10 \
--graphics none \
--location http://httpredir.debian.org/debian/dists/stretch/main/installer-amd64/ \
--network bridge=br0 \
--console pty,target_type=serial \
--extra-args 'console=ttyS0,115200n8 serial'
```

### Clone a VM
```sh
virt-clone --connect=qemu://example.com/system -o vm_old_source -n vm_new_dest --auto-clone
```

## Connect to VM Console without network or X

To connect to the console of the virtual machine use the following command. You can use “ctrl + ]” to exit out of the VM console.

```sh
virsh console vm_name
```

Setting up access to a VM’s console is no different than a physical server, where you simply add the proper kernel boot parameters to the VM.

To add serial console on a Debian 9 VM (Grub2), edit

```sh
#/etc/default/grub
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0 console=tty0,115200n8 serial"
GRUB_TERMINAL=serial
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=115200 --word=8 --parity=no --stop=1"
```
run
```sh
update-grub
```
and reboot the VM.

Default console speed is 9600 baud


[Minicom](https://salsa.debian.org/minicom-team/minicom) is an easy to use serial communication program.

```sh
minicom -D /dev/ttyS0
```

## Info

### Virtual disk info
```sh
qemu-img info /var/lib/libvirt/images/disk.img
```

