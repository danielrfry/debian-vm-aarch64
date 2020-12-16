#  Pre-built Debian VM for Apple Silicon
This repository contains pre-built kernel, initial ram disk and root filesystem images for Debian aarch64, suitable for running on Apple Silicon Macs using [toyvm](https://github.com/danielrfry/toyvm). The scripts used to build the VM (for Arm and Intel) are available at [debian-vm-build](https://github.com/danielrfry/debian-vm-build).

## Booting in toyvm
```
tar xvjf debian-rootfs-aarch64.tar.bz2
toyvm -k vmlinuz-* -i initrd.img-* -d debian-rootfs-aarch64.img 'console=hvc0 root=/dev/vda1 nosplash'
```

🔑 The root password is `password`. Change that, obviously.

The kernel is based on the stock Debian configuration, except that the required virtio devices are built-in rather than compiled as modules.

The root filesystem is a bare installation of Debian created using `debootstrap`, with minimal configuration changes to make it bootable and enable networking. See [debian-vm-build](https://github.com/danielrfry/debian-vm-build) for more details.
