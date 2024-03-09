# Forensics
## Forensic Question 1
- I checked common-auth with `nano /etc/pam.d/common-auth` and noticed the line `auth sufficient pam_exec.so expose_authtok quiet /lib/x86_64-linux-gnu/security/pam-bd`
- Basically it's executing the script in "/lib/x86_64-linux-gnu/security/pam-bd" is exposing the user's authentication token while quietly executing the script (with no output)
```
nano /lib/x86_64-linux-gnu/security/pam-bd
#the password is written in plain-text, assigned to a variable in an if statement
```

![image](https://user-images.githubusercontent.com/69600390/285331394-b1c29e92-42f9-421e-a22a-354b891f194d.png)

![image](https://user-images.githubusercontent.com/69600390/285331420-3662a7cc-188f-46d8-914c-1bce091a43ef.png)

## Forensic Question 2
- I checked for cronjobs logged in /var/spool/cron/crontabs/ratman with `nano /var/spool/cron/crontabs/ratman` and noticed it was executing the script /lib/.core/sabotage
- Since the sabotage script is a binary, I used strings to list all readable text and parse machine code
```
strings /lib/.core/sabotage
#the last line outputted is the answer
```

![image](https://user-images.githubusercontent.com/69600390/285331604-36a4ff56-c886-4e9f-9d29-4ace71fb206a.png)

![image](https://user-images.githubusercontent.com/69600390/285331619-68147e68-32d8-4cf0-8405-30995be9d3b1.png)

## Forensic Question 4
- I noticed that the dump.mem file was also in an unreadable format, so I tried strings to list all readable content and since I knew I need to look for a YouTube link, I grepped out all YouTube links outputted
- Then I grepped out all links with `strings Desktop/dump.mem | grep https://` and went through any that seemed like they'd have a volunteer application form
```
#Ctrl+click to open links with browser
#and don't forget the period

strings Desktop/dump.mem | grep youtube.com
#the part after 'watch?v=' is the answer to the first question of the forensic

strings Desktop/dump.mem | grep https:// #Save yourself the hassle and just grep out all drive links with 'strings Desktop/dump.mem | grep drive.google.com'
#the form's 24th question's answer is the last option
```

![image](https://user-images.githubusercontent.com/69600390/285331814-d1b1e288-6846-48a7-a400-eec50a2a71a5.png)

![image](https://user-images.githubusercontent.com/69600390/285331831-2ca0096a-3f26-4b4d-b0c5-810d6e1d6266.png)

![image](https://user-images.githubusercontent.com/69600390/285331850-034eea38-be52-4ecb-88df-5e0f708fc9bb.png)

# Practice Scenario
## Screen Timeout Policy
- Open the gnome-control-center > Go to 'Privacy' > Then open 'Screen' > and then set 'Blank Screen Delay' to 5 minutes

![image](https://user-images.githubusercontent.com/69600390/285332247-0a3bfc23-72fe-45ec-a56e-c57e8e61f4aa.png)

## Automatic Screen Lock
- Open the gnome-control-center > Go to 'Privacy' > Then open 'Screen' > and turn on 'Automatic Screen Lock'

![image](https://user-images.githubusercontent.com/69600390/285332293-f890ca09-0840-4a5e-85b2-cf3fc06c3743.png)

## Auto Updates
(Enabling Repos doesn't give points but they are needed to get the later points)
- Open software-properties-gtk > Make sure only the first 4 sources are checked > Go to updates and switch 'Automatically check for updates' to 'Daily'

![image](https://user-images.githubusercontent.com/69600390/285332781-b0b968fa-a58a-456d-ae00-3df979b8b2f7.png)

![image](https://user-images.githubusercontent.com/69600390/285332810-01815c5c-821e-4593-8df1-8a585326fc48.png)

## User Auditing (caroline)
```
#the -m creates the user's home directory
useradd -m caroline
```

![image](https://user-images.githubusercontent.com/69600390/285333108-7eee79ab-bdde-44ac-a5a7-05d3ef0a6f3a.png)

## Group Auditing and SSH (caroline)
```
adduser caroline aperturestaff

#this point was kind of iffy since you never acutally made ssh keys, but if you do need to in future situations you can use 'ssh-keygen'
mkdir /home/caroline/.ssh
```

![image](https://user-images.githubusercontent.com/69600390/285333661-2f0146a7-0f86-482b-a056-520ae5ba3ea8.png)

## User Auditing (cjohnson)
```
adduser cjohnson adm
adduser cjohnson sudo

#sudo is the superuser group (admin group of the highest privilege)
#adm is a much lower privileged admin group which allows members to monitor the system logs
```

![image](https://user-images.githubusercontent.com/69600390/285334171-af6df7d4-27ac-4621-8dff-d564757ead1c.png)

## Group Auditing (chell)
```
adduser chell testsubjects
```

![image](https://user-images.githubusercontent.com/69600390/285334617-923fdfc0-fc85-41c7-8583-7b84c91add40.png)

## Disable Automatic Login (ratman)
```
nano /etc/gdm3/custom.conf

and delete the follow lines (alt+delete):
	'AutomaticLogin=ratman'

and modify the following lines:
	'AutomaticLoginEnable=True' -> 'AutomaticLoginEnable=False'

and then 'Ctrl+S', 'Ctrl+X'
```

![image](https://user-images.githubusercontent.com/69600390/285334843-844fc45e-a91b-45a4-93f8-e2e024c1bf47.png)

## User Auditing (wheatley)
```
#the -r deletes the user's mail spool and all files in their home directory
userdel -r wheatley
```

![image](https://user-images.githubusercontent.com/69600390/285334962-f8337ae7-4fba-4dd7-9b30-a6a2e5250f75.png)

## User Auditing (chell)
```
deluser chell adm
deluser chell sudo

deluser chell lpadmin
#lpadmin allows for management of printers and printing jobs by other users
#but it's weird how chell needs to be removed from lpadmin when the scoring config only checks for adm and sudo
```

![image](https://user-images.githubusercontent.com/69600390/285335268-78b09e0f-7c6a-4778-b648-a3c0995859bb.png)

## User Auditing (companioncube)
```
passwd companioncube
```

![image](https://user-images.githubusercontent.com/69600390/285335436-c0e30a43-e254-4014-8474-edbedf6bc5aa.png)

## User Auditing (root)
```
sudo passwd -l root
```

## Prohibited Files (mp3)
```
find / -iname *.mp3 -delete
```

![image](https://user-images.githubusercontent.com/69600390/285335635-54eef342-ecfc-468d-a986-d63aa0dccad8.png)

## Enabling Firewall (UFW)
```
apt-get install ufw -y
ufw enable
```

![image](https://user-images.githubusercontent.com/69600390/285335851-c7f81603-81e2-432f-9bf1-fde7ddb0e9ff.png)

## Service Auditing (NFS)
```
apt-get purge nfs* -y
```

![image](https://user-images.githubusercontent.com/69600390/285337080-29868ca6-18a3-4fc2-b0aa-a0b24b9d7837.png)

## Service Auditing (NGINX)
```
apt-get purge nginx* -y
```

![image](https://user-images.githubusercontent.com/69600390/285337182-9d52c8ca-dd3e-45e2-99b3-98a76808ce32.png)

## Service Auditing (Apache)
```
systemctl enable apache2
systemctl start apache2
```

![image](https://user-images.githubusercontent.com/69600390/285337422-22e50db3-92f9-4618-8330-1f5ffab079b7.png)

## Firewall Auditing (Apache)
```
ufw app list
ufw allow 'Apache Secure'
#conf is located in /etc/ufw/applications.d/
```

![image](https://user-images.githubusercontent.com/69600390/285337522-bfa4c175-b7df-4f7c-8add-a020a4e430c7.png)

## Service Auditing (SSH)
```
systemctl enable ssh
systemctl start ssh
```

![image](https://user-images.githubusercontent.com/69600390/285337598-42cfa9dd-586c-4b0d-b589-e052b09818b6.png)

## Insecure sudoers rule
```
visudo

Comment or remove the following line:
	'gman ALL=(ALL:ALL) NOPASSWD: ALL'
```

![image](https://user-images.githubusercontent.com/69600390/285337827-aea04b49-4b74-4643-8994-1721bb5869a7.png)

## Kernel Settings (Enable IPv4 TIME WAIT ASSASSINATION)
```
nano /etc/sysctl.conf

Add/Modify the following line:
	'net.ipv4.tcp_rfc1337 = 1'
```

![image](https://user-images.githubusercontent.com/69600390/285338003-eb8b97fc-37bb-4b54-97a3-a7bfc94394ec.png)

## Kernel Settings (Enable IPv4 TCP SYN cookies)
```
nano /etc/sysctl.conf

Add/Modify the following line:
	'net.ipv4.tcp_syncookies = 1'
```

![image](https://user-images.githubusercontent.com/69600390/285338509-22e03baf-f941-443e-9c5d-19a515e04a85.png)

## Kernel Settings (Disable IPv4 forwarding)
```
nano /etc/sysctl.conf

Add/Modify the following line:
	'net.ipv4.ip_forward=0'
```

![image](https://user-images.githubusercontent.com/69600390/285338722-a18c4a31-2332-47ee-a8f9-70b0624c5bfd.png)

## Kernel Settings (Disable Source Routing)
```
nano /etc/sysctl.conf

Add/Modify the following line:
	'net.ipv4.conf.all.accept_source_route = 0'
	'net.ipv4.conf.default.accept_source_route = 0'
```

![image](https://user-images.githubusercontent.com/69600390/285625662-81ed5318-fd17-4916-ac4e-a9d61821c8dd.png)

## Kernel Settings (Ignore Send Redirects)
```
nano /etc/sysctl.conf

Add/Modify the following line:
	'net.ipv4.conf.all.send_redirects = 0'
	'net.ipv4.conf.default.send_redirects = 0'
```

![image](https://user-images.githubusercontent.com/69600390/285625783-f0313129-85e6-441c-bd41-27658c9aa741.png)

## Kernel Settings (Enable Martian Packet Logging)
```
nano /etc/sysctl.conf

Add/Modify the following line:
	'net.ipv4.conf.all.log_martians = 1'
```

![image](https://user-images.githubusercontent.com/69600390/285625801-6e8fde45-d931-472a-8df9-ff10ea60a0e2.png)

## Update Kernel
```
apt-get install linux-image-unsigned-6.2.0-26-generic -y
reboot
```

![image](https://user-images.githubusercontent.com/69600390/285625816-c2821c14-1efa-48d9-b48b-a1c0535e2681.png)

![image](https://user-images.githubusercontent.com/69600390/285625817-16e589ff-171b-4ea3-b3ef-e43491bb6b2e.png)

## Kernel Settings (Enable Source Address Verification)
```
nano /etc/sysctl.conf

Add/Modify the following line:
	'net.ipv4.conf.all.rp_filter = 1'
	'net.ipv4.conf.default.rp_filter = 1'
```

![image](https://user-images.githubusercontent.com/69600390/285625833-f215c26d-870d-4379-aee1-37d76c725b68.png)

## Kernel Settings (Ignore ICMP Redirects)
```
nano /etc/sysctl.conf

Add/Modify the following line:
	'net.ipv4.conf.all.accept_redirects = 0'
	'net.ipv4.conf.default.accept_redirects = 0'
```

![image](https://user-images.githubusercontent.com/69600390/285625850-aa4d0d91-39ba-4c9f-9e8d-9ae1fae5bad8.png)

## Software Auditing (Ophcrack)
```
apt-get purge ophcrack* -y

#Microsoft Windows password cracker using rainbow tables
```

![image](https://user-images.githubusercontent.com/69600390/285625882-c8354c34-d884-48da-8836-37e0f4e98c56.png)

## Software Auditing (Hydra)
```
apt-get purge hydra* -y

#very fast network logon cracker (It's true, I used it for hacking)
#used this on my one and only science fair and I won lol (presentation was made about one command so it only took a day 💀)
```

![image](https://user-images.githubusercontent.com/69600390/285625918-e47f0a0b-d294-40b7-a7a9-f8673d995b3b.png)

## Software Auditing (JohnTheRipper)
```
apt-get purge john* -y

#active password cracking tool (It's great, I've also used this before)
```

![image](https://user-images.githubusercontent.com/69600390/285625958-3a9055af-ca64-415e-bb7d-bcb79597e2e7.png)

## Software Auditing (Nmap)
```
apt-get purge nmap* -y

#The Network Mapper
```

![image](https://user-images.githubusercontent.com/69600390/285626001-1b503d76-8a85-4cbc-ae30-9d646bd5b581.png)

## Software Auditing (Snort)
```
apt-get purge snort* -y

#flexible Network Intrusion Detection System
```

![image](https://user-images.githubusercontent.com/69600390/285626012-fd2cea7b-ebef-4d98-9099-05a24accf45d.png)

## Software Auditing (Wireshark)
```
apt-get purge wireshark* -y

#network traffic analyzer
```

![image](https://user-images.githubusercontent.com/69600390/285626026-1f3861aa-b00c-4038-a3cb-14bad7490a35.png)

## Firefox Settings (Enable Block Dangerous and Deceptive Content)
```
Open firefox > Settings > Privacy & Security > Scroll down until 'Security' > and turn on 'Block dangerous and deceptive content'
```

![image](https://user-images.githubusercontent.com/69600390/285626122-1e647340-5676-4e9f-a173-8db9e2d3c121.png)

## Disable SSH Root Login
```
nano /etc/ssh/sshd_config

Change the following line:
	'PermitRootLogin yes' -> 'PermitRootLogin no'
```

![image](https://user-images.githubusercontent.com/69600390/285626167-3e8b807e-2956-4303-a5ab-3072cc4e6f9f.png)

## Prohibited Files (Cronjob)
```
rm -f /lib/.core/*
```

![image](https://user-images.githubusercontent.com/69600390/285626197-0ede1310-37be-4209-b522-2b05038ddd43.png)

## Backdoor (PAM)
```
#Removing the PAM backdoor we found and used to answer the 
nano /etc/pam.d/common-auth

Comment or remove the following line:
	'auth sufficient pam_exec.so expose_authtok quiet /lib/x86_64-linux-gnu/security/pam-bd'
```

![image](https://user-images.githubusercontent.com/69600390/285626216-2349d9a0-4663-4c29-bdce-b2771e75a0cd.png)

## PAM (Account Lockout Policy)
```
nano /etc/pam.d/common-auth

Add or modify the following line:
	'auth [default=die] pam_faillock.so authfail'
```

## PAM (Secure Maximum Password Age)
```
nano /etc/login.defs

Change the following line:
	'PASS_MAX_DAYS 99999' -> 'PASS_MAX_DAYS 30'
```

## PAM (Secure Minimum Password Age)
```
apt-get install libpam-* -y
nano /etc/security/pwquality.conf

Add or modify the following line:
	'minlen=8' (or higher)
```

## PAM (Credit Complexity Checks)
```
nano /etc/security/pwquality.conf

Add or modify the following lines:
	'dcredit=-1'
	'ucredit=-1'
	'lcredit=-1'
	'ocredit=-1'
```

## PAM (Enable Dictionary Check)
```
nano /etc/security/pwquality.conf

Add or modify the following lines:
	'dictcheck=1'
```

## PAM (Enable Username Check)
```
nano /etc/security/pwquality.conf

Add or modify the following lines:
	'usercheck=1'
```

## PAM (SHA512 Encryption)
```
nano /etc/login.defs

Change the following line:
	'ENCRYPT_METHOD MD5' -> 'ENCRYPT_METHOD SHA512'
```

## PAM (Secure Number of Login Retries)
```
nano /etc/login.defs

Edit the following:
	'LOGIN_RETRIES 9999' -> 'LOGIN_RETRIES 5'
```

## PAM (Secure History Policy)
```
nano /etc/pam.d/common-password

Append 'remember=13' to the end of the line containing 'pam_unix.so'
```

## Software Auditing (Chrome)
```
apt-get purge google-chrome-stable -y
```

## GRUB Settings (Enable Signature Checks)
```
nano /etc/grub.d/40_custom

Add the following lines:
	'set check_signatures=enforce'
	'export check_signatures'
```

## GRUB Settings (Unauthorized Superusers)
```
nano /etc/grub.d/40_custom

Delete the following lines:
	'set superusers="root wheatley"'
	'password wheatley EVIL'
	'password root aperture'
	'export superusers'
```

## File Auditing (/etc/shadow Permissions)
```
chmod 640 /etc/shadow
```

## File Auditing (/etc/passwd Permissions)
```
chmod 644 /etc/passwd

#but it isn't needed because if you do any user auditing (any user modding that calls to /etc/passwd) it'll reset back to default perms (644)
```

## File Auditing (/usr/bin/cp Permissions)
```
chmod a-s /usr/bin/cp
```

## Insecure /etc/hosts Entries
```
nano /etc/hosts

Delete the following lines:
	'127.0.0.1 google'
	'127.0.0.1 duckduckgo'
```

## Configuring SSH (Disable Password Authentication)
```
#the creator of the image didn't configure this properly in the scoring engine though, so you won't get points
#and I've already told him about all misconfigured parts of his image
nano /etc/ssh/sshd_config

Edit the following line:
	'PasswordAuthentication yes' -> 'PasswordAuthentication no'
```

## Configuring SSH (Port 1382)
```
nano /etc/ssh/sshd_config

Change the following line:
	'#Port 22' -> 'Port 1382'
```

## Configuring SSH (Deny testsubjects)
```
nano /etc/ssh/sshd_config

Add the following line:
	'DenyGroups=testsubjects'
```

## Configuring Apache (Response Header)
```
nano /etc/apache2/conf-enabled/security.conf

Change the following line:
	'ServerTokens Full' -> 'ServerTokens Prod'
```

## Configuring Apache (Server Signature)
```
nano /etc/apache2/conf-enabled/security.conf

Change the following line:
	'ServerSignature On' -> 'ServerSignature Off'
```

## Firewall Auditing (OpenSSH)
```
ufw allow 'OpenSSH'
ufw reload
```

## Kernel Settings (Disable IPv6)
```
nano /etc/sysctl.conf

Add/Modify the following line:
	'net.ipv6.conf.all.disable_ipv6 = 1'
	'net.ipv6.conf.default.disable_ipv6 = 1'
```

## Prohibited Files (Malicious Configuration File in APT)
```
rm -f /etc/apt/apt.conf.d/99autotrust
```

## Service Auditing (FTP)
```
systemctl enable vsftpd
systemctl start vsftpd
```

## Firewall Auditing (FTP)
```
ufw allow 'vsftpd'
ufw reload
```

## Prohibited Filse (Malicious PPA Repository)
```
rm -f /etc/apt/sources.list.d/persepolis-ubuntu-ppa-jammy.list
```

## Configuring FTP (Anonymous FTP)
```
nano /etc/vsftpd.conf

Add/Modify the following line:
	'anonymous_enable=NO'
```

## Firewall Auditing (Incoming)
```
ufw default deny incoming
ufw reload
```

## Configuring FTP (Chroot Users)
```
nano /etc/vsftpd.chroot_list

Delete all existing lines and add the following:
	'testsubjects'
```

## Service Auditing (CUPS)
```
apt-get purge cups* -y
```

## Service Auditing (Avahi)
```
apt-get purge avahi* -y
```

## Configuring FTP (TLS)
```
nano /etc/vsftpd.conf

Add/Modify the following line:
	'ssl_enable=YES'
	'ssl_tlsv1=YES'
	'ssl_sslv2=YES'
	'ssl_sslv3=YES'
```

## ACLs (testsubjects)
```
setfacl -m g:testsubjects:--- /home/glados
```

## ACLs (blackmesa)
```
setfacl -m g:blackmesa:--- /var/www/aperture-apache-server/employees
```

## Configuring FTP (Anonymous TLS/SSL)
```
nano /etc/vsftpd.conf

Add/Modify the following line:
	'allow_anon_ssl=NO'
```

## Configuring FTP (Passive Port Range)
```
nano /etc/vsftpd.conf

Add the following lines:
	'pasv_enable=YES'
	'pasv_min_port=50000'
	'pasv_max_port=50100'

#and then allow those ports to pass through the firewall
ufw allow 50000:50100/tcp
ufw reload
```

## Display Manager Settings (gdm3 User and Group)
```
nano /etc/gdm3/custom.conf

Delete the following lines:
	'User=rick'
	'Group=rick'
```

## Display Manager Settings (gdm3- Disable TCP)
```
nano /etc/gdm3/custom.conf

Change the following line:
	'DisallowTCP=false' -> 'DisallowTCP=true'
```

## Disable Core Dumps
```
nano /etc/sysctl.conf

Add/Modify the following line:
	'fs.suid_dumpable=0'

nano /etc/security/limits.conf

Add/Modify the following line:
	'* hard core 0'
```

## Service Auditing (AppArmor)
```
systemctl enable apparmor
systemctl start apparmor
```

## Software Auditing (Firefox)
```
apt-get install firefox -y
```

## Prohibited Files (Keylogger Module)
```
rm -f /lib/modules/5.19.0-46-generic/updates/dkms/kisni.ko
```

## Kernel Settings (lockdown)
```
#update kernel version
apt-get install linux-image-generic -y
reboot

#because the kernel is what your system boots off of so unless you reboot it won't give you points
nano /etc/default/grub

Append the following into the value of the variable 'GRUB_CMDLINE_LINUX':
	'lockdown=integrity' or 'lockdown=confidentiality'
Ctrl+S > Ctrl+X

update-grub

#list the contents of /sys/kernel/security/lockdown with the following and make sure the output has square brackets around integrity or confidentiality
cat /sys/kernel/security/lockdown
```

## Service Auditing (Prohibited Service r00t)
```
rm -f /etc/systemd/system/r00t.service
systemctl daemon-reload
```

## Configuring Auditd (Local Events Logging)
```
nano /etc/audit/auditd.conf

Change the following line:
	'local_events = no' -> 'local_events = yes'
```

## Bash Aliases (GOODBYE)
```
nano /etc/bash.bashrc

Remove the following line:
	"alias GOODBYE=':(){ :|:& };:'"
```

## Process Limit (2500)
```
nano /etc/security/limits.conf

Modify the following line:
	'* hard nproc 99999' -> '* hard nproc 2500'
```

## User Auditing (syslog)
```
chsh -s /usr/sbin/nologin syslog
```

## File Auditing (/home)
```
chown root:root /home
```

## File Auditing (/swapfile)
```
chmod 600 /swapfile
#it won't give you points
#the scoring config is actually messed up and the creator is checking for perms of a file named 'sw apfile' in the root directory, if you want to get the points regardless then look below.
touch sw\ apfile
chmod 600 /sw\ apfile
```

## Kernel Settings (ASLR)
```
nano /etc/sysctl.conf

Add/modify the following line:
	'kernel.randomize_va_space = 1'
```

## Kernel Settings (ExecShield)
```
nano /etc/sysctl.conf

Add/modify the following line:
	'kernel.exec-shield = 1'
```

## Prohibited Files (Rootkit)
```
rm -f /etc/ld.so.preload /usr/src/evil_rabbit.so
```

## Prohibited Files (Malicious Script in PATH)
```
nano /usr/sbin/victim
rm -f /usr/sbin/victim
```
