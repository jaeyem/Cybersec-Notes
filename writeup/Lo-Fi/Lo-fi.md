[[Writeup]]
![[Pasted image 20250503231602.png]]

Want to hear some lo-fi beats, to relax or study to? We've got you covered! 

Access this challenge by deploying both the vulnerable machine by pressing the green "Start Machine" button located within this task, and the TryHackMe AttackBox by pressing the  "Start AttackBox" button located at the top-right of the page.

Navigate to the following URL using the AttackBox: http://10.10.39.113 and find the flag in the root of the filesystem.


Check out similar content on TryHackMe:

    LFI Path Traversal
    File Inclusion

Note: The web page does load some elements from external sources. However, they do not interfere with the completion of the room.

## Enumeration
```
┌──(dd0x㉿kali)-[~]
└─$ nmap -p- --min-rate 10000 10.10.237.214
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-03 11:21 EDT
Nmap scan report for 10.10.237.214
Host is up (0.35s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 10.86 seconds
```

```
┌──(dd0x㉿kali)-[~]
└─$ nmap -p 22,80 -sCV 10.10.237.214       
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-03 11:21 EDT
Nmap scan report for 10.10.237.214
Host is up (0.26s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 8f:03:2d:0f:21:8b:01:98:bf:4a:dd:26:21:09:e5:be (RSA)
|   256 5b:fa:d0:7c:09:c5:c0:c7:83:df:23:d1:b4:28:36:64 (ECDSA)
|_  256 61:af:79:a5:12:99:60:8d:fb:c0:5d:26:17:78:e6:e5 (ED25519)
80/tcp open  http    Apache httpd 2.2.22 ((Ubuntu))
|_http-server-header: Apache/2.2.22 (Ubuntu)
|_http-title: Lo-Fi Music
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 16.23 seconds
```

Port 80
![[Pasted image 20250503232939.png]]
Here in Discrography there are list of genre when you click them the url appears like this 
`http://10.10.237.214/?page=relax.php`
`http://10.10.237.214/?page=sleep.php`

Now we will do Traversal for eg. `../../../../../blabla.txt`

## Exploit

Let's open burp suite and it loaded this
![[Pasted image 20250503233148.png]]

Send it to the repeater
Now in the request we will change the parameters
I tested `/etc/passwd` which returns a 200 and continuously do the path traverse `../../../etc/passwd` until you reach this 
![[Pasted image 20250503233735.png]]
now change the `/etc/passwd` to `flag.txt`
![[Pasted image 20250503233514.png]]


