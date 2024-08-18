# Qemu with Busybox

## Build command
```shell
# Build qemu
$ sudo apt-get install libglib2.0-dev ninja-build
$ git clone git@github.com:qemu/qemu.git && cd qemu
$ ./configure --target-list=aarch64-softmmu --enable-virtfs
$ make
$ sudo make install

# Build linux-kernel
$ sudo apt install flex bison libssl-dev
$ git clone --depth=10 https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git && cd linux
$ make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- defconfig
$ make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- -j4

# BUild busybox
# open the CONFIG_STATIC in .config
$ git clone https://git.busybox.net/busybox cd busybox
$ CROSS_COMPILE=aarch64-linux-gnu- make defconfig
$ CROSS_COMPILE=aarch64-linux-gnu- make -j4
$ CROSS_COMPILE=aarch64-linux-gnu- make CONFIG_PREFIX='rootfs' install
$ cd rootfs
$ mv linuxrc init
$ mkdir -p proc sys dev etc/init.d && cd etc/init.d
$ vim rcS
##### in rcS ####

#!/bin/sh
mount -t proc none /proc
mount -t sysfs none /sys
mount -t devtmpfs none /dev
mount -t debugfs none /sys/kernel/debug

#################

$ sudo chmod 777 rcS
```

## Build image
```shell
$ cd 
$ dd if=/dev/zero of=root.img bs=1M count=32
$ mkfs.ext4 -F root.img
$ mkdir tmpdir
$ sudo mount -o loop root.img tmpdir/
$ cd tmpdir/
$ sudo cp -a ../busybox/rootfs/. .
$ cd ..
$ sudo umount tmpdir

```


