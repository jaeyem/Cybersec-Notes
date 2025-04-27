[[Enumeration]]

## Telnet
application layer protocol used to connect to a virtual terminal of another computer.
```
telnet [IP]
```

Needs login (username) & password 

## File Transfer Protocol (FTP)
developed to make the transfer of files between different computers with different systems efficient.
sends and receives data as cleartext; therefore, we can use Telnet (or Netcat) to communicate with an FTP server and act as an FTP client.

```
telnet [IP] 21
```

requires USER name & PASS passwd

Another way to access

```
ftp [IP]
```

## Simple Mail Transfer Protocol (SMTP)
used to communicate with an MTA server. Because SMTP uses cleartext, where all commands are sent without encryption, we can use a basic Telnet client to connect to an SMTP server and act as an email client (MUA) sending a message.
SMTP server listens on port 25 by default.

```
telnet [IP] 25
```

## Post Office Protocol 3 (POP3)
a protocol used to download the email messages from a Mail Delivery Agent (MDA) server, as shown in the figure below. The mail client connects to the POP3 server, authenticates, downloads the new email messages before (optionally) deleting them.

```
telnet [IP] 110
```

sample commands
```
Mailbox Information and Retrieval: 

- **STAT:** Retrieves the number of messages and the total size of the mailbox. 

- **LIST:** Lists all messages in the mailbox, including their message number and size. 

- RETR `<message_number>`: Retrieves the full content of a specific message. 

- TOP `<message_number> <number_of_lines>`: Retrieves the header information and a specified number of lines of the message body.
```

## Internet Message Access Protocol (IMAP)
makes it possible to keep your email synchronized across multiple devices (and mail clients). In other words, if you mark an email message as read when checking your email on your smartphone, the change will be saved on the IMAP server (MDA) and replicated on your laptop when you synchronize your inbox.

```
telnet [IP] 143
```

LOGIN username password

## password attack

```
hydra -l username -P wordlist.txt server service
```


## Default port number for common protocols

|Protocol|TCP Port|Application(s)|Data Security|
|---|---|---|---|
|FTP|21|File Transfer|Cleartext|
|FTPS|990|File Transfer|Encrypted|
|HTTP|80|Worldwide Web|Cleartext|
|HTTPS|443|Worldwide Web|Encrypted|
|IMAP|143|Email (MDA)|Cleartext|
|IMAPS|993|Email (MDA)|Encrypted|
|POP3|110|Email (MDA)|Cleartext|
|POP3S|995|Email (MDA)|Encrypted|
|SFTP|22|File Transfer|Encrypted|
|SSH|22|Remote Access and File Transfer|Encrypted|
|SMTP|25|Email (MTA)|Cleartext|
|SMTPS|465|Email (MTA)|Encrypted|
|Telnet|23|Remote Access|Cleartext|