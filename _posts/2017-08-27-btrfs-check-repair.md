---
layout: post
title: "Find and delete corrupted files on btrfs"
---
Some days ago my laptop overheated and then turned off. I booted it up again, opened Chromium and got the "cannot load profile" error.
I tried the usual "delete & resync" approach, but it didn't work. One file got corrupted due to the sudden shutdown and I wasn't able to delete it.

In order to fix this issue, I applied the following steps:
1. booted-up a linux live distro
2. `lsblk` to find my `/home` partition (`/dev/sdb2` in this case)
3. `cryptsetup luksOpen /dev/sdb2`
4. `btrfs check /dev/mapper/lvmvg-root`
5. `btrfs check /dev/mapper/lvmvg-home`
6. `btrfs check --repair /dev/mapper/lvmvg-root`
7. `btrfs check --repair /dev/mapper/lvmvg-home`. That one didn't succeed because of a corrupted file. `btrfs check` gave me the inode of the corrupted file, so the only thing left was to find it and delete it.
8. `mount /dev/mapper/lvmvg-home /mnt`
9. `find /mnt -inum XXXXX`
10. `rm -f /mnt/DAMAGED_FILE`
11. `umount /mnt`
12. `btrfs check --repair /dev/mapper/lvmvg-home`
13. `cryptSetup luksClose /dev/sdb2`
14. `shutdown -r now`

