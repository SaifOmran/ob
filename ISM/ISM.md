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
page 222
## RAID techniques
- Storage box = dummy disks + controller.
- RAID 0: performance and utilization, but low level of protection.
- RAID 1: protection, but low level of utilization, it is not like a backup as the 2 disk are identical and any change in 1 instantly effects the other, so if any disk got ransomware the other would also, the backup concept means that there is a point in time where the data is copied on another disk.
- RAID 1+0: low level of utilization, require minimum 4 disks.
- RAID 3 (stripping with dedicated parity): fault tolerance for one disk, but 1/3 of disk capacity is wasted for the parity, typically not used in production environment.
	- write penalty = 4.
		- read old data, read old parity, write new parity, write new data.
- RAID 5 (stripping with distributed parity): instead of having whole disk for the parity, it is distributed among the disks that contain the data itself (slightly better from RAID 3 due to direct calculations as the data and parity are on the same disk)
- RAID 6(stripping with doubled parity): tolerance of 2 disk failures and commonly used.
- Host sparing: RAID techniques are applied on RAID set which contains number of disks (7+1), 1 here refers to hot spare disk where the data is recovered on it and it becomes data disks in the RAID set.
	- If parity RAID is used, the data is rebuilt onto the hot spare from the parity and the data on the surviving disk drives in the RAID set.
	- If mirroring is used, the data from the surviving mirror is used to copy the data onto the hot spare.
	- When a new disk drive is added to the system, data from the hot spare is copied to it. The hot spare returns to its idle state, ready to replace the next failed drive.
	- Alternatively, the hot spare replaces the failed disk drive permanently. This means that it is no longer a hot spare, and a new hot spare must be configured on the storage system.
- Types of RAID controller: 
	- 1-software.
	- 2-Hardware (preconfigured from the provider).
---
## Block-based storage system
- Block-based controller = front end + cache + back end
	- front end = front end port (connect between server and storage box) + front end controller (responsible for the encapsulation and de-encapsulation of SCSI commands and route data to and from cache through the internal data bus).
	- cache = increase performance as it maintains the most accessible data.
	- back end = back end port (connected to the physical storage) + back end controller (responsible for the encapsulation and de-encapsulation of SCSI commands and requests + error detection).
- cache management techniques:
	- 1- MRU (used when compute system reads the block for only one time).
	- 2- LRU (more popular as cache discards the data which not used for a long time).
	- 3- pre-fetch or read-ahead (the controller prepares the data in the cache from the disks based on the I/O requests pattern of the compute system).
- Write-aside improves performance by preventing large sequential writes from polluting the cache, keeping cache resources available for small random I/O.
- With dedicated cache, separate sets of memory locations are reserved for reads and writes. In global cache, both reads and writes can use any of the available memory addresses. Cache management is more efficient in a global cache implementation because only one global set of addresses has to be managed.
- Cache is a volatile memory.
- Cache protection techniques:
	- Cache mirroring protects against cache/controller failure by duplicating writes on 2 caches in 2 different controllers.  
	- Cache vaulting protects against power failure by dumping cache to vault drives and when the power is restored the data is transferred back to the cache and the cache writes it in the intended disks .
	>In both techniques, only the write requests mirrored or transferred to the vault drives to maintain the data integrity, read requests are not critical as the data can be read from the physical disks
