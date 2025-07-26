Project FourEyes
Purpose and Scope
Project FourEyes is a curated collection of technical notes and deep‑dive write‑ups focused on Windows networking, file‑sharing and operating‑system internals. The repository is not a code base; it is a study guide. Each directory contains one or more Markdown documents that explore a core question – for example “What is SMB?”, “What are Windows network stacks?”, “What are DNS records?” and “What are Windows internals?”. The goal is to move beyond superficial definitions and develop an intuitive understanding of how these components work, how they can be abused, and how to defend against those abuses.

If your aim is to be a casual observer, this project is not for you. It assumes you are preparing for mental, technical and spiritual combat. You are expected to read, research, reproduce the experiments and, most importantly, think. When you finish a section, challenge yourself to explain it without notes. If you can’t, repeat the material until mastery is achieved. There are no participation trophies here; only hard‑won competence.

Repository Structure
The repository currently contains a single top‑level folder called What Is, under which each subfolder tackles a specific topic. Below is an overview:

01 – What Is SMB – Detailed notes on the Server Message Block (SMB) protocol. Topics include what SMB is, how authentication works (NTLM/Kerberos), which ports the protocol uses, the differences between SMB v1/v2/v3, and why SMB is frequently abused in internal breaches
raw.githubusercontent.com
. It includes tables summarizing ports and protocols
raw.githubusercontent.com
 and compares the security status of each version
raw.githubusercontent.com
. It also provides recommendations such as disabling SMB v1 and monitoring port 445 traffic
raw.githubusercontent.com
.

02 – What Are Windows Network Stacks – An exploration of how Windows transmits and receives packets. The notes map the 4‑layer TCP/IP model used in Windows onto the theoretical 7‑layer OSI model and explain the role of components such as Winsock, tcpip.sys, NDIS and the NIC
raw.githubusercontent.com
. They include step‑by‑step walkthroughs of resolving DNS queries, establishing TCP connections and the interplay between application and transport layers
raw.githubusercontent.com
. Later sections cover IP addressing, ARP/MAC resolution, DHCP leases, Windows Firewall and the routing table, emphasising both troubleshooting and cyber‑security implications
raw.githubusercontent.com
.

03 – What Are DNS Records – Notes on DNS record types, how Windows resolves names, common misconfigurations and their impact on both help‑desk troubleshooting and security operations. (Content forthcoming.)

04 – What Are Event Logs – Guides on the Windows event logging subsystem, key log channels, and how attackers and defenders can leverage or evade logs. (Content forthcoming.)

05 – What Are Windows Internals – High‑level notes on the structure of the Windows operating system, including processes, threads, memory management, kernel vs. user mode, registry architecture and the startup sequence. (Content forthcoming.)

06 – What Is a Windows Kernel – An introduction to the Windows kernel, its responsibilities and how kernel‑mode vulnerabilities differ from user‑mode issues. (Content forthcoming.)

Each folder contains multiple Markdown files. Some files labelled CaseBook summarise textbook knowledge. Others labelled Deep Dive or Detection Use Cases provide more advanced analysis, including how attackers abuse the feature and how defenders can detect malicious activity. There are also Questions documents intended as self‑tests. Tackle them only after you have studied the preceding material.

How to Use This Repository
Clone the repository – Use git clone or download the repository as a ZIP file if you prefer offline reading. Once cloned, you can navigate the directories and open the .md files with any Markdown reader.

Read systematically – Start with the lower‑numbered sections within each topic. For example, in 01 – What Is SMB, read the CaseBook first to build foundational knowledge, then proceed to the Deep Dive and Detection Use Cases. Use the Questions document to test comprehension.

Reproduce and experiment – Many sections include command examples (e.g., Get-SmbServerConfiguration, Get-NetIPConfiguration, wevtutil) and recommended tools (Wireshark, netstat, Splunk). Run these commands in a lab environment to see real output. Do not trust memory; verify everything.

Integrate offensive and defensive perspectives – The notes intentionally present both how attackers exploit a feature and how defenders can spot or mitigate those attacks. Challenge yourself to think like both a red‑team operator and a blue‑team analyst. For example, after learning about NTLM relay attacks
raw.githubusercontent.com
, practice configuring alerts for SMB guest logins and lateral movement
raw.githubusercontent.com
.

Update and contribute – If you discover inaccuracies or new insights, fork the repository, make your edits, and submit a pull request. Contributions should include references and maintain the high standard of clarity and depth.

Philosophy
This project embodies the belief that preparation matters. In an age of escalating cyber conflict, understanding the minutiae of protocols, stacks and kernels is not optional – it is mission‑critical. The notes deliberately juxtapose theoretical models with practical implementation details. They also highlight common weaknesses (e.g., SMBv1) and provide direct commands to disable or harden them
raw.githubusercontent.com
. Every section reflects a broader theme: know your terrain before you go to war.
