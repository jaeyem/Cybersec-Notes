[[HackTheBox]]

### Shebang
each script and always starts with "#!".
```
#!/bin/bash
```

### Arguments
advantage of bash scripts is that we can always pass up to 9 arguments ($0-$9) 
9 arguments because the first argument `$0` is reserved for the script. As we can see here, we need the dollar sign ($) before the name of the variable.

```
dd0x200@htb[/htb]$ ./script.sh ARG1 ARG2 ARG3 ... ARG9
       ASSIGNMENTS:       $0      $1   $2   $3 ...   $9

```

