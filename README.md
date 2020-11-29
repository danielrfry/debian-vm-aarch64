#  Pre-built Debian VM for Apple Silicon
This repository contains pre-built kernel, initial ram disk and root filesystem images for Debian aarch64, suitable for running on Apple Silicon Macs using [toyvm](https://github.com/danielrfry/toyvm).

⚠️ **The root filesystem is 10GB in size after decompressing.**

```
$ bunzip2 debian-rootfs-aarch64.img.bz2
$ toyvm -k vmlinuz-4.19.152 -i initrd.img-4.19.152 -d debian-rootfs-aarch64.img 'console=hvc0 root=/dev/vda1 nosplash'
```

🔑 The root password is `password`. Change that, obviously.

The kernel is based on the stock Debian configuration, except that the required virtio devices are built-in rather than compiled as modules. It was built on a Raspberry Pi 4 running the beta 64-bit version of Raspberry Pi OS.

The root filesystem is a bare minimum installation of Debian created using `debootstrap`, with the only modifications being to add `fstab` entries for the root filesystem and swap file, configure the first virtual network interface, and install the custom kernel `.deb` packages.
