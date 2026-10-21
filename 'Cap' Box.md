# **Second box: Cap.**

##Task:
Given the task to gain access to the machine and capture the user and root flags. Target IP of 10.129.44.36.

##What I did:
1) First ran the command `ping <target_ip>` to make sure the connection was good.
2) Next I ran `nmap -sV<target_ip>` and determined there was 3 open ports; 21 (FTP), 22 (SSH), 80 (HTTP).
3) Then I opened my browser and typed in the URL `http://<target_ip>` resulting in being redirected to the path `/data/1`, which was a dashboard showing a network
   "security snapshot" with information for the packet counts of IP, TCP, UDP and total for the logged-in user "nathan".
4) I then changed the ID in the URL and through process of elimination got to `/data/0`, which showed a different data set. This confirmed an Insecure Direct
   Object Reference (IDOR) vulnerability: the web app trusted the ID in the URL with no access control.
5) Following this, I downloaded the capture from `/data/0` and saved the file as `0.pcap`.
6) I then used the command `wireshark` in the terminal to open the Wireshark analyser and opened the file in Wireshark.
7) It showed a mix of HTTP and FTP traffic. By right clicking on a FTP packet and using Wireshark's ability to follow the TCP stream  I was able to reconstruct
   the full session.
8) From doing this, I found the user nathan's username and password (in plaintext) from the reconstructed TCP stream.
9) Using reasoning, since the only other authenticated service on the machine is SSH, within the terminal I attempted `ssh nathan@<target_ip>` and used the password
   from the TCP stream.

##What I found:
- The web app's path endpoint lacked authorisation checks, allowing any logged-in user to view and download the network capture files of other users.
- One of those captures contained an unencrypted FTP login session, disclosing the target user's password in plaintext.

##Mistakes I made:
- Assumed `/data/2` would show a different user's data since the ID is the following number from 1. Only once it returned nathan's own page did I realise and then
  try `/data/0`, leading to what I was looking for.
- Did not realise Linux terminals hide the entirety of the password input (i.e. no cursor movement or dots etc) and assumed the prompt wasn't registering, losing
  the Pwnbox session mid-attempt as a result.
- Hadn't used Wireshark prior to this, so I didn't immediately know to use "Follow -> TCP Stream" rather than reading packets one by one. After spending a few
  minutes exploring Wireshark, however, I found the Follow instrument for the TCP stream.

##My key take-aways:
- The insecurity of user nathan's password is due to two combined vulnerabilities. Firstly, IDOR: The server determines what data belongs to their respective
  requests based off of only the ID in the URL, instead of checking it against the logged-in user sending the request(s). Secondly, using FTP: FTP is a plaintext
  protocol so it doesn't encrypt anything being sent to or from the server, meaning anyone who can find and analyse the traffic can directly read it's contents
  and subsequently sensitive information such as user credentials.
- People often use the same password for different services. As a result, the vulnerability caused by using FTP allows those who received a leaked password to
  attempt it on other services, in this case SSH, granting the attacker access to the whole machine, not just a number of folders/files.
- IDOR vulnerabilities can be found by simply manually editing numbers in a URL, and are common in the real world.
- Credential theft via capturing traffic from plaintext protocols like FTP can happen easily and is a realistic finding.

##How it can be prevented:
- Ensure there is server-side authorisation checks on all endpoints that grant access to data.
- Instead of using FTP, use an alternative that encrypts traffic such as SFTP.
  








  


  
