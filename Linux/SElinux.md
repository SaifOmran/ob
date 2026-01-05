### Describe SELinux Architecture
- SELinux adds an extra security layer on top of Linux permissions.
- Traditional Linux security uses DAC (Discretionary Access Control:
	- Permissions: `rwx`
	- Ownership: user / group
	- If a service is compromised, it can access anything allowed to the user.
	- SELinux limits what a process is allowed to access no matter who runs it.
---
### How SELinux works
- SELinux policies are security rules that define how specific processes access relevant files, directories, and ports.
- Every resource entity, such as a file, process, directory, or port, has a label called an SELinux context, the context label matches a defined SELinux policy rule to allow a process to access the labelled resource.
- By default, an SELinux policy does not allow any access unless an explicit rule grants access. When no allow rule is defined, all access is disallowed.
---
### SELinux policy types
1. Targeted policy -> used in redhat.
2. Minimum policy.
3. Multi-level security (MLS) policy.

- This is the form of the SElinux context
	- ![[Pasted image 20260104234251.png]]
	- The targeted policy focuses only on the type field (label).
	- The type field usually ended with `_t`.
---
### Example of SELinux concept
- In the next figure we have 2 services, Apache service and MariaDB each one has its own context (label), ==Apache -> httpd_t, MariaDB -> mysqld-t==.
- ==Apache== can only access the files with ==httpd_sys_content_t== label.
- MariaDB can only access the files with ==mysqld_db_t== label.
- If any service of them tried to access any files of the other one the SElinux policy will prevent it unless there is a rule that allow the access.
- ![[Pasted image 20260104234857.png]]
- To show the context of the files we use `ls -lZ`.
---
### SELinux operational modes
1. Enforcing -> SELinux is enable + the policy is applied + record logs (used in production).
2. Permissive -> SELinux is enable + the policy is NOT applied + record logs (used in testing).
3. Disabled -> SELinux is turned off.
- To change the operational mode form enforcing to permissive we use `setenforce 0` (applied in run time).
- To change the operational mode form permissive to enforcing we use `setenforce 1`(applied in run time).
- To change the operational mode from  disable to enforcing or permissive, we have to edit in the SELinux configuration file -> */etc/selinux/config* and then ==reboot the system==, when we change the mode from disabled to enforcing the SELinux makes enforced relabelling to make labels for all files that are created when it was turned off.
---
### Monitoring SELinux status
- `sestatus` -> is  used  to  get  the status of a system running SELinux. It displays data about whether SELinux is enabled or disabled, location of key directories, and the loaded policy with its status.
	- ![[Pasted image 20260105000718.png]]
- `getenforce` -> show whether the operational mode is enforcing or permissive in run time.
---
### SELinux context database
- The contexts which are assigned to the files are stored in */etc/selinux/targeted/contexts/files/file_contexts*.
- This is the database the selinux is used to assign the context to the file based on its location.
	- for example the files in */mnt* have context like this `system_u:object_r:mnt_t:s0` with `mnt_t` type.
---
### Changing the context type for files
- To change the context type for file we use `chcon -t [type] [file]`, this modification is applied on the filesystem in the inode table of this file, if there is enforced relabelling the SELinux will re-assign each file with suitable context type based on the location file.
- To make force relabelling we use `restorecon -v [file]`, this command will make the SELinux to read the context type of the file from the selinux DB and assign it to the file based on the file location
- ##### Scenario on what we said:
	- You have a web file that Apache (httpd) should read -> /var/www/html/index.html
	- The default context -> `ls -Z /var/www/html/index.html`
		- system_u:object_r:==httpd_sys_content_t==:s0 index.html
	- Now we manually change the type -> `chcon -t user_home_t /var/www/html/index.html`
	- Check again the context -> `ls -Z /var/www/html/index.html`
		- system_u:object_r:==user_home_t==:s0 index.html
	- This change is written directly into the inode of the file.
		- Result:
			- Apache cannot read the file.
			- SELinux denies access (even if permissions are 777).
	- Enforced relabeling (restore default context) -> `restorecon -v /var/www/html/index.html`
	- Check after the enforced relabeling -> `ls -Z /var/www/html/index.html`
		- system_u:object_r:==httpd_sys_content_t==:s0 index.html
		- The file is now accessible by Apache again.
- [DEMO link for similar scenario](https://itihub.sharepoint.com/sites/PTPCloudArchitecture46Ismailia/_layouts/15/stream.aspx?id=%2Fsites%2FPTPCloudArchitecture46Ismailia%2FShared%20Documents%2FCloud%2FRecordings%2FLinux%20Admin2%2DSElinux%20DEMO%2Emp4&referrer=StreamWebApp%2EWeb&referrerScenario=AddressBarCopied%2Eview%2E3d23dfd3%2D25c1%2D46f4%2Da7b3%2D36858d9820af)
---
### Semanage command
##### Changing the context of file
- As we have seen that `chcon` command only change the label in inode table or on the filesystem and if there any enforced relabeling happened the file label will restored to its label based on its location.
- So, we need to define a rule that inform the SELinux DB with a label for the file, so if there any enforced relabeling happened, SELinux uses its DB to re-assign the label for the files and sees that the file we defined the rule for has a specific label, so we guarantee that the file will have the required label.
- so, `chcon` -> filesystem relabeling and `semanage` -> change in SELinux DB. 
- To define a rule for file context in SELinux DB we use `semanage fcontext -a -t [label] [file]` 
	- `-a` -> add.
	- `-v` -> verbose.
- and then we use `restore -v [file]` to relabel the file with the new context
- The defined rules are stored in */etc/selinux/targeted/contexts/files/file_contexts.local*.
- To define a rule for ==all files== in the same directory we use `semanage fcontext -a -t [label] "dir1/dir2(/.*)?"` then we use `restorecon -Rv /dir1/dir2`.
	- `-R` -> recursive for all files in the directory dir2.
- 