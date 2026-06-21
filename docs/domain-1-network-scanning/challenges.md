# Domain 1: Challenge Examples

## Challenge 1: Find Domain Controller DNS Name

**Question:** Conduct a comprehensive scan of the network to identify the DNS computer name of the Domain Controller.

**Answer Format:** AaaaaAaaa*AAAAAA*aaa

**Difficulty:** Easy  
**Estimated Time:** 10-15 minutes

### Solution Steps

1. Run aggressive NMAP scan with OS detection:
```bash
nmap -A -T4 192.168.1.0/24
```

2. Look for Windows domain controllers (usually port 389/LDAP, 445/SMB, 3389/RDP)

3. Use SMB enumeration to get domain info:
```bash
nmap --script smb-os-discovery.nse -p445 [DC_IP]
```

4. Computer name will be in the output

### Expected Output
```
| smb-os-discovery:
|   OS: Windows 2016 (Build 14393)
|   Computer name: DOMAINCONTROLLER01
|   Domain: ACMECORP
|   Workgroup: WORKGROUP
```

### Answer Format Notes
- Computer name is usually uppercase with numbers
- Do NOT include domain name
- Case-sensitive (DOMAINCONTROLLER01, not domaincontroller01)

---

## Challenge 2: Find IMAP Mercury Service Version

**Question:** Identify the version of the IMAP Mercury service running on a Windows development server.

**Answer Format:** N*NN (Example: 1.45)

**Difficulty:** Easy  
**Estimated Time:** 10 minutes

### Solution Steps

1. Scan all ports looking for IMAP (usually 143 or 993):
```bash
nmap -A -T4 -p- 192.168.1.0/24
```

2. Find IMAP port and run version detection:
```bash
nmap -sV -p 143 192.168.1.50
```

3. Output will show version information

### Expected Output
```
PORT    STATE SERVICE VERSION
143/tcp open  imap    Dovecot imapd 2.3.4 (Debian)
```

Or for Mercury:
```
PORT    STATE SERVICE VERSION
143/tcp open  imap    Mercury/32 4.62 IMAP4rev1
```

### Answer Format Notes
- Mercury version format: Major.Minor (e.g., 4.62)
- Do NOT include service name (just "4.62", not "Mercury 4.62")
- Match format exactly (N.NN pattern)

---

## Challenge 3: Enumerate Active Directory Users

**Question:** List all users found in the Active Directory of the target domain.

**Answer Format:** Multiple lines, one username per line

**Difficulty:** Medium  
**Estimated Time:** 15 minutes

### Solution Steps

1. Identify domain controller:
```bash
nmap -A -T4 192.168.1.0/24 | grep -i "domain\|ldap"
```

2. Run enum4linux for user enumeration:
```bash
enum4linux -U 192.168.1.5
```

3. Output will list all users found

### Expected Output
```
User: Administrator
User: Guest
User: krbtgt
User: DOMAINUSER1
User: DOMAINUSER2
User: ServiceAccount
```

### Answer Format Notes
- One username per line
- Include all discovered users
- Match case as shown in output

---

## Challenge 4: Find All Shared Resources

**Question:** Enumerate all SMB shares on the network and report accessible ones.

**Answer Format:** Share names, one per line

**Difficulty:** Medium  
**Estimated Time:** 15 minutes

### Solution Steps

1. Run comprehensive scan for SMB:
```bash
nmap -p445 --script=smb-enum-shares.nse 192.168.1.5
```

2. Check each discovered share:
```bash
smbclient -L 192.168.1.5
```

3. Note all shares (both system and user-created)

### Expected Output
```
	Share        Type     Comment
	----         ----     -------
	C$           STYPE_DISKTREE Default share
	IPC$         STYPE_IPC       Remote IPC
	Users        STYPE_DISKTREE  User home directories
	Documents    STYPE_DISKTREE  Shared documents
	BACKUP       STYPE_DISKTREE  System backups
```

### Answer Format Notes
- Report share names without quotes
- Include $ signs if present (C$, IPC$)
- One share per line

---

## Tips for Domain 1 Challenges

1. **Run scans early** - They take 5-10 minutes to complete
2. **Use multiple tools** - NMAP and enum4linux give complementary results
3. **Check port 445** - Windows shares use SMB on port 445
4. **Save results** - Redirect output to file: `nmap ... > results.txt`
5. **Review carefully** - Output sometimes has lots of information, search for key fields
6. **Case sensitivity** - Answer format matters (Windows vs WINDOWS)
7. **Answer format** - Always double-check format before submitting

---

## Common Pitfalls

- **Forgetting to use -sV** - Without version detection, you won't see service versions
- **Skipping high ports** - Services sometimes run on unusual ports (3307 for MySQL)
- **Not using -p-** - Default scan only checks top 1000 ports
- **Wrong case** - "admin" != "Admin"
- **Including extra info** - "192.168.1.5:445" should just be "192.168.1.5"

---

**Next:** Review [advanced.md](advanced.html) for deeper topics, or move to [Domain 2](../domain-2-system-hacking/)
