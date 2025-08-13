**Web shell** is a browser-based shell session we can use to interact with the underlying operating system of a web server. Again, to gain remote code execution via web shell, we must first find a website or web application vulnerability that can give us file upload capabilities. Most web shells are gained by uploading a payload written in a web language on the target server. The payload(s) we upload should give us remote code execution capability within the browser. The proceeding sections and challenges will primarily be focused on executing commands through our web shells in the browser. Still, it is essential to know that relying on the web shell alone to interact with the system can be unstable and unreliable because some web applications are configured to delete file uploads after a certain period of time. To achieve persistence on a system, in many cases, this is the initial way of gaining remote code execution via a web application, which we can then use to later upgrade to a more interactive reverse shell.

# Landanum
**Laudanum** is a repository of ready-made files that can be used to inject onto a victim and receive back access via a reverse shell, run commands on the victim host right from the browser, and more. The repo includes injectable files for many different web application languages to include asp, aspx, jsp, php, and more. 
https://github.com/jbarcia/Web-Shells/tree/master/laudanum


## Detection & Prevention
Monitoring

When it comes to looking for and identifying active shells, payload delivery and execution, and potential attempts to subvert our defenses, we have many different options to utilize to detect and respond to these events. Before talking about data sources and tools we can use, let's take a second to talk about the MITRE ATT&CK Framework and define the techniques and tactics being utilized by attackers. The ATT&CK Framework as defined by MITRE, is "a globally-accessible knowledge base of adversary tactics and techniques based on real-world observations."
![[Pasted image 20250807002759.png]]


Events To Watch For:

    File uploads: Especially with Web Applications, file uploads are a common method of acquiring a shell on a host besides direct command execution in the browser. Pay attention to application logs to determine if anyone has uploaded anything potentially malicious. The use of firewalls and anti-virus can add more layers to your security posture around the site. Any host exposed to the internet from your network should be sufficiently hardened and monitored.

    Suspicious non-admin user actions: Looking for simple things like normal users issuing commands via Bash or cmd can be a significant indicator of compromise. When was the last time an average user, much less an admin, had to issue the command whoami on a host? Users connecting to a share on another host in the network over SMB that is not a normal infrastructure share can also be suspicious. This type of interaction usually is end host to infrastructure server, not end host to end host. Enabling security measures such as logging all user interactions, PowerShell logging, and other features that take note when a shell interface is used will provide you with more insight.

    Anomalous network sessions: Users tend to have a pattern they follow for network interaction. They visit the same websites, use the same applications, and often perform those actions multiple times a day like clockwork. Logging and parsing NetFlow data can be a great way to spot anomalous network traffic. Looking at things such as top talkers, or unique site visits, watching for a heartbeat on a nonstandard port (like 4444, the default port used by Meterpreter), and monitoring any remote login attempts or bulk GET / POST requests in short amounts of time can all be indicators of compromise or attempted exploitation. Using tools like network monitors, firewall logs, and SIEMS can help bring a bit of order to the chaos that is network traffic.

Establish Network Visibility

Much like identifying and then using various shells & payloads, detection & prevention requires a detailed understanding of the systems and overall network environment you are trying to protect.

![[Pasted image 20250807002844.png]]

## Protecting End Devices

End devices are the devices that connect at the "end" of a network. This means they are either the source or destination of data transmission. Some examples of end devices would be:

    Workstations (employees computers)
    Servers (providing various services on the network)
    Printers
    Network Attached Storage (NAS)
    Cameras
    Smart TVs
    Smart Speakers

We should prioritize the protection of these kinds of devices, especially those that run an operating system with a CLI that can be remotely accessed. The same interface that makes it easy to administer and automate tasks on a device can make it a good target for attackers. As simple as this seems, having anti-virus installed & enabled is a great start. The most common successful attack vector besides misconfiguration is the human element. All it takes is for a user to click a link or open a file, and they can be compromised. Having monitoring and alerting on your end devices can help detect and potentially prevent issues before they happen.

On Windows systems, Windows Defender (also known as Windows Security or Microsoft Defender) is present at install and should be left enabled. Also, ensuring the Defender Firewall is left enabled with all profiles (Domain, Private and Public) left on. Only make exceptions for approved applications based on a change management process. Establish a patch management strategy (if not already established) to ensure that all hosts are receiving updates shortly after Microsoft releases them. All of this applies to servers hosting shared resources and websites as well. Though it can slow performance, AV on a server can prevent the execution of a payload and the establishment of a shell session with a malicious attacker's system.

## Potential Mitigations:

Consider the list below when considering what implementations you can put in place to mitigate many of these vectors or exploits.

    Application Sandboxing: By sandboxing your applications that are exposed to the world, you can limit the scope of access and damage an attacker can perform if they find a vulnerability or misconfiguration in the application.

    Least Privilege Permission Policies: Limiting the permissions users have can go a long way to help stop unauthorized access or compromise. Does an ordinary user need administrative access to perform their daily duties? What about domain admin? Not really, right? Ensuring proper security policies and permissions are in place will often hinder if not outright stop an attack.

    Host Segmentation & Hardening: Properly hardening hosts and segregating any hosts that require exposure to the internet can help ensure an attacker cannot easily hop in and move laterally into your network if they gain access to a boundary host. Following STIG hardening guides and placing hosts such as web servers, VPN servers, etc., in a DMZ or 'quarantine' network segment will stop that type of access and lateral movement.

    Physical and Application Layer Firewalls: Firewalls can be powerful tools if appropriately implemented. Proper inbound and outbound rules that only allow traffic first established from within your network, on ports approved for your applications, and denying inbound traffic from your network addresses or other prohibited IP space can cripple many bind and reverse shells. It adds a hop in the network chain, and network implementations such as Network Address Translation (NAT) can break the functionality of a shell payload if it is not taken into account.





[[HackTheBox]]