> Malware doesn't always knock at the front door. Sometimes it hides in your own jacket.


> [!NOTE] Objective
> - Understand how attackers inject code into legitimate processes using techniques like DLL inection and process hollowing 
> - Learn how to ID these tactics using tools like Process Explorer, Procmon, Event Viewer and Sysmon ( Event Id7+1)
> - Think like an adversary who wants to run malware without triggering alarms - and a defender who needs to spot execution inside trusted processes

## Explain Like I Am 12
- Imagine your fav app - say, Notepad- is like a clean hotel room. Attackers do not book their own room. They sneak into Notepad's room, change clothes, and start doing bad things - buit the hotel log still says "Notepad"
- Thats what process hollowing: they empty the room and put something else inside. DLL Injkection is like sneaking in an extra suitcase with esecret tools.
## Deep Dive - 2 Key Techniques
#### DLL Inject
- Inject a Dynamic-Link Library (.dll) into another process's memory space so it loads **malicious functions**
- Common Methods:
	- CreateRemoteThread() + LoadLibraryA()
	- SetWindowsHookEx()
	- AppInit_DLLs registry key
	- Reflextive DLL Injection
- Used In: Red team payloads, keyloggers, credential stealers
#### Process Hollowing
- Create a legitimate process (ex svchost.exe) in suspended state, wipe its memory, then inject malwatre code into it and resume execution.
- Steps:
	- CreateProcess() with CREATE_SUSPENDED
	- Unmap origonal code using ZwUnmapViewOfSection()
	- Allocate memory and write payload with WriteProcessMemory()
	- Resume thread
- Used In: Ransomeware, APTs, Cobalt Strike, Metasploit
## Detection Logic (Defender's View)
- Goal: Detect legit processes doing shady things

| Behavior                                                                    | Detection Strategy                        |
| --------------------------------------------------------------------------- | ----------------------------------------- |
| Legitimate process loads suspcious DLL                                      | Sysmon Event ID 7: DLL load               |
| Process spawns from unexpected parent                                       | Event ID 4688 + odd PPID                  |
| Process loads from uncommon path(e.g %TEMP%)                                | Regex path filter in logs                 |
| Hollowed process exibits different memory sections or command line mismatch | EDR memory scanning parnt mismatch alerts |
## Tools

| Tools            | Function                                                              |
| ---------------- | --------------------------------------------------------------------- |
| Process Explorer | View Loaded DLLs per process                                          |
| Procmon          | Trace DLL load/write/create activity                                  |
| Sysmon           | Event ID1 (process create), ID 7 (DLL load), ID 10 (thread injection) |
| PEStudio         | Examine DLL headers                                                   |
| Autoruns         | AppInit_DLLs, KnownDLLs entries                                       |
## LAB - Detect Injection-Like Behavior
#### Inspect Running DLLs
- Open Process Explorer
- Right-click on notepad.exe (or any process)
- Click Properties > DLLs tab
- Look for:
	- DLLs from unexpected directories (like %TEMP%, %APPDATA%)
	- Unsigned or unverified DLLs
	- DLLs loaded after launch (suspicious injection behavior)
#### Trace DLL Loads with Procmon
- Open Procmon
- Set Filter:
	- Operation is Load Image
- Launch calc.exe, notepad.exe, or any app
- Watch which DLLs are loaded and where they're loaded from.
- Mark suspicious ones by path (non-System32 or System app loading from loading from AppData, etc.)
