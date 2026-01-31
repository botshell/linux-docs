List block devices
```sh
lsblk
```

Output info about filesystems.
```sh
lsblk -f [device...]
```

Displays the amount of space available on the file system containing each file name argument. If no file name is given, the space available on all currently mounted file systems is shown.
```sh
df -h [FILE]
```

List the partition tables for the specified devices and then exit.  If no devices are given, the devices mentioned in /proc/partitions (if this file exists) are used.
```sh
fdisk -l [device...]
```
Manipulate disk partition table (m for help)
```
fdisk [device]
```

> Create a new label
> - g  create a new empty GPT partition table
> - o  create a new empty MBR (DOS) partition table

> Generic
> - p  print the partition table
> - n  add a new partition
> - t  change a partition type (type L to list all)
> - d  delete a partition

> DOS (MBR)
> - a  toggle a bootable flag

> GPT
> - M  enter protective/hybrid MBR

> Save & Exit
> - w  write table to disk and exit
> - q  quit without saving changes

Format
```
apt install dosfstools -y
mkfs.vfat -F 32 /dev/vda1  # for uefi partition
mkfs.ext4 /dev/vda2  # for root partition
sudo blockdev --rereadpt /dev/vdb  # inform system fo flush partiton table, it will fail when partition is mountted 
sudo partx -u /dev/vdb #  can also type this to flush partiton table, it's useful when partition is mountted
echo 1 > /sys/block/sdb/device/rescan  #  if the above code don't work, try this, it inform system fo flush partiton table size
```

Mount
```
mkdir -p /mnt/vda1 /mnt/vda1 /mnt/iso
mount /dev/vda1 /mnt/vda1
mount /dev/vda2 /mnt/vda2
mount -t ext4 /dev/vda2 /mnt  # declare filesystem type in grub when drivers don't enabled

mount -o loop debian-13.2.0-amd64-netinst.iso /mnt/iso

umount /mnt/vda* /mnt/iso
```

# Partition Table Samples:
> DOS (MBR)
> MBR disk partition table sample (extended ≤ 1, primary + extended ≤ 4, extended cotains logical up to 128).
> After add a extended partition using all the remain space, it sometimes remind that **"All space for primary partitions is in use."** without showing **"Adding logical partition"** option when add a new partition as ioctl() delay re-reading altered partition table to sync disks. Just use `w` to write table to disk and exit, then re-enter into fdisk.

| Device    | Boot | Start     | End       | Sectors   | Size | Id   | Type            |
| :-------- | ---- | --------: | --------: | --------: | ---: | ---: | :-------------- |
| /dev/sda1 | *    | 2048      | 2099199   | 2097152   | 1G   | 83   | Linux           |
| /dev/sda2 |      | 2099200   | 268435455 | 266336256 | 127G | f    | W95 Ext'd (LBA) |
| /dev/sda5 |      | 2101248   | 268435455 | 266334208 | 127G | 8e   | Linux LVM       |

---

> GPT

| Device    | Start     | End       | Sectors   | Size | Type             |
| :-------- | --------: | --------: | --------: | ---: | :--------------  |
| /dev/sda1 | 2048      | 2099199   | 2097152   | 1G   | EFI System       |
| /dev/sda2 | 2099200   | 268435455 | 266336256 | 127G | Linux filesystem |
| /dev/sda3 | 2101248   | 268435455 | 266334208 | 127G | Linux LVM        |

| NAME                | FSTYPE      | FSVER    | FSAVAIL  | MOUNTPOINTS |
| :------------------ | :---------- | :------- | -------: | :---        |
| sda1                | vfat        | FAT32    | 965.3M   | /boot/efi   |
| sda2                | ext4        | 1.0      | 803.8M   | /boot       |
| sda3                | LVM2_member | LVM2 001 |          |             |
| ├─debian--vg-root   | ext4        | 1.0      | 114.4G   | /           |
| └─debian--vg-swap_1 | swap        | 1        |          | [SWAP]      |

| num | alias | type             |
| --- | ----- | ---------------- |
| 1   | uefi  | EFI System       |
| 20  | linux | Linux filesystem |
| 19  | swap  | Linux swap       |
| 42  | home  | Linux home       |
| 44  | lvm   | Linux LVM        |

---

get uuid
```
lsblk -f /dev/sdX
blkid
ls -l /dev/disk/by-uuid/
```
