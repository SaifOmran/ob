# Chapter 15 - Storage
## Storage naming
- Storage may be attached disks, USBs, CD-ROM, SAN storage.
- Storage naming:
	- If the storage SATA, SCSI, USB -> system will name it as `sda` for first disk and `sdb` for the second disk and so on and all storage devices will be located in `/dev` , and each disk can be partitioned to number of partitions, first partition for `sda1` will be named sda1 and second partition will be named `sda2` and so on.
	- If the storage IDE -> system will name it as `hda` for first disk and `hdb` for the second disk and so on.
	- If the storage Para virtualized -> system will name it as `vda`  for first disk and `vdb` for the second disk and so on.
	- If the storage nvme-> system will name it as `nvme0n1`  for first disk in namespace 1 and `nvme1n1` for the second disk in namespace 1 and so on, and the first partition for `nvme0n1` will be `nvme0n1p1`
## Storage partitioning styles
- ![[Pasted image 20251230183821.png]]
### Master Boot Record (MBR)
- Partition style where the MBR info are stored in the first sector of the hard disk.
- It uses 32-bit addressing, so it has $2^{32}$ sector and the sector size = 512 Byte so, the maximum size for partition is 2TB.
- The first sector divided into:
	- 446 byte for boot code.
	- 2 byte for magic number which is used for checking the corruption
	- 64 byte for the partition table (PT), the entry size in the PT = 16 byte, that is why the maximum number of partitions = 4
		- The partition table contains 4 columns which are the partition number, start sector, last sector and flags (those are the 16 byte).
- As we can only make 4 partitions, we can make 3 primary and 1 extended and the extended can be divided into infinite number of logical partitions.
### GUID Partition Table (GPT)
- uses a modern standard for organizing storage devices, supporting large drives (over 2TB) and many partitions, replacing the older MBR system. It's linked with UEFI firmware, offering faster boot times and better security features like Secure Boot, essential for modern operating systems like Windows 10/11. GPT uses GUIDs (Globally Unique Identifiers) for partition locations and includes backup headers for data integrity, making it more robust than MBR.
- The partition table = 16K and the entry is 128 byte so, it can support 128 partition.
- It uses 64-bit addressing so, $2^{64}$ sector  and the sector size = 512 byte, so it can support 9.4ZB for partition.
### Steps to configure the storage
- After attaching new storage to the system, we need to configure it
- Sometimes the new SCSI disks don't appear after the attachment, in this situation you can reboot the system or use `echo "- - -" > /sys/class/scsi_host/host2/scan` to avoid rebooting the system and the downtime
>There are 3 host files host1 host2 host3, so ensure to scan them all.
1. Create new partition use `fdisk [device_path]` -> fdisk /dev/sdba
	- By default it would be MBR.
	- `o` -> to use MBR partitioning style.
	- `g` -> to use GPT partitioning style.
	- `n` -> to add new partition.
	- `p` -> primary (in case of MBR).
	- `e` -> extended (in case of MBR).
	- `d` -> delete partition (unmount the partition before deletion).
	- `L` -> show types of the partition.
	- `w` -> save.
2. Make filesystem on the partition using `mkfs -t [type of filesystem] [partition]` -> `mkfs -t ext4 /dev/sda1` or `mkfs.[type of FS] [prtition]` -> `mkfs.ext4 /dev/sdb5`.
3. Mount the filesystem to mount point using `mount [filesystem partiton] [mount point]`
	-> `mount /dev/sda1 /mnt/mydata`.
	>This is a temporary mounting which will be removed after rebooting.
4. you can unmount the partition using `umount [mount point]` -> `umount /mnt/mydata`
5. To make the mounting persistent we have to make the mounting in */etc/fstab* file.
	- we can add entry to this file by 3 ways:
		1. Using the device name-> /dev/sda1           /mnt/mydata    ext4    defaults    0    0
		>Not recommended as the device names can change as it depends on the hardware detection
		2. Using UUID ( `blkid` to get it)->UUID=*value*    /mnt/mydata  ext4   defaults   0    0
		3. Using LABEL (`blkid` to get it)->LABEL=*text*  /mnt/mydata  ext4    defaults   0    0
			>Create label for the filesystem using `e2label [partition] [label]`
		4. Use `mount -av` to mount all filesystems in the */etc/fstab* to their mount points

- To show the blocks devices on system `lsblk`
	- `lsblk [device path]` ->show specific device
	- `lsblk -l` -> show flat list instead of the tree
	- `lsblk -f` -> show filesystem, UUID and LABEL
- To show the mounted filesystems `df -hT` h for human-readable size and T to show filesystem type
-  To modify the partition size we have to delete and create it again with the same starting sector, and we have to resize the filesystem if it supports resizing.
- We resize the filesystem using `resize2fs [partition with FS]` .
- If you deleted the partition without unmounting it, the OS may be not feel this deletion and to test that you can see the partitions in */proc/partitions* and you will see deleted partitions, so you have to use `partprobe [device path]` to inform the OS that the partition table is changed, if you made unmounting before the deletion you wouldn't need to make this process.
- The extended partition can't have a filesystem as it is just container for the logical partitions that can have.
- While making the permanent mounting in */etc/fstab*, we may forget the syntax, we can solve this problem by mounting the filesystem using `mount` command, then we dispaly */etc/mtab* file which containing the manually mounted filesystems and copy the line we need and finally paste it on the */etc/fstab* file.
>If there any error in the */etc/fstab* file this will make the system enter the emergency mode while booting up.

==After any modification in the */etc/fstab* file we have to use `systemctl daemon-reload`.

---
### Managing swap space
- The swap space is specified based on the memory size
- We need to increase the swap space, we can do this by 2 methods:
	1. Creating new partition (if there free space).
		1. Create partition using `fdisk [device path]` and change its type to swap 82 using `t`.
		2. Make the filesystem using `mkswap [partiton]`.
		3. Enable swap on the partition using `swapon [partition]` (temporary mounting).
		4. Make persistent mounting in */etc/fstab* -> UUID    swap    swap     defaults   0   0 
		5. Verify the persistent mounting using `swapon -av`.
		6. Check the free space of the swap using `free -m` or `swapon -s`.
	2. Creating swap file (if there is no chance to create new partition).
		1. Create file with specific size ->`dd  if=/dev/zero  of=/swapfile1  bs=1M  count=1024`
		2.  Make the filesystem using `mkswap /swapfile1`.
		3. As this file is very important we secure it using `chmod 600 /swapfile1`.
		4. Make persistent mounting in */etc/fstab* -> /swapfile1    swap    swap     defaults   0   0 .
		5. Verify the persistent mounting using `swapon -av`.
		6. Check the free space of the swap using `free -m` or `swapon -s`.
---
### Scenario of overwriting MBR partition table info
![[Pasted image 20260101184735.png]]

---
### Number of inode and its effect on creating files and directories
#### What is an inode?
An inode is a data structure in Linux filesystems (like ext4) that stores metadata about a file or directory, such as:
- File type (file, directory, link, etc.).
- Permissions.
- Owner & group.
- Size.
- Timestamps.
- Pointers to data blocks.
- Every file and every directory consumes exactly ONE inode.
#### Number of inodes
- The total number of inodes is fixed at filesystem creation 
- ==Default block size = 4 KB==
- ==Default ratio: 1 inode per 16 KB==
- We can change the number of inodes using `mkfs.ext4 -i [bytes-per-inode]`.
- ==To calculate the number of inodes -> Number of inode = partition size / bytes per inode==
- To check the number of inodes we use `df -i`
---
