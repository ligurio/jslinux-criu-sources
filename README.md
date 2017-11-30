# jslinux-criu

[CRIU](http://criu.org/) is a project to implement checkpoint/restore
functionality for Linux in userspace.

[jslinux](http://bellard.org/jslinux/) is Javascript PC Emulator written by Fabrice Bellard.

This README describes how to setup demonstration stand with CRIU inside jslinux.

#### Enable EPEL repository

```
wget http://download.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm
rpm -ivh epel-release-6-8.noarch.rpm
```

yum install -y protobuf protobuf-c  protobuf-c-devel  protobuf-compiler protobuf-devel protobuf-python

#### Build a kernel

CRIU project supports Linux kernel since [version 3.3](http://criu.org/Upstream_kernel_commits).
It is required to build our own kernel.


Download Linux kernel source code:

```
cd /usr/src/kernels/
curl -O https://www.kernel.org/pub/linux/kernel/v4.0/linux-4.0.4.tar.gz
```

Download patch with implementation of serial device.
Original patch is in tarball from [Bellard's site](http://bellard.org/jslinux/linuxstart-20120111.tar.gz)
and also there is a [patch](http://sourceforge.net/projects/toysbox/files/jsPCemulator_haveFun/)
for Linux kernel 3.0.3.

```
patch -p1 < linux-3.0.3-jsPcEmulator.patch 
patch -p1 < jslinux.patch
make config-3.0.3jspcemul
make
cd linuxstart-20120111
make all
make kernel
```

### HDD

The disk image contains a filesystem generated with Buildroot using BusyBox.
We should setup [buildroot](http://buildroot.uclibc.org/download.html)
and create HDD with CRIU binary and other utilities inside.

```
wget http://buildroot.uclibc.org/downloads/buildroot-2015.02.tar.gz
tar xzvf buildroot-2015.02.tar.gz
```

Install [required packages](http://buildroot.uclibc.org/downloads/manual/manual.chunked/ch02.html):

```
yum install binutils bzip2 gawk gcc gcc-c++ gettext make ncurses-devel patch unzip wget zlib-devel flex git-core bison
```

```
make menuconfig
```

You can use default buildroot config with small changes:

- add file, tmux, gcc, make utilities
- switch to using glibc

Run compilation:

```
make
```

Create disk image

```
hda_helpers.sh mount-new
```

Unpack rootfs

```
sudo tar -C <MOUNTPOINT_OF_IMAGE> -xf images/rootfs.tar
```

Prepare demonstration files -
we can use demo with checkpoint of [simple program with loop](http://criu.org/Simple_loop).

### TODO

* Port Bellard's patches to latest linux kernel (4.0.4+)
* Make an OpenVZ demo

### See also

* [LXC demo](https://linuxcontainers.org/lxd/try-it/)
* [Alpine Linux demo](http://xming.github.io/alpine-v86/demo2/coldboot.html)
* [JSLinux rewritten to be human readable](https://github.com/levskaya/jslinux-deobfuscated)
* [Docker Tutorial](https://www.docker.com/tryit/#1)
* [Ubuntu Tour](http://www.ubuntu.com/tour/en/)
* [Rust language playground](https://play.rust-lang.org/)
* [musl libc demo](http://www.openwall.com/lists/musl/2014/03/15/3)
* [ToysBox](http://sourceforge.net/projects/toysbox/files/jsPCemulator_haveFun/)
* [Linux kernel config 3.7.0](https://github.com/kmacrow/Vespen/blob/master/linuxstart-20120111/linux-3.7.0-rc3-config)
* [Virtual x86](http://copy.sh/v86/)
* [Online OR1K Emulator running Linux](http://jor1k.com)
* [Javascript emulator for the LatticeMico platform](https://github.com/ubercomp/jslm32)
* [ANGEL - Browser-based RISC-V ISA simulator](http://riscv.org/angel/)
