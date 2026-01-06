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
	- Ex: `semanage -a -t httpd_sys_content_t /mnt/website(/.*)?` then `restorecon -Rv /mnt/website`
	- `-R` -> recursive for all files in the directory dir2.

- To delete rule we use `semanage fcontext -d -t [label] [file]`, then we use `restore -v [file]`
	- `-d` -> delete.

- To modify rule we have created we use `semanage fcontext -m -t [label] [file]`, then we use `restore -v [file]`
	- `-m` -> modify.

- To show the defined rules we use `semanage fcontext -lC`.

##### Changing the context of port
- As it is known that the Apache is running on port 80, and Apache can access port 80 as port 80 has *httpd_sys_content_t* label which accessible by the Apache.
- What if I want to change the Apache listening port to 82 for testing (you can change it in */etc/httpd/conf/httpd.conf* ? we will notice that if we restarted the httpd service that there is an error.
	- ![[Pasted image 20260105025245.png]]
	- ![[Pasted image 20260105025328.png]]
	- This error is happened because port 82 is not associated with the *httpd_port_t*, so Apache can not listen on it or use it.

- To show the labels of the ports we use `semanage port -l`.

- To add a defined rule for port we use `semanage port -a -t [label] -p [tcp | udp] [port number]`
	- Example -> `semanage port -a -t http_port_t -p tcp 82`, in this case if we restarted the service, it will restart without any error as the Apache service has access to the port label.

- To delete port rule we use `semanage port -d -t [label] -p [tcp | udp] [port number]`.
	- Example -> `semanage port -d -t http_port_t -p tcp 82`.

- To modify port rule we use `semanage port -m -t [label] -p [tcp | udp] [port number]`.
	- Example -> `semanage port -m -t ssh_port_t -p tcp 82`.

- To show definde ports rules for ports we use `semanage port -lC`.
---
##### Permissive mode for service
- Instead of putting the whole system into permissive mode, you can make only one service permissive.
- The service continues to work normally.
- SELinux does NOT block its actions.
- All violations are logged for analysis.
- The rest of the system remains Enforcing.
##### Why to use permissive mode for service ?
- Troubleshooting SELinux issues
- Testing a new or custom service
- Identifying which SELinux rules are missing
- Creating a proper SELinux policy later
- This is much safer than disabling SELinux globally.
##### Practical Example (httpd service)
1. Find the SELinux type of the service -> `ps -eZ | grep httpd`.
	- ![[Pasted image 20260106012948.png]]
	- The SELinux type is `httpd_t`.

2. Put the service into permissive mode -> `semanage permissive -a httpd_t`
	- `httpd` is permissive.
	- Violations are logged but not blocked.

3. Verify by show the services in the permissive mode -> `semanage permissive -l`.
	- ![[Pasted image 20260106013433.png]]

4. Now we will change the listening port of the Apache server from 80 to 85.
	- ![[Pasted image 20260106013607.png]]
	- Add the port to the firewall -> `firewall-cmd --add-port=85/tcp`.

5. Now if we tried to restart the httpd service, it will be restarted without any error as the permissive mode is applied on it, so there is no policy applied on it.
	- ![[Pasted image 20260106013846.png]]

6. If we returned the permissive mode to the httpd service, there would be an error
	- ![[Pasted image 20260106014258.png]]
	- So, we need to return the listening port to 80 again once we returned the enforcing mode on the httpd service.
---
### SELinux Booleans
##### What are SELinux Booleans?
- SELinux Booleans are on/off switches that modify SELinux policy behaviour without changing file contexts or writing new policies.
- They allow or deny specific actions for services at runtime.
- They provide flexibility while keeping SELinux in Enforcing mode.
##### Why SELinux Booleans are used ?
- Enable optional features safely
- Allow controlled exceptions (without disabling SELinux)
- Avoid complex custom policy writing
- Commonly used with services like httpd, ftp, ssh
##### How SELinux Booleans work ?
- Each Boolean controls one specific permission
- Values:
    - `on` / `1` → allow behaviour
    - `off` / `0` → deny behaviour
- Booleans affect policy decisions, not labels or permissions
##### Boolean commands
- To show the all Booleans -> `semanage boolean -l`.
- To activate Boolean -> `setsebool [bool_name] on`. (runtime)
- To de-activate Boolean -> `setsebool [bool_name] off`. (runtime)
- To make the activation or the de-activation permanent even after reboot we use `-P
	- `setsebool -P [bool_name] on`.
	- `setsebool -P [bool_name] off`.
---
## يعني إيه SELinux Boolean؟

تخيل SELinux عامل **زرار ON / OFF** لكل ميزة في السيستم.

- الزرار ده اسمه **Boolean**
- بيشغّل أو يقفل **تصرف معين** في policy
- من غير ما:
    - تغيّر permissions
    - تغيّر context
    - تعطل SELinux
👉 مجرد سماح أو منع لسلوك معيّن.

---

## الفكرة في سطر واحد
SELinux Boolean = استثناء متحكم فيه
بدل ما تقول:
> “SELinux معطّلني، أقفله”

تقول:

> “اسمح للتصرف ده بس”

---

## مثال بسيط جدًا (Apache)
### المشكلة
- Apache شغال ✔
- Permissions صح ✔
- Context صح ✔
- ومع ذلك مش راضي يقرأ ملفات من `/home`
ليه؟  
👉 SELinux policy **قافلة التصرف ده**

---

### الحل بالـ Boolean
في Boolean اسمه:
`httpd_enable_homedirs`
- OFF → Apache ممنوع يدخل على home
- ON → Apache مسموح له
تشغيله:
`setsebool -P httpd_enable_homedirs on`
✔ Apache اشتغل  
✔ SELinux لسه Enforcing  
✔ أمان عالي

---
## الفرق بين Boolean وباقي الحلول

|الحل|بيعمل إيه|
|---|---|
|Disable SELinux|يكسر الأمان كله ❌|
|Permissive mode|يسمح بكل حاجة للخدمة ⚠️|
|chcon|تغيير مؤقت للفايل|
|semanage|تغيير دائم للـ context|
|**Boolean**|سماح لسلوك معين فقط ✅|

---

## تستخدم Boolean إمتى؟
تستخدمه لما:
- الخدمة شغالة صح
- لكن SELinux مانع تصرف معيّن
- وفي Boolean جاهز للتصرف ده