> If you do not understand the engine, you will never know when it is being tampered with

> [!NOTE] Objective
> - Today I will understand:
> 	- What the Windows kernel is
> 	- What happens in Ring 0 vs Ring 3
> 	- How the OS Draws the line between userland and kernel mode
> 	- Why this distinction is life or death for defenders
## Explain Like I Am 12
`Think of your computer like a medievil kingdom`
- User-mode(Ring 3) is the marketplace
	- Programs like browsers, games, and apps run here
- Kernel-mode (Ring 0) is the throne room
	- It controls everything: memory, CPU, devices, files
- Cannot let peasents (programs) directly touch the crown jewels (kernel). That is why there is a wall between them
	- Breaking through that wall - via exploits or drivers - is how malware becomes king of your system
## Ring 0 [VS] Ring 3 - The CPU Enforces the Rules

| Ring   | Mode      | Who Runs Here                     | Privileges                             |
| ------ | --------- | --------------------------------- | -------------------------------------- |
| Ring 0 | Kernel    | Windows NT Kernel, Drivers        | Full access to hardware, memory, CPU   |
| Ring 3 | User Mode | Chrome, Word, Powershell, Malware | Limited, must ask kernel for resources |
- The CPU's privilege rings prevent apps from directly talking to memory or hardware
- System Calls (like NtOpenProcess) are how Ring 3 requests actions from Ring 0
## What Is the Kernel?
`The kernel is the core component of Windows that controls:`
- Memory Management
- Process Scheduling
- Device Drivers
- Security Enforcement
- Interrupt Handling
- System Calls API
Windows' kernel is called the Windows NT kernel (ntoskrnl.exe)
- It acts as the gatekeeper between user-mode apps and real hardware
## Major Components of The Windows Kernel

| Component                        | Purpose                                                      |
| -------------------------------- | ------------------------------------------------------------ |
| Executive                        | High-level kernel functions (I/O, memory, object management) |
| HAL (Hardware Abstraction Layer) | Makes drivers/platforms uniform accross systems              |
| Kernel Dispatcher                | Handels threads, IRGLs, context switching                    |
| Object Manager                   | Manages access to kernel objects                             |
| Security Reference Monitor (SRM) | Enforces ACLs and token-based access                         |
## User-Mode <---> Kernel-Mode Interaction Flow
`User App (ex. calc.exe) > Windows API (ex. CreateFile) > Native API (ntdll.dll --> NtCreateFile) > SYSCALL > Kernel Mode (ntoskrnl.exe)`
- SYSCALL interface is the critical attack surfaces. Malware tries to:
	- Hook it (to hide behavior)
	- Bypass it (direct kernel access)
	- Tamper with it (patch the syscall table)
## Detection Relevance

| Concept          | Relevance to Blue Team                                                       |
| ---------------- | ---------------------------------------------------------------------------- |
| Ring 0 VS Ring 3 | Alerts from Ring 0 (ex. kernel driver install are critical priority)         |
| Syscalls         | Many EDRs inject hooks here -- attackers evade detection by avoiding them    |
| SRM / Tokens     | Prvilege Escalation happens here. Logs like 4672 depend on these transitions |
| Driver Loading   | Unsigned drivers = rootkits = detection and alert trigger                    |
## Tools To Explore The Kernel

| WinDbg Preview        | Windows kernel debugger                     |
| --------------------- | ------------------------------------------- |
| Process Hacker        | View token privileges, loaded drivers       |
| DriverView            | See all active drivers, signed/unsigned     |
| PE-sieve / Detections | Detect injected or hooked processes         |
| Sysmon + ETW logs     | Surface kernel-mode behaviors via telemetry |
