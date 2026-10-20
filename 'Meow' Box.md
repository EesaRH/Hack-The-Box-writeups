# **First box: Meow.**

## Task:
Given the task to find and read the contents of a file and provide the flag within the file as proof, the target IP was 10.129.89.221.

## What I did:
1) Ran `nmap -sV` scan in order to find out what service each port is running and (where possible) version info.
2) Once it showed port 23 was open running Telnet, I connected directly using `Telnet 10.129.89.221`. I then was presented with the option to log in,
   guessing the username `root` exploiting the vulnerabitlity default and blank credentials for admin accounts.
3) Used `pwd` to find out where I currently was, followed by `ls` to list the available files/folders and then `cd` to move around and eventually find
   the target file.
4) Once in the target file, I ran the `cat` command to display the file contents and retrieve the flag for proof.

## Mistakes I made:
1) Though not a mistake and more of an inefficient method, I used `nmap` at first, though I shortly realised that `nmap -sV` is better in practice, 
   and used that instead.
2) Mixed up `dir` from Windows CLI with `ls` from Linux CLI, which is what I was working in.

## My key take-aways:
-I gained confidence in scanning networks and navigating through folders using the CLI.
-Learnt about the misconfiguration companies would make by leaving Telnet running, allowing malicious people to exploit no password on an admin account, and how 
 easy it is to exploit this vulnerablity.
-That Telnet sends everything uncencrypted.

## How it can be prevented:
-SSH replaced Telnet in the late 1990s, but some people still leave Telnet running, hence the misconfiguration. Disabling Telnet where it isn't needed is one way
 to prevent this vulnerability on your own network/system.
-Scanning your own network to check for open port 23 is another preventative measure.
-Make sure default and blank credentials on all devices are changed is another method of prevention.


