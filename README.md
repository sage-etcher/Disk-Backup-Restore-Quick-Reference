# Linux/Posix Hard Drive Backup/Restore Reference

## Index
 
  - [Environment](#environment)
    * [Arch Live ISO](#arch-live-iso)
  - [Partition Table](#partition-table)
  - [Boot Loader](#boot-loader)
    * [MBR/BIOS Boot (Legacy)](#mbrbios-boot-legacy)
    * [UEFI/ESP Boot Partition](#uefiesp-boot-partition)
  - [Partitions](#partitions)
    * [Logical Clone using PartClone](#logical-clone-using-partclone)
    * [Raw Clone using Unix dd](#raw-clone-using-unix-dd)
  - [License](#license-bsd-2-clause)

---

## Environment

The following expects a unix-like environment. all commands prefixed by a '$'
may be done by user, while any '#' prefixed are expected to be run as root.
Additionally, you are expected to have access to the following software: 
sfdisk, dd, PartClone.

### Arch Live ISO

  1. Confirm conection to internet.
  2. Update package database and keyring.
  3. Update installed packages.
  4. Clear package cache.
  5. Install recovery tools.

    $ ping google.com
    # pacman -Sy
    # pacman -S archlinux-keyring
    # pacman -Syu
    # pacman -Scc
    # pacman -S partclone sfdisk

---

## Partition Table

    # sfdisk -d /dev/sda > sda.partition.table.txt
    # sfdisk /dev/sda < sda.partition.table.txt

## Boot Loader

### MBR/BIOS Boot (Legacy)

    # dd if=/dev/sda of=sda.mbr.raw.img bs=446 count=1 status=progress
    # dd of=/dev/sda if=sda.mbr.raw.img bs=446 count=1 status=progress

### UEFI/ESP Boot Partition

Please see [Logical Clone using PartClone](#logical-clone-using-partclone)
on a FAT32 partition.

## Partitions

### Logical Clone using PartClone

PartClone supports most of the common file systems, a non-exauhstive list 
being: ntfs, exfat, fat32, ext4, btfs, ufs2, and hfs. PartClone backsup only 
the used sectors, making it fast and efficient, however that also means it can 
only be used on supported file systems.

    # partclone.ntfs -c -s /dev/sda3 -o sda3.partition.ntfs.img
    # partclone.ntfs -r -o /dev/sda3 -s sda3.partition.ntfs.img

### Raw Clone using Unix dd

Unix dd creates a raw 1:1 backup of the partition, including both used and 
unused sectors in the resulting image. Compared to PartClone, this proccess is 
much slower and produces significantly larger images.

This method is especially useful for "Windows Reserved" partitions, and other 
instances where the data is unknown or unformatted. It is also necessary in 
cases where the partition's filssystem is not supported by PartClone.

    # dd if=/dev/sda2 of=sda2.partition.raw.img bs=1M status=progress
    # dd of=/dev/sda2 if=sda2.partition.raw.img bs=1M status=progress

---

## License (BSD-2-Clause)

Copyright 2024 Sage I. Hendricks

Redistribution and use in source and binary forms, with or without 
modification, are permitted provided that the following conditions are met:

  1. Redistributions of source code must retain the above copyright notice, 
     this list of conditions and the following disclaimer.

  2. Redistributions in binary form must reproduce the above copyright notice, 
     this list of conditions and the following disclaimer in the documentation 
     and/or other materials provided with the distribution.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS “AS IS” AND 
ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED 
WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE 
DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE 
FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL 
DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR 
SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER 
CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, 
OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE 
OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
