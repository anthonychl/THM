Weak File Permissions - Writable /etc/passwd

The /etc/passwd file contains information about user accounts. It is world-readable, but usually only writable by the root user. Historically, the /etc/passwd file contained user password hashes, and some versions of Linux will still allow password hashes to be stored there.

Note that the /etc/passwd file is world-writable:
```
ls -l /etc/passwd
```
Generate a new password hash with a password of your choice:
```
openssl passwd newpasswordhere
```
Edit the /etc/passwd file and place the generated password hash between the first and second colon (:) of the root user's row (replacing the "x").

Switch to the root user, using the new password:
```
su root
```
Alternatively, copy the root user's row and append it to the bottom of the file, changing the first instance of the word "root" to "newroot" and placing the generated password hash between the first and second colon (replacing the "x").

Now switch to the newroot user, using the new password:
```
su newroot
```