---
ID: 62382
post_title: UEFI Application development in OVMF
author: admin
post_excerpt: ""
layout: post
permalink: >
  http://3mdeb.kleder.co/uefi/uefi-application-development-in-ovmf/
published: true
post_date: 2015-11-21 11:18:52
tags: [ ]
categories:
  - UEFI
  - QEMU
  - OVMF
---
OVMF (*Open Virtual Machine Firmware*) is a project that aim is to enable UEFI support in various virutal machines. According to [whitepaper][1] various projects have interest in supporting OVFM ie. VirtualBox, Xen, BHyVe and of course QEMU. Why someone may be interested in OVMF ?

*   IMHO the most important reason is that OVMF give ability to develop UEFI applications without using real hardware. This speeds up development cycle by giving ability to start before hardware prototype arrive. There are also cases when hardware is available only remotely, dealing with remote setup is usually annoying, so part of development can be performed locally.

*   It also improve testing and debugging, because we have full control over execution environment internals. Error injection and corner case analysis can be ineffective in real world. OVMF and qemu open debugging to new levels.

*   It enables training and learning environment. Not every one can obtain UEFI PC and mess with it when there is risk of destroying sth.

*   Many other reason like ability to test/develop UEFI support for various OSes, simplified debugging, lack of dependencies on legacy address space and devices. This and other interesting reasons can be found in [Laszlo Ersek whitepaper][1]

What I want to show below is procedure for setting up development environment for UEFI applications and show how to compile and deploy `Hello World` application in that environment.

## QEMU

Let's use upstream version of QEMU:

`git clone https://github.com/qemu/qemu.git 
cd qemu
git submodule init
git submodule update
./configure --target-list=x86_64-softmmu
make -j$(nproc)`

### Prepare application image

Of course size depends on you application requirements. Usually 128MB is way too much.

`dd if=/dev/zero of=app.disk bs=1 count=1 seek=$(( (128 * 1024 * 1024) - 1))
sudo mkfs.vfat app.disk
cd ..`

## EDK2

First build UEFI firmware with shell:

`git clone https://github.com/tianocore/edk2.git
cd edk2
nice OvmfPkg/build.sh -a X64 -n $(nproc)
cp Build/OvmfX64/DEBUG_GCC4?/FV/OVMF.fd ovmf.flash`

Now we need some application that can be delivered over `app.disk` to virtual machine. There are couple examples of UEFI applications in `edk2` source code, but let's start with classical `Hello World`. Source code can be found in `MdeModulePkg/Application/HelloWorld`. This module is built in `MdeModulePkg` because of that we can built it as follows:

`source edksetup.sh
build -a X64 -p MdeModulePkg/MdeModulePkg.dsc -t GCC49 -n$(nproc)`

Let's put it into `app.disk`:

`mkdir mnt_app
cp ../qemu/app.disk .
sudo mount app.disk mnt_app
sudo cp Build/MdeModule/DEBUG_GCC49/X64/HelloWorld.efi mnt_app
sudo umount mnt_app`

## OVMF configuration

When we develop UEFI application best way would be to run OVMF to UEFI Shell for test purposes. This is not default option. How to change boot order so we every time will lend in shell ?

On booting screen hit `<Esc>`.

<a class="fancybox" rel="group" href="/assets/images/tiano_boot.png"><img src="/assets/images/tiano_boot.png" alt="" /></a>

You should be in main menu of UEFI setup:

<a class="fancybox" rel="group" href="/assets/images/uefi_setup.png"><img src="/assets/images/uefi_setup.png" alt="" /></a>

Go through `Boot Maintenance Manager -> Boot Options -> Change Boot Order`, select `Change Boot Order` and hit `<Enter>`. Then using `+/-` keys move `EFI Internal Shell` to the top.

<a class="fancybox" rel="group" href="/assets/images/boot_order.png"><img src="/assets/images/boot_order.png" alt="" /></a>

Do not forget to save configuration with `<F10>`.

## Run HelloWorld.efi

To boot OVMF I'm using Laszlo's slightly modified script which can be found [here][2].

After booting to UEFI Shell we should look something like this:

<a class="fancybox" rel="group" href="/assets/images/uefi_shell.png"><img src="/assets/images/uefi_shell.png" alt="" /></a>

Note `FS0:` in mapping table list - this is our `app.disk`.

Type:

`fs0:
HelloWorld.efi`

Result should look like this: <a class="fancybox" rel="group" href="/assets/images/hello_world.png"><img src="/assets/images/hello_world.png" alt="" /></a>

NOTE: If your result is different then make sure to check last lines of `app.ovmf.log` it can give you some hints.

## Summary

Hopefully at least part of above article was useful somehow. There are many ways to extend this sample presentation. In example application delivery can be achieved by network transfer. If you have any question or need UEFI support please do not bother to ping me in comments or using any other medium.

 [1]: http://www.linux-kvm.org/downloads/lersek/ovmf-whitepaper-c770f8c.txt
 [2]: https://raw.githubusercontent.com/pietrushnic/edk2/ovmf-helloworld/ovmf.sh