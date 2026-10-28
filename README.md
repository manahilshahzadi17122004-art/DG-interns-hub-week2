# DG Interns Hub - Week 2
## Network Traffic Analysis, Nmap and Windows Logging

**Intern:** [Manahil Shahzadi]  |  **Deadline:** 30 October 2026, 11:59 PM IST

## 1. Goal
Read packets and Windows event logs so Week 3 (endpoint + Snort IDS work) makes sense.

## 2. Lab setup
| Machine | Role | IP |
|---|---|---|
| Kali Linux (VirtualBox) | Attacker / analyst (Wireshark 4.6.6, Nmap 7.99) | 192.168.56.102 |
| Ubuntu (VirtualBox) | Apache 2.4.66 target | 192.168.56.101 |
| Windows 11 laptop (host) | Windows target + Event Viewer / PowerShell | 192.168.56.1 |

VirtualBox: Adapter 1 = NAT, Adapter 2 = Host-only (192.168.56.0/24). Shared folder `/media/sf_captures` saves output straight into `Captures`.

## 3. Folder structure
```
DG Interns HUB/Week 2/
├── Notes/                 8 theory answers, one file each
├── Writeups/              6 TryHackMe rooms + 3 Wireshark sample captures
├── Findings/              screenshots (Wireshark, Nmap, Event Viewer, PowerShell)
├── Captures/              PCAP files + Nmap -oN / -oX output
├── Cheat Sheets/          Wireshark (20) and Windows Event ID (15)
├── Week2_Report.pdf
├── Week2_Presentation.pptx
└── README.md
```

## 4. Theory (Notes)
1. TCP three-way handshake, teardown and flags table
2. Protocol analysis table: DNS, HTTP, TLS, ARP, ICMP, SMB
3. Capture vs display filters (10 examples)
4. Nmap scan types: -sn, -sS, -sT, -sU, -sV, -O, -A
5. Windows basics: processes, services, registry, users/groups, UAC, Event Viewer logs
6. 15 key Security Event IDs
7. Logon types 2, 3, 4, 5, 7, 10, 11
8. Baseline and why it matters for a SOC analyst

## 5. Lab: TryHackMe (free)
Nmap, Live Host Discovery, Basic Port Scans, Windows Basics, Windows Fundamentals 1 and 2, plus 3 official Wireshark sample captures. One writeup per room in `Writeups/`.

## 6. Hands-on results
### Task 1 - Capture traffic
Apache on Ubuntu; ping, curl and nslookup from Kali while Wireshark captured on eth1.
- `Findings/01_icmp.png` - 4 echo requests / 4 replies
- `Findings/02_tcp_handshake.png` - SYN, SYN-ACK, ACK (frames 462-464), GET, 200 OK, FIN
- `Findings/03_http_get.png` - GET / HTTP/1.1
- `Findings/04_dns.png` - DNS query for google.com
- `Captures/Task1_capture.pcapng`

### Task 2 - Nmap
| Scan | Result |
|---|---|
| -sn 192.168.56.0/24 | 4 hosts up (.1, .100, .101, .102) in 10.02 s |
| -sV .101 | 80/tcp open, Apache httpd 2.4.66 (Ubuntu) |
| -O, -A .101 | OS and service detail (see output files) |
| -sS .101 | SYN scan captured in Wireshark |
| -sS -sV .1 | Windows host scan |

Filter used on the SYN scan: `tcp.flags.syn==1 && tcp.flags.ack==0` -> many SYNs from one source to many ports in a fraction of a second. This is the pattern Snort will detect in Week 3.
Files: `Captures/nmap_*.txt` (-oN), `nmap_*.xml` (-oX), `nmap_syn_scan.pcapng`.

### Task 3 - Windows events (Windows 11 laptop)
4625 failed logon, 4624 successful logon (Logon Type 3), 4720 new user, 4732 added to Administrators, 7045 service created/deleted (System log), 1102 log cleared. Screenshots `Findings/11_event_4625.png` to `16_event_1102.png`.

### Task 4 - PowerShell hunting
```powershell
$ev = Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4625}
$ev | Select TimeCreated, Id, @{n='Account';e={$_.Properties[5].Value}} | Export-Csv failed_logons_4625.csv -NoTypeInformation
Import-Csv failed_logons_4625.csv | Group-Object Account | Sort-Object Count -Descending | Format-Table Count, Name
```
Screenshot `Findings/17_powershell_4625.png`, CSV in `Captures/`.

## 7. Cheat sheets
- Wireshark Filter - Meaning - When to Use It (20 entries)
- Windows Event ID - What It Means - When to Use It (15 entries)

## 8. Key learnings
- A normal TCP session is SYN, SYN-ACK, ACK ... FIN/ACK; scans break that pattern.
- Display filters turn thousands of packets into one readable story.
- 4625 + 4624 + 4720 + 4732 in sequence is a compromise story; 1102 means someone is hiding it.
- Knowing the baseline is what makes anomalies visible.

## 9. LinkedIn
Concept post "What a port scan looks like in Wireshark" and the weekly post (PCAP + Event Viewer screenshots, tagging DG Interns Hub).

## 10. Rules followed
No late submission, own words (no plagiarism), only free tools.
