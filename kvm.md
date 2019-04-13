---
layout: default
title: KVM
description: Kernel Virtual Machine and QEMU notes
tags: kvm qemu virsh
---

* Table of contents
{:toc}

##Graphical manager - virt-manager

[//]: # (TODO: bridge adapter, network)

##virsh

[Source](https://serverfault.com/questions/403561/change-amount-of-ram-and-cpu-cores-in-kvm)

###For offline configuration

####edit the XML in VM config:
```sh
virsh edit vm_name
```

####or the sanity checked option:

#####To increase the number of CPUs

```sh
virsh setvcpus vm_name vcpu_count --config --maximum
virsh setvcpus vm_name vcpu_count --config
```

#####To increase the memory size

```sh
virsh setmaxmem vm_name memsize --config
virsh setmem vm_name memsize --config
```

###For online configuration

You can set the vCPU and memory while the VM is running with --current instead of --config, but the new numbers have to be within the maximum values already set. You can not set these maximum numbers while the VM is running. You will have to shutdown the VM.
```sh
virsh shutdown vm_name
```
Change the max memory or CPU count and start it again.
```sh
virsh start vm_name
```

