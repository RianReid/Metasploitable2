# Metasploitable2 Report

## Introduction

### Setup
I started by creating a Metasploitable2 VirtualBox VM.
- Metasploitable2 VM IP address: 10.0.2.4

After doing some research I found that many people use Kali Linux to do their penetration testing on Metasploitable2, so I also created a Kali Linux VirutalBox VM. 
I also learned that a free version of Nessus can be used to perform a scan on Metasploitable2 to highlight any vulnerabilties that I may be able to exploit, so I went 
ahead and installed Nessus on my Kali Linux VM.

![image of install](images/1.PNG)  

Once it was up and running I did a basic network scan as well as an advanced network scan on the Metasploitable2 IP address 10.0.2.4 with each scan both returning approximately 72 vulnerabilities.

![image of scan](images/2.PNG)

When looking through some of the critical and high severity results I discovered that some of them mention how the vulnerability may be exploited, and many listed you could use metasploit, so I installed metasploit on the Kali Linux VM and decided to give it a try.

## Exploiting the Vulnerabilities

### UnrealIRCd Backdoor Detection
Description: The remote IRC server is a version of UnrealIRCd with a backdoor that allows an attacker to execute arbitrary code on the affected host.
- vulnerability found from Nessus

![image of v2](images/8.PNG)

UnrealIRCD is an open source interent relay chat daemon and happens to be the most popular IRC server with over 40% of the market share.
With the use of my nmap scan on the Metasploitable2 IP address I found that for this specific vulnerability the ports I could exploit were ports 6667 or 6697.

![image of nmap scan2](images/7.PNG)

To begin I started up metasploit and did a search for unreal to find the unrealIRCD exploit. After that I looked at the options for the exploit and found that I needed to set the target host and that the target port was already set to 6667 which I found from the nmap scan. After doing some research I learned this exploit may not have a payload built into it so I looked at the payload options and learned that the bind_ruby option gives you the highest privilege so that's what I used. I learned that the payload should listen for a connection and then spawn a command shell once exploited and once I set the payload and ran exploit it worked and opened a command shell on the remote machine. Once I got the shell I ran a couple of commands to show that I had root privilege and also included the encrypted root user passsword from the /etc/shadow file.

![image of process1](images/9.PNG)
![image of process2](images/10.PNG)
![image of process3](images/11.PNG)

### Exploiting Port 139 & 445 (Samba)
Description: Metasploitable2 is using Samba version 3.0.20, which allows an attacker to execute arbitrary commands, by specifying a username containg shell meta characters.

![image of nmap scan3](images/15.PNG)

For this exploit I started up metasploit and did a search for samba to find the correct exploit. After that I looked at the options for the exploit and found that I needed to set the target host and that the target port was already set to 139 but since 445 was also listed in the nmap scan I decided to set the target port to 445 instead. After setting the target host and the port I ran exploit and was able to get another shell with root privilege and was able to run ifoncifg as well as show that I was the root user.

![image of process4](images/12.PNG)
![image of process5](images/13.PNG)
![image of process6](images/14.PNG)

### Exploiting TELNET
Description: This module will test a telnet login on a range of machines and report successful logins.

For this exploit I created a users file as well as a passwords file shown below with some basic usernames and passwords and placed it on my desktop. After starting up metasploit I began the telnet_login vulnerability and looked at the options to set everything up. I changed the target host to the Metasploitable2 VM, set stop on success adn vebose to true, and also set the user_file and the pass_file to take the files I created on my desktop. Once I hit run it iterated throguh the list trying each user and password combination and stopped once it got the correct match which spawned a shell where I was able to run a couple commands as the msfadmin user.

![image of process7](images/16.PNG)
![image of process8](images/17.PNG)
![image of process9](images/18.PNG)

### NFS Exported Share Information Disclosure
Description: At least one of the NFS shares exported by the remote server could be mounted by the scanning host. An attacker may be able to leverage this to read (and possibly write) files on remote host.
- vulnerability found from Nessus

![image of v1](images/3.PNG)

NFS stands for network file system and helps to share files and folders between systems, it also enables you to mount a remote share locally. This all depends on remote procedure calls or RPC service which is controlled by the rpcbind service. In order to find the ports that control rpcbind and NFS I ran an nmap scan on the Metasploitable2 IP address I found that for this specific vulnerability the ports I would exploit were ports 111 for rpcbind and port 2049 for NFS.

![image of nmap scan1](images/4.PNG)

Shown below I learned that I'd be able to mount to the root filesystem, so I planned to create an ssh key on my attacking machine(Kali VM), mount to the nfs, and then add my key to the root users account authorized keys file, this process is shown below. Unfortunately for this exploit I was unable to successfully ssh into the Metasploitable2 VM. From the examples I found my proccess should've worked but for some reason I was still prompted for a password even though I left the ssh passphrase as empty, but I will keep tinkering with it to see if I can eventually ssh in, this was jsut as far as I got!

![image of process10](images/5.PNG)
![image of process11](images/6.PNG)
![image of process12](images/19.PNG)

## Conclusion
Overall I find Metasploitable2 very interesting to mess around on and practice penetration testig with. I think this was very helpful to practice what I learned in college and to push myself to see what I could figure out with on my own and with the help of some research. I plan to keep on exploiting more vulnerabilities and tessting my knowledge on how pen testing works. Thank you for challenging me with this project and report, it was a great refresher on Linux/Github and I appreciate the opportunity to show you my passion!
