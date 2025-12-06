---

---
# Day 1
- Data: collection of zeros and ones without meaning.
- information: collection of structured data that have meaning.
- structured data: have pattern like DB tables, semi structured: key and value like XML, Quasi structured: between structured and semi structured there is no pattern and no key and value but you can get info, unstructured: hard to see a pattern like video and PDF files.
- Tape was used for backup and archiving, but it is legacy now as it is expensive and slow.
- Difference between local cloud provider and the hyper scaler: 
	1- pay easily with local currency.
	2- Data protection, the data is stored on the same country. 
- private cloud: only accessed by the owner only, no matter where the infrastructure it is.
- Types of servers:
	- 1- Towered: like the case but with powerful resources.
	- 2- Rack mounted: each server is standalone with its network devices and storage also the cooling system, each server is managed by separate IP. 
	- 3- Blade: collection of small servers on the same enclosure can be managed by one IP.
- Virtual memory: some space of the hard disks is used as RAM.
- LVM: logical volume manager: collect hard disks on same pool called volume group, then we can assign any amount of storage to the servers without downtime.
- Types of file system base:
	- 1-Disk based: NTFS,FAT32,EXT3,EXT4
	- 2-Network based: Shared folder
	- 3-Virtual based: an abstraction layer that allows a system to interact with various underlying file systems in a uniform way, providing a single, consistent interface for applications.
- Bare metal hypervisor: allows more utilization than hosted as there is no main OS that uses the resources.
- Desktop virtualization: decouples a user's full desktop environment (OS, apps, data) from their physical device, hosting it on a central server or cloud, allowing access from anywhere on any device using thin client.
- legacy application: monoethnic app which acts as a one unit.
- modern application: uses microservices architecture, which is better for security and availability.
- Software-Defined Data Centre (SDDC) :An IT platform where every aspect of a data centre, including compute, storage, and networking, is virtualized and managed by software. It uses abstraction, resource pooling, and automation to deliver IT-as-a-service and infrastructure-as-a-service (IaaS).
- green field: building the infrastructure from scratch and taking the best component from best vendor
- brown field: there are existing components and I need to buy components that have compatibility with them.
- converged infrastructure: compute-network-storage, storage connected to server using SAN.
- Hyper-converged infrastructure: compute-network, storage built-in server which is faster.
>   still server pooling for the storage is existed
---
# Day 2
222-
- Storage box = dummy disks + controller.
- RAID 0: performance and utilization, but low level of protection.
- RAID 1: protection, but low level of utilization.
- RAID 1+0: low level of utilization.
- RAID 3 (stripping with dedicated parity): fault tolerance for one disk, but 1/3 of disk capacity is wasted for the parity.
> write penalty = 4.
> read old data, read old parity, write new parity, write new data.
- RAID 5 (stripping with distributed parity): instead of having whole disk for the parity, it is distributed among the disks that contain the data itself (slightly better from RAID 3 due to direct calculations)
- RAID 6(stripping with doubled parity): tolerance of 2 disk failures 
- RAID techniques are applied on RAID set which contains number of disks (7+1), 1 here refers to hot spare disk where the data is recovered on it and it becomes data disks in the RAID set.
- Types of RAID controller: 
	- 1-software 
	- 2-Hardware (preconfigured from the provider)
- block-based controller = front end + cache + back end
	- front end = front end port (connect between server and storage box) + front end controller (responsible for the encapsulation and de-encapsulation of SCSI commands and requests)
	- cache = increase performance as it maintains the most accessible data
	- back end = back end port (connected to the physical storage) + back end controller (responsible for the encapsulation and de-encapsulation of SCSI commands and requests + error detection)
- cache store techniques:
	- 1- MRU
	- 2- LRU
	- 3- pre fetch

- 