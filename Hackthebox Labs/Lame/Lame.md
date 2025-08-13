[[HTB Labs]]
![[Pasted image 20250609215814.png]]
# Machine Info

----
## About Lame
Lame is an easy Linux machine, requiring only one exploit to obtain root access. It was the first machine published on Hack The Box and was often the first machine for new users prior to its retirement. 
## Achievement
https://www.hackthebox.com/achievement/machine/2090648/1
---
# Write Up
## Enumeration
```
┌──(dd0x㉿kali)-[~]
└─$ nmap -p- --min-rate 10000 10.10.10.3
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-09 09:14 EDT
Stats: 0:00:08 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 64.22% done; ETC: 09:14 (0:00:04 remaining)
Nmap scan report for 10.10.10.3
Host is up (0.21s latency).
Not shown: 65530 filtered tcp ports (no-response)
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3632/tcp open  distccd

Nmap done: 1 IP address (1 host up) scanned in 13.88 seconds
```

```
┌──(dd0x㉿kali)-[~]
└─$ nmap -p 21,22,139,445,3632 -sCV 10.10.10.3
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-09 09:15 EDT
Nmap scan report for 10.10.10.3
Host is up (0.20s latency).

PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.14.140
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey: 
|   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
|_  2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
3632/tcp open  distccd     distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_clock-skew: mean: 1h38m39s, deviation: 2h49m45s, median: -21m23s
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   Computer name: lame
|   NetBIOS computer name: 
|   Domain name: hackthebox.gr
|   FQDN: lame.hackthebox.gr
|_  System time: 2025-06-09T08:54:15-04:00
|_smb2-time: Protocol negotiation failed (SMB2)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 55.69 seconds

```

```
┌──(dd0x㉿kali)-[~]
└─$ searchsploit vsftpd 2.3.4 
--------------------------------------------------------- ---------------------------------
 Exploit Title                                           |  Path
--------------------------------------------------------- ---------------------------------
vsftpd 2.3.4 - Backdoor Command Execution                | unix/remote/49757.py
vsftpd 2.3.4 - Backdoor Command Execution (Metasploit)   | unix/remote/17491.rb
--------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```
This payload didn't work so I tried other exploitable versions which I find `searchsploit 3.0.20`.
```
┌──(dd0x㉿kali)-[~]
└─$ searchsploit 3.0.20   
--------------------------------------------------------- ---------------------------------
 Exploit Title                                           |  Path
--------------------------------------------------------- ---------------------------------
CubeCart 3.0.20 - '/admin/login.php?goto' Arbitrary Site | php/webapps/36686.txt
CubeCart 3.0.20 - 'switch.php?r' Arbitrary Site Redirect | php/webapps/36687.txt
CubeCart 3.0.20 - Multiple Script 'redir' Arbitrary Site | php/webapps/36685.txt
Maxthon Browser 3.0.20.1000 - ref / replace Denial of Se | windows/dos/16084.html
Samba 3.0.20 < 3.0.25rc3 - 'Username' map script' Comman | unix/remote/16320.rb
Samba < 3.0.20 - Remote Heap Overflow                    | linux/remote/7701.txt
Spy Emergency 23.0.205 - Unquoted Service Path Privilege | windows/local/40550.txt
--------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```
Using the  Samba 3.0.20 < 3.0.25rc3 - 'Username' map script' Comman.... which works as a root 

continnue......
