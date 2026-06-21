# CEH v13 Practical Exam - Site Structure

**Generated:** June 22, 2026  
**Theme:** Just the Docs (Professional Documentation)  
**Total Pages:** 30+  

---

## Complete Navigation Map

```
🏠 HOME (nav_order: 1)
   ├─ Title: Home
   ├─ Description: Complete open-book reference guide for CEH v13
   ├─ Features: Overview, domain links, exam strategy
   └─ URL: /

⚡ QUICK START (nav_order: 2, has_children: true)
   ├─ 📋 Index (nav_order: 0)
   │  ├─ Title: Quick Start Guide
   │  ├─ Content: Overview, exam facts, tips
   │  └─ Provides: Navigation to subsections
   │
   ├─ ⏱️ Time Strategy (nav_order: 1)
   │  ├─ Title: Time Management Strategy
   │  ├─ Content: 6-hour breakdown, challenge ordering
   │  └─ Sections: Timeline, strategy by type, time allocation
   │
   ├─ 🔧 Commands (nav_order: 2)
   │  ├─ Title: Critical Commands
   │  ├─ Content: 100+ essential tool commands
   │  └─ Covers: All 7 domains with examples
   │
   ├─ ✍️ Formatting (nav_order: 3)
   │  ├─ Title: Answer Formatting Guide
   │  ├─ Content: Exact format rules by domain
   │  └─ Includes: Real examples, testing checklist
   │
   ├─ 🌳 Decision Tree (nav_order: 4)
   │  ├─ Title: When Stuck Troubleshooting
   │  ├─ Content: Flowchart for problem-solving
   │  └─ Covers: All challenge types
   │
   └─ 🛠️ Tools (nav_order: 5)
      ├─ Title: Tools Cheat Sheet
      ├─ Content: Quick reference for 85+ tools
      └─ Format: One-liners by tool

🎯 DOMAINS (nav_order: 3, has_children: true)
   ├─ 📑 Overview (nav_order: 0)
   │  ├─ Title: Domains Hub
   │  ├─ Content: Domain table, study paths, tips
   │  └─ Features: Difficulty levels, tool lists, time estimates
   │
   ├─ 🔍 Domain 1: Network Scanning (nav_order: 1, has_children: true)
   │  ├─ README.md (Overview)
   │  ├─ commands.md (NMAP, SMB, SNMP, DNS)
   │  └─ challenges.md (Challenge examples)
   │
   ├─ 🔓 Domain 2: System Hacking (nav_order: 2, has_children: true)
   │  ├─ README.md (Overview)
   │  ├─ commands.md (Hydra, Metasploit)
   │  └─ challenges.md (Challenge examples)
   │
   ├─ 🕸️ Domain 3: Web Hacking (nav_order: 3, has_children: true)
   │  ├─ README.md (Overview)
   │  ├─ commands.md (SQLMAP, Nikto, Burp)
   │  └─ challenges.md (Challenge examples)
   │
   ├─ 🔐 Domain 4: Cryptography (nav_order: 4, has_children: true)
   │  ├─ README.md (Overview)
   │  ├─ commands.md (Hashcat, Steghide, DIE)
   │  └─ challenges.md (Challenge examples)
   │
   ├─ 📱 Domain 5: Mobile & IoT (nav_order: 5, has_children: true)
   │  ├─ README.md (Overview)
   │  ├─ commands.md (ADB, SQLite3)
   │  └─ challenges.md (Challenge examples)
   │
   ├─ 📊 Domain 6: Traffic Analysis (nav_order: 6, has_children: true)
   │  ├─ README.md (Overview)
   │  ├─ commands.md (Wireshark, Tshark)
   │  └─ challenges.md (Challenge examples)
   │
   └─ 📡 Domain 7: Wireless (nav_order: 7, has_children: true)
      ├─ README.md (Overview)
      ├─ commands.md (Aircrack-ng suite)
      └─ challenges.md (Challenge examples)

🛠️ TOOLS REFERENCE (nav_order: 4)
   ├─ Title: Tools Reference Hub
   ├─ Content: 85+ tools organized by category
   ├─ Categories:
   │  ├─ Network Reconnaissance (9 tools)
   │  ├─ Credential Cracking (5 tools)
   │  ├─ Web Exploitation (6 tools)
   │  ├─ Cryptography & Analysis (8 tools)
   │  ├─ Packet Analysis (4 tools)
   │  ├─ Wireless (4 tools)
   │  ├─ Mobile & IoT (5 tools)
   │  ├─ System Tools (10+ tools)
   │  └─ Exploitation Frameworks (2 tools)
   │
   └─ Links to: TOOLS-COMPLETE-REFERENCE.md (existing file)
```

