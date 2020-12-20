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

## Booting in qemu
qemu can also be used, as it includes support for virtio devices. As qemu includes an Arm emulator, this works (with reduced performance) even on non-Arm platforms.

```
tar xvjf debian-rootfs-aarch64.tar.bz2
qemu-system-aarch64 -M virt -cpu cortex-a53 -hda debian-rootfs-aarch64.img -kernel vmlinuz-* -initrd initrd.img-* -append 'root=/dev/vda1 ro nosplash' -nographic -m 2048
```

## Hints

### Enlarging the root filesystem
With the VM stopped, first increase the size of the disk image file (20GB in this example):

```
$ dd if=/dev/zero of=debian-rootfs-aarch64.img bs=1g count=0 seek=20
```

Inside the VM, assuming the first virtual disk contains the root filesystem, resize the partition to fill the entire disk image:

```
# apt install parted
# parted /dev/vda
GNU Parted 3.2
Using /dev/vda
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) resizepart
Warning: Not all of the space available to /dev/vda appears to be used, you can
fix the GPT to use all of the space (an extra 20971520 blocks) or continue with
the current setting?
Fix/Ignore? Fix
Partition number? 1
Warning: Partition /dev/vda1 is being used. Are you sure you want to continue?
Yes/No? Yes
End?  [10.7GB]? 100%
(parted) quit
Information: You may need to update /etc/fstab.
```

Finally, resize the filesystem:

```
# resize2fs /dev/vda1
```

### Snapshots
APFS supports file cloning, which is useful for creating disk image 'snapshots'. Unchanged blocks are shared between clones, making efficient use of storage.

```
$ df -h .
Filesystem     Size   Used  Avail Capacity iused      ifree %iused  Mounted on
/dev/disk3s5  460Gi  148Gi  293Gi    34% 1095372 4826881148    0%   /System/Volumes/Data
$ cp -c debian-rootfs-aarch64.img debian-rootfs-aarch64-snapshot.img
$ df -h .
Filesystem     Size   Used  Avail Capacity iused      ifree %iused  Mounted on
/dev/disk3s5  460Gi  148Gi  293Gi    34% 1095373 4826881147    0%   /System/Volumes/Data
```
