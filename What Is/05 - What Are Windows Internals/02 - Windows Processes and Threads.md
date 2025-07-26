
> [!NOTE] Objective
> Understand how processes and threads work in WIndows, how malware abuses them, and how to track and detect execution using built in and external tools
## Windows Process Execution chain
- Process Lifecycle
	1. Parent process calls CreateProcess() or CreateRemoteThread()
	2. Windows kernel allocates memory and assigns a PID(process ID)
	3. One or more threads are created and executed
	4. Child processes inherit enviorment, variables, One or more threads are created and executed
	5. Child processes inherit enviorment variables, privileges, or handles (if not restricted)
	6. Process logs creation events (eg. Event ID 4688)
- Key Terms
	- PID = Process ID
	- PPID = Parent Process ID (Helps detect malicious chains)
	- Token = Security Context (Wgho you are)
	- Thread = Unit of work (can be injected or hijacked)
## Tools: Monitor and Inspect Porcess Activity

| Tool                   | What it Shows                  | Why it Matters                                  |
| ---------------------- | ------------------------------ | ----------------------------------------------- |
| tasklist               | Basic running processes        | Lightweight snapshot                            |
| Process Explorer       | Full tree + parent/child links | See unusual PPID relationships                  |
| Procmon                | Real-time system calls         | Track registry, file, and process events        |
| Event Viewer           | Event ID 4688, 4696, 4672      | Log of created processes and elevated token use |
| taskkill, wmic prosess | Process control                | Baisc command-line interaction                  |