---

## File Location Map

### Root Level
```
/Users/easinarafat/Security/CEHv13 Practical Prep/
├─ _config.yml                          (UPDATED - Just the Docs config)
├─ Gemfile                              (NEW - Ruby dependencies)
├─ JEKYLL-MIGRATION-SUMMARY.md          (NEW - This migration guide)
├─ SITE-STRUCTURE.md                    (NEW - Site structure doc)
├─ README.md                            (Existing - Project readme)
├─ .git/                                (Git repository)
└─ docs/                                (Documentation root)
```

### Documentation Structure
```
docs/
├─ index.md                             (UPDATED - Home page with frontmatter)
│
├─ quick-start/                         (NEW - Exam prep section)
│  ├─ index.md                          (NEW - Quick start overview)
│  ├─ time-strategy.md                  (NEW - 6-hour timeline)
│  ├─ critical-commands.md              (NEW - 100+ commands)
│  ├─ answer-formatting.md              (NEW - Format rules)
│  ├─ decision-tree.md                  (NEW - Troubleshooting)
│  └─ tools-cheat.md                    (NEW - Tool reference)
│
├─ domains/                             (NEW - Domains hub)
│  └─ index.md                          (NEW - All domains overview)
│
├─ domain-1-network-scanning/
│  ├─ README.md                         (UPDATED - Frontmatter added)
│  ├─ commands.md                       (Existing content)
│  └─ challenges.md                     (Existing content)
│
├─ domain-2-system-hacking/
│  ├─ README.md                         (UPDATED - Frontmatter added)
│  ├─ commands.md                       (Existing content)
│  └─ challenges.md                     (Existing content)
│
├─ domain-3-web-hacking/
│  ├─ README.md                         (UPDATED - Frontmatter added)
│  ├─ commands.md                       (Existing content)
│  └─ challenges.md                     (Existing content)
│
├─ domain-4-cryptography/
│  ├─ README.md                         (UPDATED - Frontmatter added)
│  └─ challenges.md                     (Existing content)
│
├─ domain-5-mobile-iot/
│  ├─ README.md                         (UPDATED - Frontmatter added)
│  └─ challenges.md                     (Existing content)
│
├─ domain-6-traffic-analysis/
│  ├─ README.md                         (UPDATED - Frontmatter added)
│  └─ challenges.md                     (Existing content)
│
├─ domain-7-wireless/
│  ├─ README.md                         (UPDATED - Frontmatter added)
│  └─ challenges.md                     (Existing content)
│
├─ reference/
│  └─ index.md                          (NEW - Tools reference hub)
│
├─ tools-reference/
│  └─ TOOLS-COMPLETE-REFERENCE.md       (Existing - Preserved)
│
├─ challenges/
│  └─ all-challenges.md                 (Existing - Preserved)
│
├─ QUICK-START.md                       (Existing - Legacy, replaced by quick-start/)
└─ COMPLETION-REPORT.md                 (Existing - Preserved)
```

---

## Page Hierarchy Details

### Level 1: Main Sections (Top Navigation)
1. Home (nav_order: 1)
2. Quick Start (nav_order: 2)
3. Domains (nav_order: 3)
4. Tools Reference (nav_order: 4)

### Level 2: Quick Start Subsections
- Time Management Strategy (nav_order: 1)
- Critical Commands (nav_order: 2)
- Answer Formatting (nav_order: 3)
- Decision Tree (nav_order: 4)
- Tools Cheat Sheet (nav_order: 5)

### Level 2: Domains Subsections
- Domain 1: Network Scanning (nav_order: 1)
- Domain 2: System Hacking (nav_order: 2)
- Domain 3: Web Hacking (nav_order: 3)
- Domain 4: Cryptography (nav_order: 4)
- Domain 5: Mobile & IoT (nav_order: 5)
- Domain 6: Traffic Analysis (nav_order: 6)
- Domain 7: Wireless (nav_order: 7)

### Level 3: Domain Details (if expanded)
- Commands
- Challenges

---

## Content Summary by Page

