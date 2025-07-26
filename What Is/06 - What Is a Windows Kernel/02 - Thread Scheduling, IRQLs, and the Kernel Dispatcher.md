`If you wish to detect stealth, you must understand how the CPU decides who get to speak."`

> [!NOTE] Objective
> - Today I will learn:
> 	- How the Windows kernel schedules threads
> 	- What IRQLs are and why they matter for rootkits and defenders
> 	- How Scheduling, Preemption, and priority determine execution order
> 	- Why malware that manipulates thread priorities or interupts is dangerous
## Explain Like I Am 12
`Imagine the CPU is a restraunt kitchen. Every app is a customer with an order`
- The kernel's dispatcher is the head chef - it decides what thread gets cooked (executed) next
- Orders (threads) are prioritized: some are urgent (interrupts), and some are regular.
- The Interrupt Request Level is like a "don't interrupt me" flag - higher levels get first access to the stove
Malware can abuse this system to cut in line, block defenders, or execute before security tools can even respond

---
## Key Concepts

#### What is a thread?
- A thread is the smallest unit of execution in Windows
- Every process has at least one thread.
- Thread context includes: registers, stack, instruction pointer, etc.
#### What is Thread Scheduling
- Windows uses a preemptive, priority-based round robin scheduler
- The Kernel Dispatcher decides what thread runs based on:
	- Priority class (realtime, high, normal, low)
	- Whether the thread is blocked or ready
	- CPU affinity and core availability
- If 2 threads are equal, the kernel alternates them - unless one gets elevated priority
#### What is IRQL (Interrupt Request Level)?

| IRQL Level   | Meaning                     | Used By                 |
| ------------ | --------------------------- | ----------------------- |
| 0 (passive)  | Normal thread execution     | Apps, most drivers      |
| 1 (APC)      | Async Procedure Calls       | Kernel background tasks |
| 2 (Dispatch) | Thread scheduling           | Dispatcher              |
| >= 3 (IRQs)  | Hardware interupts          | Mouse, disk, etc        |
| 27-31        | Crash, dump, NMI, emergency | Kernel Panic Levels     |
`IRQLs are how the CPU enforces control over who can run what`
- Only higher-IRQL code can interupt lower-IRQL code.
- A thread running at IRQGL 2 cannot be interupted by an IRQL 1 or 0

---
## Security Implications

#### Malware Tricks with Threads and IRQLs

| Technique                 | Description                                                               |
| ------------------------- | ------------------------------------------------------------------------- |
| Thread Priority Hijacking | Malware sets its thread to realtime priority to avoid interuption         |
| IRQL Abuse                | Malicious drivers run at high IRQL to prevent debugger or AV intervention |
| Starvation Attacks        | Malware saturates the scheduler so EDR or Sysmon threads cannot run       |
| Hook Race Conditions      | Modify function pointers during thread context switches to evade hooking  |
`These are why rootkits must be analyzed with kernel debuggers - you will not see this stuff in Sysmon`

---
## Tools To Observe Scheduling & Priority

| Tool                               | What To Watch                                                          |
| ---------------------------------- | ---------------------------------------------------------------------- |
| Process Hacker                     | Right-Click a process > view thread priorities                         |
| Windows Performance Recorder (WPR) | Record scheduling, context switches, CPU loads                         |
| Sysinternals - RAMMap              | See Driver memory allocations and thread activity                      |
| WinDbg                             | !thread, !ready, !irql commands for live kernel thread view (advanced) |

---
## Detection Engineering Relevance

| Concept                        | Applicaiton                                                          |
| ------------------------------ | -------------------------------------------------------------------- |
| High-priority thread detection | if a user process spawns threads with priority > normal, investigate |
| Starvation signs               | Spike in CPU + no logs = suspicious thread flooding                  |
| ETW Dropouts                   | Loss of logs may not be tampering - could be thread preemption       |
| Crash Dumps                    | Analyze IRQL state at crash for malware activity in kernel drivers   |

---
## Red Team Perspective
- Attackers will:
	- Elevate thread priority to avouid EDR interference
	- Delay AV threads via CPU flooding
	- Use NTAPI calls to manipulate scheduler (NtSetInformationThread)
- Defenders:
	- Monitor thread priority shifts
	- Detect kernel-mode drivers with IRQL abuse
	- Alert on ring 3 process attempting thread injection + priority change