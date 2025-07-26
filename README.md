# Project FourEyes: Purpose and Scope

## 1. Overview

Project FourEyes is a curated collection of technical notes and deep-dive write-ups focused on Windows networking, file sharing, and operating system internals.

- **Not a code base:** This repository serves as a study guide.
- **Format:** Each directory answers a specific technical question (e.g., *What is SMB?*, *What are Windows network stacks?*).
- **Objective:** Develop a working understanding of how Windows components function, how they are targeted, and how to defend them.

---

## 2. Repository Structure

- **Top-level folder:** `What Is`
- **Subfolders:** Each covers a technical topic.

### Subfolder Overview

| Section                                 | Description                                                                                                                                |
|------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| **01 – What Is SMB**                     | SMB protocol, authentication (NTLM/Kerberos), ports, protocol versions, security considerations, and recommendations.                      |
| **02 – What Are Windows Network Stacks** | TCP/IP model in Windows, OSI mapping, Winsock, tcpip.sys, NDIS, NICs, DNS, DHCP, routing, troubleshooting, security implications.           |
| **03 – What Are DNS Records**            | DNS record types, Windows name resolution, common misconfigurations, troubleshooting, and security context. *(Content forthcoming.)*         |
| **04 – What Are Event Logs**             | Windows event logging subsystem, key log channels, attacker and defender perspectives. *(Content forthcoming.)*                              |
| **05 – What Are Windows Internals**      | Windows OS structure: processes, threads, memory, registry, startup sequence. *(Content forthcoming.)*                                      |
| **06 – What Is a Windows Kernel**        | Windows kernel responsibilities, kernel- vs. user-mode vulnerabilities. *(Content forthcoming.)*                                            |

#### File Types per Section

- **CaseBook:** Fundamentals
- **Deep Dive / Detection Use Cases:** Advanced analysis and practical scenarios
- **Questions:** Self-tests to assess comprehension

---

## 3. How to Use This Repository

1. **Clone or download:**  
    - Use `git clone` or download as ZIP for offline access.

2. **Suggested study order:**  
    - Begin with the lowest-numbered section in each folder.
    - Read CaseBook for foundational knowledge, then progress to Deep Dive and Detection Use Cases.
    - Use Questions documents for self-assessment.

3. **Lab work and validation:**  
    - Run provided commands (e.g., `Get-SmbServerConfiguration`, `netstat`, `wevtutil`) in a test environment to observe real output.

4. **Offensive and defensive perspectives:**  
    - Notes provide both attacker and defender viewpoints for each feature or protocol.

5. **Contributions:**  
    - Fork, edit, and submit pull requests for corrections or new content.
    - Contributions should maintain a high standard of clarity and completeness, with references where applicable.

---

## 4. Project Philosophy

- Technical depth and practical understanding are emphasized.
- Notes bridge theory and implementation, and address common misconfigurations or weaknesses.
- Recommendations for hardening and monitoring are included where relevant.

