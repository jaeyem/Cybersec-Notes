[[linuxmain]]

```
$ ls -lha

drwxr-xr-x  3 leo  users  100 28 mag 20.03 .
drwxrwxrwx 18 root root   440 28 mag 20.03 ..
drwxr-xr-x  2 leo  users   40 28 mag 20.02 dir
-rwxr-xr-x  1 leo  users   28 28 mag 20.03 exec.sh
-rw-r--r--  1 leo  users    6 28 mag 20.02 test.txt
```

- Permissions:
    
    - `d` = directory
        
    - `rwx` = owner (`leo`) can read, write, execute
        
    - `r-x` = group (`users`) can read and execute, but not write
        
    - `r-x` = others can read and execute, but not write
        
- `3` = number of hard links to this directory
    
- Owner: `leo`
    
- Group: `users`

Another example is this
```
-rwxr-xr-x  1 leo  users   28 28 mag 20.03 exec.sh
```
### What this means for **leo** (owner):

- `r` = read permission — leo can open and read the file
    
- `w` = write permission — leo can edit/modify the file
    
- `x` = execute permission — leo can run this file as a program/script
    

---

### What this means for **users** (group):

- `r` = read permission — group members can read the file
    
- `-` = no write permission — group members **cannot** modify the file
    
- `x` = execute permission — group members can run the file