| Section | Page | Type | Pages | Content |
|---------|------|------|-------|---------|
| Home | index.md | Landing | 1 | Overview, exam facts, navigation |
| Quick Start | index.md | Hub | 1 | Exam prep overview |
|  | time-strategy.md | Guide | 1 | 6-hour timeline |
|  | critical-commands.md | Reference | 1 | 100+ commands |
|  | answer-formatting.md | Guide | 1 | Format rules by domain |
|  | decision-tree.md | Flowchart | 1 | Troubleshooting |
|  | tools-cheat.md | Reference | 1 | Tool quick ref |
| Domains | index.md | Hub | 1 | All domains overview |
|  | domain-*/README.md | Overview | 7 | Domain introductions |
|  | domain-*/commands.md | Reference | 6 | Command syntax |
|  | domain-*/challenges.md | Examples | 7 | Challenge solutions |
| Tools Reference | index.md | Hub | 1 | Tools categorized |
|  | TOOLS-COMPLETE-REFERENCE.md | Reference | 1 | Complete tool guide |
| Other | docs/challenges/all-challenges.html | Index | 1 | All challenges |
|  | COMPLETION-REPORT.md | Report | 1 | Study progress |
| **TOTAL** | | | **30+** | Complete exam guide |

---

## Navigation Features

### Automatic (Just the Docs)
✅ Breadcrumb navigation (example: Home > Quick Start > Critical Commands)  
✅ Sidebar navigation (hierarchical, collapsible)  
✅ Search functionality (Ctrl+F in browser)  
✅ Back to top button  
✅ Heading anchor links  
✅ Parent-child relationship visualization  

### Manual Navigation
- Home page has links to all main sections
- Quick Start overview has links to all subsections
- Domains overview has table and links to each domain
- Each domain has links to its subsections
- Tools reference has categorized links

---

## Search Index

The search will index:
- Page titles (all)
- Page descriptions (all)
- Content headings (level 2+)
- Body text (all markdown)

**Common searches that will work:**
- "network scanning" → finds Domain 1
- "NMAP commands" → finds Domain 1 + Quick Start
- "hash cracking" → finds Domain 4 + Quick Start
- "answer format" → finds Answer Formatting page
- "when stuck" → finds Decision Tree page
- "aircrack-ng" → finds Domain 7 + Tools Reference
- Any domain number, tool name, or command

---

## Mobile Experience

✅ Responsive design (auto-adapts to phone/tablet)  
✅ Touch-friendly navigation  
✅ Search works on mobile  
✅ Readable font sizes  
✅ Proper code block wrapping  
✅ Table scrolling support  

---

## Accessibility

✅ Semantic HTML structure  
✅ Proper heading hierarchy (h1, h2, h3)  
✅ Alt text ready (for images)  
✅ High contrast light theme  
✅ Keyboard navigation support  
✅ Screen reader compatible  

---

## Theme Configuration

**Theme:** just-the-docs/just-the-docs  
**Version:** 0.10.1  
**Color Scheme:** light  
**Features Enabled:**
- Search: Yes
- Breadcrumbs: Yes
- Back to top: Yes
- Heading anchors: Yes
- SEO tags: Yes
- GitHub metadata: Yes
- Syntax highlighting: Yes (Rouge)

---

## Deployment URLs

### Local Development
```
http://localhost:4000
http://localhost:4000/quick-start/
http://localhost:4000/domains/domain-1-network-scanning/
```

### GitHub Pages
```
https://mrx-arafat.github.io/mrx-arafat-CEHv13-Practical-Prep
https://mrx-arafat.github.io/mrx-arafat-CEHv13-Practical-Prep/quick-start/
https://mrx-arafat.github.io/mrx-arafat-CEHv13-Practical-Prep/domains/
```

---

## Quick Navigation References

### From Home Page
- Quick Start: Direct link to exam prep
- Pick a Domain: Links to each domain 1-7
- Tools Reference: Complete tool documentation
- Domain Overview: All domains at a glance

### From Any Page
- Breadcrumbs: Navigate back up hierarchy
- Sidebar: Jump to any section
- Search: Find any content instantly
- Back to top: Jump to page header

---

## Summary

- **30+ pages** of professional documentation
- **4 main sections** in top navigation
- **7 domains** with 2-3 subsections each
- **6 quick-start** exam prep pages
- **85+ tools** documented
- **100+ commands** with syntax
- **20 challenges** explained
- **3-level** navigation hierarchy
- **Full search** functionality
- **Professional** light theme

The site is optimized for:
- ✅ Study before exam
- ✅ Reference during exam
- ✅ Quick command lookup
- ✅ Troubleshooting when stuck
- ✅ Format verification
- ✅ Decision-making guidance

---

**Status:** Complete and ready for deployment ✅