---
## Storage provisioning
- The process of assigning storage resources to compute systems based on capacity, availability and performance requirements.
- LUN: logical unit which created from a RAID set and it has unique ID.
- LUNs are spread across all the physical drives that belong to that set.
- In non-virtualized environment LUN appears as a raw storage drive to the operating system. To make this drive usable, it is formatted with a file system and then the file system is mounted.
- In virtualized environment there is 2 methods:
	- 1- LUN Assigned to the Hypervisor (Most Common Method)
		- The storage system presents a LUN to the hypervisor (e.g., ESXi, Hyper-V).
		- The hypervisor sees it as a raw physical disk.
		- The hypervisor formats it with its own file system (VMFS, NTFS, etc.).
		- Inside this file system, the hypervisor creates virtual disks (VMDK, VHDX).
		- These virtual disks are then assigned to virtual machines and appear to each VM as a raw drive.
		- Each VM formats its virtual disk with its own guest file system (NTFS, EXT4, etc.).
		- Multiple VMs can share the same LUN, because the hypervisor manages access through its file system
	- 2-LUN Assigned Directly to a Virtual Machine (Direct LUN / RDM / Passthrough
		- The entire LUN is mapped directly to a single VM.
		- No virtual disk (no VMDK/VHDX) is created.
		- This method is recommended when:
		    - The VM runs performance-critical applications that need low latency.        
		    - Storage cannot be shared with other VMs (to avoid slowdowns).
		    - The VM participates in a cluster with a physical server, so both need direct access to the same LUN.
- It is highly recommended to create the RAID set from drives of the same type, speed, and capacity to ensure maximum usable capacity, reliability, and consistency in performance.
- Shared pool can consists of different types of drivers 
- Types of storage provisioning:
	- Traditional (Thick): the whole LUN is allocated for compute system or VM.
	- Virtual (Thin): only needed part of LUN (storage) by the compute system or VM is allocated and the rest is free for any other VMs.
- Physical disks (storage box) --> Pool (from one storage box)--> *provisioning* --> LUN (from one storage box).
- Over provisioning: presenting more storage capacity to servers than the physical capacity actually available on the storage system.
- LUN masking: A process that provides data access control by defining which LUNs a compute system can access.
## Storage tiering
- Establishing hierarchy of storge types (tiers) to store data based on service level requirement at minimal cost.
- Storage tiers:
	- 1- flash tier (fastest).
	- 2- HDD tier.
	- 3- hybrid tier.
	- ![[Pasted image 20251208192914.png]]
- Tiering is applied on one storage box.
- cache tiering = use some space of SSDs as cache memory like virtual memory concept.
- SAN zoning: connect servers to the storage only physically by opening channel between them.
- N-port: node port on server or storage box.
- F-port: ports on SAN switch connected to storage box or server.
- E-port: ports between SAN switches.
- G-port: can be F or E.
- HBA (front end controller) for storage box = HBA for the servers.
- HBA has WWN and NIC has MAC.
- WWN = WWNN + WWPN.
- WWN is is used for zoning process.
- WWN = HBA identifier = LUN identifier
---
# Day 3
- Types of the storage
	- 1- DAS: Direct attached storage (HDD, SSD).
	- 2- SAN: Block-level storage (RAW without file system).
		- How the data is transferred ? by FC-SAN and IP-SAN
			- FC-SAN has 2 protocols: FC and FCoE.
			- IP-SAN has 2 protocols: ISCSI and FCIP.
	- 3- NAS: uses TCP/IP models, we need only to understand some types of file systems.
	- 4- Object: Data can be transferred through API.
	- 5- Unified: storage can understand block-level, TCP/IP and API.
# FC
- Most used.
- Initiator = HBA (server).
- Target = front-end port (Storage box).
- Interconnecting device: SAN switch or SAN director.
- FC uses FC layers which are lossless layers so the data can NOT be lost
- FC pros:
	- High performance due to low traffic in SAN fabric (only storage traffic) and the high capability pf the SAN switch (8G,16G).
	- Reliable as it uses FC-layers.
- FC cons:
	- Expensive as all the components (SAN switch) were not existed in our environment.
	- Complex: servers connected to storage boxes with L2 switches (for management) and SAN switch for data transfer.
# ISCSI
- used in start-ups
- initiator:
	- NIC + ISCSI SW: the server CPU handles ISCSI functionality and TCP/IP functionality.
	- ToE: TCP offload engine. TCP functionality on ToE NIC and ISCSI handled by the server.
	- ISCSI HBA: the server does NOT handle anything.
	- Target: front end port
	- interconnecting device: L2 switch
	- ISCSI pros:
		- No additional cost as the interconnecting devices are already exists in the data centre
	- ISCSI cons:
		- Network congestion.
		- uses TCP lossy layers
# FCIP
- initiator: HBA (server).
- target: front-end port (Storage box).
- used to transfer data from site to site.
- main component : FCIP gateway, it is put in each site and connected to the SAN switch in the site and between the sites there is a router.
- FCIP gateway encapsulates FC frame into network frame to route it to the other site.
# FCoE
- initiator: converge network adapter (CNA) can act as NIC and HBA.
- target: front-end port (Storage box).
- interconnecting devices : FCoE switch can act as L2 switch and SAN switch.
- Mix of FC and ISCSI as it uses the network physical layers from ISCSI as it is cheap (no need to additional devices) and FC-layers which are lossless for reliability.
- we need CNA as it uses network layers (NIC) then FC-layers (HBA).
- The network becomes converged enhanced ethernet (CEE).
- FCoE is connected to SAN switch
---
# File system 
- NAS: it is file sharing storage using TCP/IP stack.
- Peer-to-peer file sharing model (P2P): clients share files with each others.
- Client-server file sharing model : server decides the permissions and once someone opened the file it becomes locked for the rest EX: NFS,CIFS.
- distributed file system model: each client has chunk of data and each one shares it (uTorrent) EX: Hadoop.
---
# Object storage
- All objects are stored on the same line (flat address space), so we can get the data at the same time
- each object has:
	- object = data.
	- metadata = some info (owner, type, date, permissions).
	- Defined attributes (key): for easy query.
- Use API calls.
# Unified storage
- can serve block-level (SAN) ,file-level (NAS), object-level (OSD).
---
# Business continuity
- information availability (IA): ensure that the info is available, accessible in timeliness and consistent (info integrity).
- SLA = service level agreement.
- MTBF: mean time between failures = total uptime / no # fail.
- MTTR: mean time to recover = total downtime / no # failures.
- RPO: recovery point objectives: amount of time that the data is loss.
- RTO: recover time objective : time required to recover (go up again).
- Disaster Recovery (DR) Drills:  crucial IT exercises simulating outages to test data restoration and business continuity plans, ensuring systems can switch to backup sites without major disruption.
