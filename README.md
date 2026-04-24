🧠 HTB Writeups — Juan Ignacio Ramiz Martinez
📊 Stats
Category	Count
Machines Completed	2
Writeups Published	2
Active Directory	1
Linux Targets	1
📌 About

This repository contains technical writeups of retired Hack The Box machines, focused on:

Real-world attack techniques
Clear methodology
Reproducible steps
Professional documentation

All activities were performed in legal and controlled environments.

🧭 Methodology

Each machine follows a structured offensive workflow:

Machine Information
Reconnaissance → Host discovery + Port scanning
Enumeration → Services, shares, directories
Exploitation → Initial foothold
Post-Exploitation → Privilege escalation
Summary → Attack path + flags
🖥️ Machines
🟢 Easy
#	Machine	OS	Category	Techniques	Link
01	Support	
	Active Directory	SMB enum · .NET reversing · XOR decrypt · LDAP · RBCD	📄 View Writeup
🟡 Medium
#	Machine	OS	Category	Techniques	Link
02	AirTouch	
	WiFi / Network / Web	SNMP · WPA · Aircrack-ng · Web exploit · Evil Twin	📄 View Writeup
🔴 Hard

Coming soon...

🧪 Techniques Covered
🔐 Active Directory
SMB Null Session
LDAP Enumeration
BloodHound Analysis
Resource-Based Constrained Delegation (RBCD)
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
⚙️ Tools Used

nmap · smbclient · netexec · impacket
evil-winrm · bloodhound · ldapsearch
aircrack-ng · airmon-ng · hashcat
burp suite · gobuster · wfuzz
python · bash

📁 Repository Structure

htb-writeups/
├── support/
│ └── index.html
├── AirTouch/
│ └── AirTouch.html
└── README.md

⚠️ Disclaimer

These writeups only cover retired Hack The Box machines.

Publishing solutions for active machines is a violation of HTB Terms of Service.
