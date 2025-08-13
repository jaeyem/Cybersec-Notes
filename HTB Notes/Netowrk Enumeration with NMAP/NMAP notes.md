[[HackTheBox]]
# Enumeration
`Enumeration` is the most critical part of all. The art, the difficulty, and the goal are not to gain access to our target computer. Instead, it is identifying all of the ways we could attack a target we must find.

## Use Cases

The tool is one of the most used tools by network administrators and IT security specialists. It is used to:

- Audit the security aspects of networks
- Simulate penetration tests
- Check firewall and IDS settings and configurations
- Types of possible connections
- Network mapping
- Response analysis
- Identify open ports
- Vulnerability assessment as well.
## Nmap Architecture

Nmap offers many different types of scans that can be used to obtain various results about our targets. Basically, Nmap can be divided into the following scanning techniques:

- Host discovery
- Port scanning
- Service enumeration and detection
- OS detection
- Scriptable interaction with the target service (Nmap Scripting Engine)


## Syntax

The syntax for Nmap is fairly simple and looks like this:
```
nmap <scan types> <options> <target>
```

#### Scan Network Range
```shell-session
 sudo nmap 10.129.2.0/24 -sn -oA tnet | grep for | cut -d" " -f5
```

|10.129.2.0/24`|Target network range.|
|`-sn`|Disables port scanning.|
|`-oA tnet`|Stores the results in all formats starting with the name 'tnet'.|



## Host Discovery
Scan Multiple IPs
```
dd0x200@htb[/htb]$ sudo nmap -sn -oA tnet 10.129.2.18 10.129.2.19 10.129.2.20| grep for | cut -d" " -f5

10.129.2.18
10.129.2.19
10.129.2.20
```



#### Note:
TTL value is 128, which is a strong indicator of a Windows operating system, as Windows typically sets an initial TTL of 128 for ICMP packets. In contrast, Linux/Unix systems commonly use 64 or 255.
