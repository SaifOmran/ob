### Boot process steps
1. Power on the server, once the electricity is delivered to the motherboard, the CPU starts to execute the instructions in firmware that is stored on the ROM, the firmware may be BIOS or UEFI (we will consider the rest of steps that the firmware is BIOS).
2. Start the first instructions of the firmware which are Power On Self Test (POST) -> initialize hardware devices and detect them.
3. Continue BIOS instructions by checking boot order devices
	- Hard disk (we will consider that the first boot device is the hard disk).
	- USB.
	- CD.
4. Once the boot device is hard disk with MBR partition style, so the CPU will check the first sector of the hard disk which contains
	- Partition table = 16 byte
	- Boot loader/Boot code = 446 byte, and it stores the $1^{st}$ stage boot loader which points to the location of $2^{nd}$ stage boot loader file */boot/grub2/grub.cfg*.
	- Magic number = 2 byte
5. Grub takes the control and load the grub menu which contains the entries (kernels) stored in */boot/loader/entries* and we choose one of the kernels.
	- ![[Pasted image 20260103225711.png]]
6. Kernel takes control, the kernel is divided to 2 files are stored in */boot* and they are loaded in the memory
	- ![[Pasted image 20260103225750.png]]
	- vmlinuz -> initialize the hardware again to name it (like /dev/sda1) instead of grub naming (hd0,msdos1)
	- initramfs -> contains modules needed by kernel to initialize the hardware and it contains initial filesystem.
7. kernel starts the *systemd* process as it is first process and this process start searching for the / partition which is /dev/sda2 partition and mount it to /sysroot (read-only mount)
![[Pasted image 20260103193742.png]]

8. change root from initial filesystem to the root of the real time filesystem (root user)
	`switch_root /sysroot`.
9. re-execute *systemd* process as we in the new real-time filesystem, and it starts the services which are enabled (services are enabled while booting up the system).
10. finally *systemd* specify which system target to boot up with.
	- here we notice that the systemd is the first process started with process ID =1
	- ![[Pasted image 20260103230027.png]]
---
### Grub files
- */boot/grub2/grub.cfg* -> boot load file.
- */etc/default/grub* -> file contains grub parameters.
	- If we made any changes in this file we need to execute `grub2-mkconfig -o /boot/grub2/grub.cfg` to regenerate *grub.cfg* file to make these changes applied in next boot up.
	- ![[Pasted image 20260103224048.png]]
- */etc/grub.d* -> directory contains scripts used to generate */boot/grub2/grub.cfg* file.
---
### Partitions
- */dev/sda1* -> mounted to /boot (boot partition)
- */dev/sda2* -> mounted to / (root partition)
---
### Root password recovery 
- ##### Method 1
	- ![[WhatsApp Image 2026-01-04 at 12.11.25 AM.jpeg]]
- ##### Method 2
	- ![[WhatsApp Image 2026-01-04 at 12.11.31 AM.jpeg]]
---
### Grub password
- ![[WhatsApp Image 2026-01-03 at 6.33.59 PM 1.jpeg]]
