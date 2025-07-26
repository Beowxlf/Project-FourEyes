> The Source of ALL Visibility...


> [!NOTE] Objectives
> - Master Windows event Logging architecture
> - Understand log types, channels, and formats
> - Learn how to filter, extract, and correlate logs using Event Viewer, wevtutil, and Sysmon.

## Explain Like I Am 12
- Think of logs like a security camera system in a castle
- The guards (Windows components) write reports
- These reports go into different rooms (log channels)
- Some rooms are always open (security, System), and some are hidden unless turned on (like PowerShell logs)
- Your job is to find the right room, check the time stamps, and figure out what went wrong and who was involved
## Deep Dive: Anatomy of Windows Logging
#### Core Logging Channels

| Channel             | Purpose                                    |
| ------------------- | ------------------------------------------ |
| Security            | Authentication, privilege use, audit logs  |
| System              | OS-level events                            |
| Application         | Logs from user applications                |
| Setup               | OS install/updates                         |
| Forwarded Events    | Logs from other machines (via collector)   |
| Microsoft-Windows-* | Subsystem logs (PowerShell, Defender, WMI) |


