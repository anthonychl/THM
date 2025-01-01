Config files often contain passwords in plaintext or other reversible formats.

List the contents of the user's home directory:
```
ls /home/user
```
Note the presence of a myvpn.ovpn config file. View the contents of the file:
```
cat /home/user/myvpn.ovpn
```
```
user@debian:~$ cat myvpn.ovpn 
client
dev tun
proto udp
remote 10.10.10.10 1194
resolv-retry infinite
nobind
persist-key
persist-tun
ca ca.crt
tls-client
remote-cert-tls server
auth-user-pass /etc/openvpn/auth.txt
comp-lzo
verb 1
reneg-sec 0

```
The file should contain a reference to another location where the root user's credentials can be found.
```
user@debian:~$ cat /etc/openvpn/auth.txt
root
password123
```
Switch to the root user, using the credentials:
```
su root
```