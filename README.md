🧠 HTB Writeups — Juan Ignacio Ramiz Martinez
<p align="center"> <b>Offensive Security · Active Directory · Web Exploitation</b><br> Technical writeups of <b>retired Hack The Box machines</b> </p> <p align="center"> <img src="https://img.shields.io/badge/HTB-Writeups-red?style=for-the-badge&logo=hackthebox"> <img src="https://img.shields.io/badge/Focus-Offensive%20Security-black?style=for-the-badge"> <img src="https://img.shields.io/badge/Status-Active-green?style=for-the-badge"> </p>
📊 Stats
Category	Count
Machines Completed	2
Writeups Published	2
Active Directory	1
Linux Targets	1
📌 About

This repository contains technical writeups of retired Hack The Box machines.

✔ Real-world attack techniques
✔ Clear methodology
✔ Reproducible steps
✔ Professional documentation

All activities were performed in legal and controlled environments.

🧭 Methodology

Each machine follows a structured workflow:

1. Machine Information
2. Reconnaissance      → Host discovery + Port scanning
3. Enumeration         → Services, shares, directories
4. Exploitation        → Initial foothold
5. Post-Exploitation   → Privilege escalation
6. Summary             → Attack path + flags
🖥️ Machines
🟢 Easy
#	Machine	OS	Category	Techniques	Link
01	Support	Windows	Active Directory	SMB · .NET · XOR · LDAP · RBCD	📄 View
🟡 Medium
#	Machine	OS	Category	Techniques	Link
02	AirTouch	Linux	WiFi / Network / Web	SNMP · WPA · Aircrack-ng · Web · Evil Twin	📄 View
🔴 Hard

Coming soon...

🧪 Techniques Covered
🔐 Active Directory
SMB Null Session
LDAP Enumeration
BloodHound Analysis
RBCD Attack
Evil-WinRM
🌐 Web & Network
SNMP Enumeration
WPA Handshake Capture
Aircrack-ng
File Upload Bypass
Reverse Shells
Credential Harvesting
🧬 Binary Analysis
.NET Reversing
XOR Password Decryption
⚙️ Tools
nmap · smbclient · netexec · impacket
evil-winrm · bloodhound · ldapsearch
aircrack-ng · hashcat · burp suite
gobuster · wfuzz · python · bash
📁 Structure
htb-writeups/
├── support/
│   └── index.html
├── AirTouch/
│   └── AirTouch.html
└── README.md
⚠️ Disclaimer

These writeups only cover retired Hack The Box machines.

Publishing active machine solutions violates HTB Terms of Service.

📬 Contact
<p align="center"> <a href="mailto:juanignacioramizmartinez@gmail.com"> <img src="https://img.shields.io/badge/Email-Contact-red?style=for-the-badge&logo=gmail"> </a> <a href="https://github.com/Nacho-Sudo"> <img src="https://img.shields.io/badge/GitHub-Nacho--Sudo-black?style=for-the-badge&logo=github"> </a> <a href="https://app.hackthebox.com/users/2705371"> <img src="https://img.shields.io/badge/HackTheBox-Profile-green?style=for-the-badge&logo=hackthebox"> </a> </p>
<p align="center"> <b>“Break systems. Understand them. Secure them.”</b> </p>
