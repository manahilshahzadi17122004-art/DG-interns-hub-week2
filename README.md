# Week 2 - Network Traffic Analysis
Intern: Manahil Shahzadi

## Lab Setup
- Kali: 192.168.56.102 (Wireshark, Nmap)
- Ubuntu: 192.168.56.101 (Apache)
- Windows 11: 192.168.56.1 (Event Viewer)
- Network: VirtualBox Host-only 192.168.56.0/24

## Folder Structure
Notes / Writeups / Findings / Captures / Cheat Sheets

## What I Did

### 1. Wireshark Capture
Ping, curl, nslookup captured on eth1
- SYN, SYN-ACK, ACK handshake
- HTTP GET and 200 OK
- DNS query
File: Captures/Task1_capture.pcapng

### 2. Nmap Scans
- -sn 192.168.56.0/24 = 4 hosts up
- -sV .101 = 80/tcp Apache 2.4.66
- -sS .101 = SYN scan
Filter: tcp.flags.syn==1 && tcp.flags.ack==0
This filter shows port scan pattern for Snort Week 3

### 3. Windows Events
4625 Failed Logon, 4624 Success Logon Type 3, 4720 New User, 4732 Added to Admin, 7045 Service Created, 1102 Log Cleared

### 4. PowerShell
Get-WinEvent for 4625 and export to CSV

## TryHackMe Rooms
Nmap, Live Host Discovery, Basic Port Scans, Windows Basics, Windows Fundamentals 1, Windows Fundamentals 2

## Key Learning
Normal traffic = SYN > SYN-ACK > ACK > FIN. Scan = many SYN only. 4625+4720+4732 = attack story.

## Tools Used
Wireshark, Nmap, Event Viewer, PowerShell, VirtualBox
