# Project FourEyes: Purpose and Scope

## 1. Mission Statement

**Project FourEyes** is a curated archive of technical notes and deep-dive write-ups focused on Windows networking, file sharing, and operating system internals.

- **Not a code base:** This is a study guide for serious practitioners, not casual readers.
- **Format:** Each directory answers a core technical question (e.g., *What is SMB?*, *What are Windows network stacks?*).
- **Objective:** Achieve operational understanding of how Windows components function, how they are attacked, and how to defend them.

> **Mindset Required:**  
> If you are here to skim, leave now. This project is for those preparing for technical, mental, and spiritual combat.
>
> - Read, research, and reproduce every experiment.
> - Explain material from memory—if you can’t, repeat until you can.
> - There are no participation trophies. Competence is earned, not given.

---

## 2. Repository Structure

- **Top-level folder:** `What Is`
- **Each subfolder:** Specific, focused technical topic.

### Subfolder Overview

| Section                               | Description                                                                                                                                |
|----------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| **01 – What Is SMB**                   | Detailed SMB protocol notes: authentication (NTLM/Kerberos), ports, protocol versions, common abuse. Includes actionable recommendations.  |
| **02 – What Are Windows Network Stacks**| Maps Windows TCP/IP model to OSI. Explains Winsock, tcpip.sys, NDIS, NICs, DNS, DHCP, routing, troubleshooting, security implications.      |
| **03 – What Are DNS Records**          | DNS record types, Windows name resolution, common misconfigs, help-desk and security implications. *(Content forthcoming.)*                |
| **04 – What Are Event Logs**           | Windows event log subsystem, key channels, attacker/defender use. *(Content forthcoming.)*                                                 |
| **05 – What Are Windows Internals**    | OS structure: processes, threads, memory, registry, startup. *(Content forthcoming.)*                                                      |
| **06 – What Is a Windows Kernel**      | Kernel responsibilities, kernel- vs. user-mode vulns. *(Content forthcoming.)*                                                             |

#### File Types per Section

- **CaseBook:** Textbook fundamentals
- **Deep Dive / Detection Use Cases:** Advanced/attack/defense material
- **Questions:** Self-tests (attempt only after you’ve mastered the section)

---

## 3. How to Use

1. **Clone or download:**  
    - `git clone` the repository, or download as ZIP for offline access.

2. **Study order:**  
    - Start with lowest-numbered section in each folder.
    - Example: Read CaseBook first, then Deep Dive, then Detection Use Cases.
    - Use Questions to verify mastery.

3. **Experiment:**  
    - Run all commands (`Get-SmbServerConfiguration`, `netstat`, `wevtutil`, etc.) in a lab environment.
    - Do not trust memory—**verify everything.**

4. **Offense and defense:**  
    - Every topic shows both red team and blue team perspectives.
    - Think like an attacker *and* a defender.
    - Example: Study NTLM relay attacks, then create/monitor alerts for SMB guest logins and lateral movement.

5. **Contribute:**  
    - Fork, edit, and submit pull requests with corrections or new insights.
    - Every contribution must meet the same standards: depth, clarity, references.

---

## 4. Philosophy

**Preparation is not optional.**

- Cyber conflict escalates; shallow knowledge is a liability.
- Every note connects theory and practical commands, real weaknesses, and actionable fixes.
- If you don’t know your terrain, you will lose the war.

---

## 5. TLDR

- **Project FourEyes** is for committed practitioners only.
- **Study, apply, repeat**—no shortcuts, no ego, only earned mastery.
- If you’re not willing to do the work, walk away.

---

## 6. Abbreviations (Reference)

| Term    | Meaning                                   |
|---------|-------------------------------------------|
| SMB     | Server Message Block                      |
| OSI     | Open Systems Interconnection (model)      |
| NTLM    | NT LAN Manager (authentication protocol)  |
| NDIS    | Network Driver Interface Specification    |
| NIC     | Network Interface Card                    |
| ARP     | Address Resolution Protocol               |
| DHCP    | Dynamic Host Configuration Protocol       |
| DNS     | Domain Name System                        |
| OS      | Operating System                          |
| ZIP     | Compressed file archive format            |

---

**If you want mastery, start now. If not, stop pretending.**
