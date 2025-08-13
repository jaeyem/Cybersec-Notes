[[Writeup]]]
![[Pasted image 20250503150110.png]]
Check out our new cloud service, Authentication Anywhere -- log in from anywhere you would like! Users can enter their username and password, for a totally secure login process! You definitely wouldn't be able to find any secrets that other people have in their profile, right?


Access this challenge by deploying both the vulnerable machine by pressing the green "Start Machine" button located within this task, and the TryHackMe AttackBox by pressing the  "Start AttackBox" button located at the top-right of the page.

Navigate to the following URL using the AttackBox: http://10.10.72.222

Check out similar content on TryHackMe:

    IDOR

IDOR might be a clue 

## Enumeration

```
┌──(dd0x㉿kali)-[~]
└─$ nmap -p- --min-rate 10000 10.10.72.222   
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-03 02:43 EDT
Nmap scan report for 10.10.72.222
Host is up (0.25s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 9.10 seconds

```

```
┌──(dd0x㉿kali)-[~]
└─$ nmap -p 22,80 -sCV 10.10.72.222
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-03 02:48 EDT
Nmap scan report for 10.10.72.222
Host is up (0.25s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 2e:35:47:36:e0:97:7c:03:4c:fe:27:f2:1e:3a:fc:03 (RSA)
|   256 47:c6:51:44:5b:b3:b2:69:9c:e6:fe:c4:42:58:3f:f7 (ECDSA)
|_  256 a6:12:fc:5e:f3:85:0f:b9:9d:35:a2:75:0d:bc:82:5a (ED25519)
80/tcp open  http    Apache httpd 2.4.53 ((Debian))
|_http-server-header: Apache/2.4.53 (Debian)
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-title: Login
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 15.60 seconds   
```

So this is the website and there is a clue it says Use the guest account! (Ctrl+U)
![[Pasted image 20250503150400.png]]

which we will see the view source of login.php
![[Pasted image 20250503150458.png]]

there is a comment for the guest credentitals `guest:guest` and log it in
![[Pasted image 20250503150542.png]]
Now what the room says about IDOR we have a clue from the url 
``
`http://10.10.72.222/profile.php?user=guest` and we will change it to admin `user=admin`

![[Pasted image 20250503150652.png]]