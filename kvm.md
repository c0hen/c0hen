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

[//]: # (TODO: bridge adapter, network)

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

You can set the vCPU and memory while the VM is running with --current instead of --config, but the new numbers have to be within the maximum values already set. You can not set these maximum numbers while the VM is running. You will have to shutdown the VM.
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

Use --location to use --extra-args or
use --cdrom=/home/isos/debian9.iso

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
--network bridge:br0 \
--console pty,target_type=serial \
--extra-args 'console=ttyS0,115200n8 serial'
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


[Minicom](https://salsa.debian.org/minicom-team/minicom) is also an easy to use serial communication program.

```sh
minicom -D /dev/ttyS0
```
