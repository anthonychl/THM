This repo contains the various ways to perform privilege escalation. Privilege escalation is a cybersecurity attack that allows a threat actor to attack root access to an endpoint from an account with lower privileges. 


## Linux Privilege Escalation 

**Service Exploit** - Exploit services on the Linux machine

**Readable Shadow file** - Reading a shadow file and using a password to crack password hashes

**Wrtiable etc shadow** - If the shadow file has write permissions you can modify a password any user in the file

**Writable etc passwd** - Generate a password for any user

**Sudo Shell Escape Sequences** - Use the sudo -l command to find linux commands/services that can be executed with elevated privileges

**Sudo Environment Variables** - Exploit environment variables

**Cron Jobs** - Exploit automated jobs 

- File permissions

- Wildcards

- Path Environments

**SUID GUID** - Exploit SUID permissions to execute files with permissions of the file owner

- Executables environment variables

- Executables known exploits

- Executables abusing shell features 1 & 2

**Passwords and Keys** - Searching for passwords and keys 

- Files

- Config Files

**NFS** - Check the permissions of the file share for root privileges

**Kernel Exploits** - Exploit the kernel in the linux machine

**Escalation Scripts** - Scipts like linpeas check for common default configurations that can be leveraged for privilege escalation






