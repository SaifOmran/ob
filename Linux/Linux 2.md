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
