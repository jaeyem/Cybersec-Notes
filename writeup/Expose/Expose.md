![[Pasted image 20250603224841.png]]

## Enumeration

```
┌──(dd0x㉿kali)-[~]
└─$ nmap -p- --min-rate 10000 10.10.209.74        
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-03 10:34 EDT
Nmap scan report for 10.10.209.74
Host is up (0.28s latency).
Not shown: 65530 closed tcp ports (reset)
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
53/tcp   open  domain
1337/tcp open  waste
1883/tcp open  mqtt

Nmap done: 1 IP address (1 host up) scanned in 10.41 seconds
```

```
┌──(dd0x㉿kali)-[~]
└─$ nmap -p 21,22,53,1337,1883  -sCV 10.10.209.74
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-03 11:12 EDT
Nmap scan report for 10.10.209.74
Host is up (0.26s latency).

PORT     STATE SERVICE                 VERSION
21/tcp   open  ftp                     vsftpd 2.0.8 or later
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.23.62.59
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp   open  ssh                     OpenSSH 8.2p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 d1:c2:70:1a:8f:8e:f4:20:48:82:70:2e:8d:ef:d9:19 (RSA)
|   256 01:03:15:11:1c:86:7d:06:92:b0:36:62:15:e8:f8:89 (ECDSA)
|_  256 c8:fd:9f:65:9d:f2:4a:4f:e3:7c:e7:c7:66:17:0b:54 (ED25519)
53/tcp   open  domain                  ISC BIND 9.16.1 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.16.1-Ubuntu
1337/tcp open  http                    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: EXPOSED
|_http-server-header: Apache/2.4.41 (Ubuntu)
1883/tcp open  mosquitto version 1.6.9
| mqtt-subscribe: 
|   Topics and their most recent payloads: 
|     $SYS/broker/load/bytes/received/1min: 16.45
|     $SYS/broker/load/messages/sent/15min: 0.38
|     $SYS/broker/load/messages/received/15min: 0.09
|     $SYS/broker/load/bytes/received/5min: 3.55
|     $SYS/broker/load/connections/15min: 0.08
|     $SYS/broker/load/messages/received/1min: 0.91
|     $SYS/broker/bytes/received: 89
|     $SYS/broker/load/bytes/sent/15min: 11.55
|     $SYS/broker/bytes/sent: 1856
|     $SYS/broker/load/messages/sent/5min: 0.20
|     $SYS/broker/version: mosquitto version 1.6.9
|     $SYS/broker/load/bytes/sent/1min: 3.65
|     $SYS/broker/load/sockets/1min: 0.91
|     $SYS/broker/uptime: 2486 seconds
|     $SYS/broker/store/messages/bytes: 161
|     $SYS/broker/messages/sent: 52
|     $SYS/broker/messages/received: 5
|     $SYS/broker/heap/current: 48680
|     $SYS/broker/load/sockets/5min: 0.20
|     $SYS/broker/load/messages/sent/1min: 0.91
|     $SYS/broker/load/connections/5min: 0.20
|     $SYS/broker/load/sockets/15min: 0.08
|     $SYS/broker/load/messages/received/5min: 0.20
|     $SYS/broker/load/bytes/received/15min: 1.62
|     $SYS/broker/load/bytes/sent/5min: 1.07
|_    $SYS/broker/load/connections/1min: 0.91
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 24.57 seconds

```

