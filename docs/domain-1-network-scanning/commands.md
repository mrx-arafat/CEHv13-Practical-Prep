---
layout: default
title: Commands
parent: Domain 1 - Network Scanning & Enumeration
grand_parent: Domains
nav_order: 1
---

# Domain 1: Commands Reference

## Host Discovery

```bash
# Basic ping scan
nmap -sn 192.168.1.0/24

# ARP ping
nmap -sP 10.10.55.0/24

# TCP SYN ping
nmap -sS -P 10.10.55.0/24

# UDP ping
nmap -sU -P 10.10.55.0/24

# Using netdiscover
netdiscover -i eth0 -r 192.168.1.0/24
```

## Port Scanning

```bash
# TCP Connect Scan
nmap -sT -v 192.168.1.5

# TCP SYN Scan (stealthy)
nmap -sS -v 192.168.1.5

# UDP Scan
nmap -sU -v 192.168.1.5

# Specific ports
nmap -p 22,80,443 192.168.1.5

# Port range
nmap -p 1-1000 192.168.1.5

# All ports
nmap -p- 192.168.1.5

# Fast scan (top 100)
nmap -F 192.168.1.5

# Aggressive scan
nmap -A -T4 192.168.1.5

# Show only open
nmap --open 192.168.1.5
```

## Service Version Detection

```bash
# Version detection
nmap -sV 192.168.1.5

# Comprehensive scan
nmap -A -T4 -p- 192.168.1.5

# OS detection
nmap -O 192.168.1.5

# Script scanning
nmap -sC 192.168.1.5
```

## SMB Enumeration

```bash
# OS discovery
nmap --script smb-os-discovery.nse -p445 192.168.1.5

# User enumeration
nmap --script smb-enum-users.nse -p445 192.168.1.5

# Share enumeration
nmap -p445 --script=smb-enum-shares.nse 192.168.1.5

# SMBClient - list shares
smbclient -L 192.168.1.5

# SMBClient - access share
smbclient //192.168.1.5/Documents

# SMBget - download
smbget -R smb://192.168.1.5/Documents

# Enum4linux - all
enum4linux -a 192.168.1.5

# Enum4linux - users only
enum4linux -U 192.168.1.5

# Enum4linux - shares only
enum4linux -S 192.168.1.5

# Enum4linux - groups
enum4linux -G 192.168.1.5

# Enum4linux - password policy
enum4linux -P 192.168.1.5

# Enum4linux - with credentials
enum4linux -u admin -p password123 -a 192.168.1.5
```

## SNMP Enumeration

```bash
# NMAP SNMP scan
nmap -sU -p 161 --script snmp-processes.nse 192.168.1.5

# SNMP-check
snmp-check 192.168.1.5

# Onesixtyone discovery
onesixtyone 192.168.1.0/24
```

## DNS Enumeration

```bash
# NSLookup - basic
nslookup target.com

# NSLookup - reverse lookup
nslookup 192.168.1.5

# Dig - basic
dig target.com

# Dig - zone transfer
dig @ns1.target.com target.com axfr

# Dig - all records
dig target.com ANY
```

## Combined Scanning Workflow

```bash
# Step 1: Find live hosts
nmap -sn 192.168.1.0/24 > alive_hosts.txt

# Step 2: Quick port scan
nmap -F -iL alive_hosts.txt

# Step 3: Detailed scan on interesting hosts
nmap -A -T4 -p- 192.168.1.5 > detailed_scan.txt

# Step 4: SMB enumeration
enum4linux -a 192.168.1.5 > smb_enum.txt

# Step 5: Review results
cat detailed_scan.txt
cat smb_enum.txt
```

---

**Remember:** Always run comprehensive scans early—they take time. Work on other challenges while scanning completes.
