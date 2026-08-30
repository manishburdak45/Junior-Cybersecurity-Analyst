<div align="center">

#  Interacting with the Windows Operating System

### Section 7 of the "Introduction to Windows" Learning Series (Consolidated, All 5 Parts)

`GUI & RDP` -> `CMD Fundamentals` -> `PowerShell & Cmdlets` -> `Aliases & Help System` -> `Scripts & Execution Policy`

</div>

---

## Overview

This document consolidates all five parts of the "Interacting with the Windows Operating System" section into a single English reference: the Graphical User Interface and Remote Desktop Protocol, the Windows Command Prompt (CMD), PowerShell and its cmdlets, PowerShell aliases and the help system, and finally PowerShell scripts and Execution Policy. The goal carried through every part is the same: learning to decode exam/lab question wording into the correct command or concept, not memorizing isolated facts.

> Note: This is theory and reference material built from the module content and guided discussion. No live target output had been captured for this section at the time of writing — the focus throughout is on recognizing the logic connecting a question's wording to the right command.

---

## Objective

1. Understand the GUI as an interaction model and RDP as its remote equivalent, including RDP's default port.
2. Understand CMD as Windows' native command-line interface and its built-in help system.
3. Understand PowerShell as a more powerful, object-oriented shell built on the .NET Framework, and its Verb-Noun cmdlet convention.
4. Understand PowerShell aliases, how to discover and create them, and the PowerShell help system (`Get-Help`, `Update-Help`).
5. Understand PowerShell scripts (`.ps1`), module import, and Execution Policy — including why it is not a hard security boundary.
6. Build the specific skill of mapping a question's exact wording to the correct command or option.

---

## Part 1 — GUI and RDP

### What Is a GUI?

**GUI = Graphical User Interface.** It lets a user interact with Windows through clicking, windows, menus, and icons — without needing to know commands or a programming language.

```text
Desktop
 |-- Files
 |-- Settings
 |-- Task Manager
 `-- Applications
```

The GUI concept originated at Xerox Palo Alto Research Center in the late 1970s and was later adopted by Apple and Microsoft specifically to address usability concerns for everyday users who would otherwise struggle with a command line. Most casual Windows users never need to touch the command line at all. The GUI's introduction is what opened up widespread computer access across many demographics, since users no longer needed to memorize commands or know a programming language to operate a computer.

### Where the GUI Is Used in Practice

System administrators commonly rely on GUI-based tools for:

- Active Directory administration
- IIS configuration
- Database interaction

> Concept: the GUI is easy to use, but cybersecurity work still requires solid command-line knowledge — the GUI alone is not sufficient for the depth of enumeration and administration this field demands.

### RDP — Remote Desktop Protocol

**RDP** is a proprietary Microsoft protocol that lets a user connect to a remote system over a network and obtain a full graphical user interface, as if sitting directly at that computer.

```text
Your Computer
      |
      v
     RDP
      |
      v
Remote Windows Machine
      |
      v
Graphical Desktop
```

The connection uses **RDP client software** connecting to a target system running **RDP server software**.

### RDP's Default Port

```text
RDP -> TCP Port 3389
```

This is one of the most important facts in this entire section — RDP opens a dedicated network channel on port 3389 for sending data back and forth.

**Common real-world uses:** system administrators use RDP to quickly administer remote systems; users can also access their work computers while traveling or working from home, typically after first connecting to a VPN.

### Cybersecurity Perspective on RDP

If a port scan against a target shows:

```text
3389/tcp open
```

The immediate inference is:

```text
3389 -> RDP -> Windows remote desktop service
```

In an authorized assessment, this can lead to investigating:

- RDP version/configuration
- Authentication behavior
- Account security
- Network exposure
- Brute-force protection
- Network Level Authentication (NLA) configuration

**Defender perspective:** RDP unnecessarily exposed to the internet is a real risk. Defenders should be asking:

```text
RDP
 |
 v
Who is connecting?
 |
 v
From where?
 |
 v
When?
 |
 v
Was authentication successful?
```

### Connecting Back to Earlier Practical Work

Earlier in this learning series, RDP was used to connect from a Linux attack host, through the HTB VPN, to a Windows target:

```text
Linux
  |
  v
 VPN
  |
  v
HTB Windows Target
  |
  v
RDP : 3389
  |
  v
Windows GUI
```

Running `whoami` inside that session returned `htb-student` — confirming this was an **interactive logon session**, connecting directly to the Windows Sessions concept covered earlier in this series.

### Critical Distinction — RDP vs. VPN

These are frequently confused but serve entirely different purposes:

```text
VPN:
Your machine -> Encrypted network tunnel -> Private network (network access)

RDP:
Your machine -> Remote Desktop connection -> Windows GUI (remote desktop access)
```

In HTB labs specifically: the VPN provides **network access** into the lab environment; RDP then provides **remote desktop access** to a specific Windows target already reachable through that VPN tunnel. One gets you onto the network; the other gets you a graphical session on a specific machine within it.

### Question-Decoding Practice

| Question Wording | Answer |
|---|---|
| "Which protocol allows a user to connect to a remote Windows system and obtain a graphical interface?" | RDP |
| "What port does RDP use?" | 3389 |
| "Which protocol is commonly used by administrators to remotely administer Windows systems?" | RDP |

### Part 1 Takeaway

```text
GUI  -> Graphical interaction with Windows
RDP  -> Remote graphical interaction with Windows
RDP Port -> 3389
VPN  -> Network connectivity/tunnel, NOT remote desktop access
```

---

## Part 2 — CMD Fundamentals

### What Is CMD?

**CMD = Command Prompt**, Windows' native command-line interface.

**Executable:**
```text
C:\Windows\System32\cmd.exe
```

Can be opened from the Start Menu, by typing `cmd` in the Run dialog, or by launching the binary directly.

**Typical uses:**

- One-off commands (e.g., `ipconfig` to view IP information)
- Administrative tasks
- Troubleshooting
- Scheduled tasks
- Scripts and batch files

> Reference: Microsoft's Windows Command Reference is a comprehensive A-Z reference covering syntax and usage examples for most Windows commands — familiarity with it is recommended.

### GUI vs. CMD — The Practical Difference

```text
GUI: Click -> Window -> Option -> Result
CMD: Command -> Output
```

For example, viewing IP information via the GUI might require navigating through several screens; in CMD, it's simply:

```cmd
ipconfig
```

**Why CMD matters for cybersecurity:** enumeration involves quickly checking hundreds of things — users, network, processes, services, files, permissions, tasks, system information — all achievable rapidly from the command line, and all scriptable for automation.

### The help Command

```cmd
help
```

Lists all available CMD commands.

**Example output (partial):**

```text
For more information on a specific command, type HELP command-name
ASSOC          Displays or modifies file extension associations.
ATTRIB         Displays or changes file attributes.
BREAK          Sets or clears extended CTRL+C checking.
BCDEDIT        Sets properties in boot database to control boot loading.
CACLS          Displays or modifies access control lists (ACLs) of files.
CALL           Calls one batch program from another.
CD             Displays the name of or changes the current directory.
CHCP           Displays or sets the active code page number.
CHDIR          Displays the name of or changes the current directory.
CHKDSK         Checks a disk and displays a status report.
CHKNTFS        Displays or modifies the checking of disk at boot time.
CLS            Clears the screen.
CMD            Starts a new instance of the Windows command interpreter.
COLOR          Sets the default console foreground and background colors.
COMP           Compares the contents of two files or sets of files.
COMPACT        Displays or alters the compression of files on NTFS partitions.
CONVERT        Converts FAT volumes to NTFS. You cannot convert the current drive.
COPY           Copies one or more files to another location.
```

**Core idea:** if a command's name isn't remembered, `help` is how it gets discovered.

### Getting Help for a Specific Command

```cmd
help schtasks
```

**Example output:**

```text
SCHTASKS /parameter [arguments]

Description:
    Enables an administrator to create, delete, query, change, run and
    end scheduled tasks on a local or remote system.

Parameter List:
    /Create         Creates a new scheduled task.
    /Delete         Deletes the scheduled task(s).
    /Query          Displays all scheduled tasks.
    /Change         Changes the properties of scheduled task.
    /Run            Runs the scheduled task on demand.
    /End            Stops the currently running scheduled task.
    /ShowSid        Shows the security identifier corresponding to a scheduled task name.
    /?              Displays this help message.
```

Shows a command's description, parameters, and usage examples.

### The `/?` Pattern

```cmd
command /?
```

**Example:**

```cmd
ipconfig /?
```

**Output:**

```text
USAGE:
    ipconfig [/allcompartments] [/? | /all |
                                 /renew [adapter] | /release [adapter] |
                                 /renew6 [adapter] | /release6 [adapter] |
                                 /flushdns | /displaydns | /registerdns |
                                 /showclassid adapter |
                                 /setclassid adapter [classid] |
                                 /showclassid6 adapter |
                                 /setclassid6 adapter [classid] ]

    Options:
       /?               Display this help message
       /all             Display full configuration information.
       /release         Release the IPv4 address for the specified adapter.
       /renew           Renew the IPv4 address for the specified adapter.
       /flushdns        Purges the DNS Resolver cache.
       /displaydns      Display the contents of the DNS Resolver Cache.
       /registerdns     Refreshes all DHCP leases and re-registers DNS names
```

**Pattern to remember:**

```text
command + /? -> that command's help
help <command> -> also that command's help, via CMD's own help system
```

### ipconfig

```cmd
ipconfig
```

Displays network interface/IP information. For full detail:

```cmd
ipconfig /all
```

**Question-decoding:**

| Question | Answer |
|---|---|
| "Which command displays IP configuration?" | `ipconfig` |
| "Which option displays full IP configuration?" | `ipconfig /all` |

### schtasks — Scheduled Tasks

Used to manage/query Scheduled Tasks.

| Option | Function |
|---|---|
| `/Create` | Create a task |
| `/Delete` | Delete a task |
| `/Query` | Display all tasks |
| `/Change` | Change a task's properties |
| `/Run` | Run a task on demand |
| `/End` | Stop a currently running task |
| `/ShowSid` | Show the SID corresponding to a scheduled task |

**Question-decoding:**

| Question | Answer |
|---|---|
| "Display all scheduled tasks" | `schtasks /Query` |
| "Create a scheduled task" | `schtasks /Create` |
| "Run a scheduled task" | `schtasks /Run` |

### Scheduled Tasks — Cybersecurity Relevance

Scheduled Tasks are normally used for backups, updates, maintenance, and automation — but they matter for security investigation too:

```text
Unknown scheduled task
        |
        v
Runs automatically
        |
        v
Suspicious executable
        |
        v
Investigate
```

Attackers can abuse scheduled tasks for persistence, which is why defenders should investigate suspicious tasks. The module content covered command usage here; detailed attack techniques for scheduled task abuse were noted as being outside this specific material's scope.

### Command -> Purpose -> Option Reference

| Command | Purpose |
|---|---|
| `help` | List of available commands |
| `help schtasks` | Detailed help for `schtasks` |
| `ipconfig` | IP/network configuration |
| `ipconfig /?` | `ipconfig` help |
| `ipconfig /all` | Detailed IP configuration |
| `schtasks /Query` | List scheduled tasks |
| `schtasks /Create` | Create a scheduled task |
| `schtasks /Run` | Run a task |
| `schtasks /End` | Stop a running task |

### The Verb-Driven Decoding Habit

Question wording carries a powerful clue in its verb:

```text
view / display -> query / list
create         -> Create
delete         -> Delete
run            -> Run
change         -> Change
stop / end     -> End
```

**Worked example:** "Display all scheduled tasks" should not be decoded as just "scheduled tasks -> schtasks" — it needs the verb too: "display -> /Query", giving the complete answer `schtasks /Query`. This habit of pairing the noun (which command) with the verb (which option) significantly speeds up question-solving.

### Part 2 Takeaway

```text
CMD -> cmd.exe -> Commands -> Options -> Help system -> Enumeration / Administration / Troubleshooting

command /?  -> that command's help
help command -> that command's help, via CMD's help system
```

---

## Part 3 — PowerShell and Cmdlets

### What Is PowerShell?

**PowerShell** is Microsoft's command shell and scripting environment, designed specifically with system administrators in mind.

```text
CMD -> Executes commands
PowerShell -> Commands + Objects + Scripting + Automation
```

PowerShell is built on top of the **.NET Framework**, which is what allows it to interact so deeply with the operating system.

### CMD vs. PowerShell

Both interact with Windows, but differently:

```text
CMD:
ipconfig
dir
schtasks

PowerShell:
Get-Service
Get-Process
Get-ChildItem
```

**PowerShell's key strength:** it handles command output as **objects**, not just plain text — making filtering, selecting, and formatting output far easier:

```text
Command -> Output -> Filter -> Select -> Format
```

This was already demonstrated earlier in this learning series:

```powershell
Get-Service | ? {$_.Status -eq "Running"} | select -First 2 | fl
```

### Cmdlets

PowerShell's built-in commands are called **cmdlets**, following a consistent **Verb-Noun** format:

```text
Get-Process
Get-Service
Get-ChildItem
Set-Location
Get-Help
```

There are more than 100 core cmdlets, with many more available or authorable for custom tasks.

**Breaking down the pattern:**

```text
Get-Process
  |     |
 Verb  Noun

Get     -> what to do
Process -> what to do it on
```

**Meaning:** "retrieve processes."

### Get-ChildItem

```powershell
Get-ChildItem
```

Lists the contents of the current directory — PowerShell's equivalent to CMD's `dir`.

**With a path:**

```powershell
Get-ChildItem -Path C:\Users\Administrator\Documents
```

**Recursively:**

```powershell
Get-ChildItem -Path C:\Users\Administrator\Downloads -Recurse
```

### Understanding -Path

```powershell
Get-ChildItem -Path C:\Users
```

| Part | Role |
|---|---|
| `Get-ChildItem` | Command |
| `-Path` | Parameter |
| `C:\Users` | Argument/value |

This three-part distinction — command, parameter, value — matters throughout PowerShell.

### Understanding -Recurse

```powershell
Get-ChildItem -Recurse
```

```text
Without -Recurse:
Current directory -> Files/folders (one level)

With -Recurse:
Current directory -> Files/folders -> Subdirectories -> Their subdirectories -> ...
```

Recursive search is valuable in cybersecurity work — for example, locating particular files within a directory tree in an authorized lab environment.

### Tab Completion

Typing `Get-ChildItem -` and pressing Tab cycles through available parameters — a practical habit that removes the need to memorize every parameter for every cmdlet.

### PowerShell Aliases (Introduced)

Many cmdlets have shorter alias names:

```text
Set-Location -> cd, sl
Get-ChildItem -> ls, gci
```

Available aliases can be viewed with:

```powershell
Get-Alias
```

**Example output:**

```text
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           % -> ForEach-Object
Alias           ? -> Where-Object
Alias           ac -> Add-Content
Alias           asnp -> Add-PSSnapin
Alias           cat -> Get-Content
Alias           cd -> Set-Location
Alias           CFS -> ConvertFrom-String                          3.1.0.0    Microsoft.PowerShell.Utility
Alias           chdir -> Set-Location
Alias           clc -> Clear-Content
Alias           clear -> Clear-Host
Alias           clhy -> Clear-History
Alias           cli -> Clear-Item
Alias           clp -> Clear-ItemProperty
```

**Creating a custom alias:**

```powershell
New-Alias -Name "Show-Files" Get-ChildItem
Get-Alias -Name "Show-Files"
```

```text
Show-Files -> alias -> Get-ChildItem
```

> Note: an alias created this way generally applies within the current PowerShell session's context unless separately persisted/configured.

### Cybersecurity Relevance of Aliases

Aliases can improve productivity during repeated pentesting/SOC work, but in professional documentation, writing `Get-ChildItem` explicitly is usually clearer than `ls`, since the reader immediately understands what the command does.

### Question-Decoding Practice

| Question | Answer |
|---|---|
| "Which PowerShell cmdlet is used to list the contents of a directory?" | `Get-ChildItem` |
| "What parameter allows Get-ChildItem to recursively enumerate subdirectories?" | `-Recurse` |
| "What is the alias for Get-ChildItem?" | `ls` or `gci` |
| "How can you view available PowerShell aliases?" | `Get-Alias` |

### Recognizing the Verb-Noun Pattern

Even without knowing the exact cmdlet, the pattern allows an educated guess:

```text
"Get information about processes"  -> Get-Process
"Get information about services"   -> Get-Service
"Get directory contents"           -> Get-ChildItem
"Change location"                  -> Set-Location
```

### PowerShell Help System (Introduced)

```powershell
Get-Help
```

For a specific cmdlet:

```powershell
Get-Help Get-AppPackage
```

**Example output when local help files aren't installed:**

```text
NAME
    Get-AppxPackage

SYNTAX
    Get-AppxPackage [[-Name] <string>] [[-Publisher] <string>] [-AllUsers] [-PackageTypeFilter {None | Main |
    Framework | Resource | Bundle | Xap | Optional | All}] [-User <string>] [-Volume <AppxVolume>]
    [<CommonParameters>]

ALIASES
    Get-AppPackage

REMARKS
    Get-Help cannot find the Help files for this cmdlet on this computer. It is displaying only partial help.
        -- To download and install Help files for the module that includes this cmdlet, use Update-Help.
```

Help files are not installed by default in PowerShell, which is why `Get-Help` can return only partial, auto-generated help until `Update-Help` is run.

### CMD Help vs. PowerShell Help

```text
CMD:        help command   OR   command /?
PowerShell: Get-Help Command
```

### Why PowerShell Matters So Much for Cybersecurity

Because it combines OS interaction, objects, filtering, scripting, and automation into one environment. The services example from earlier in this series demonstrates the full pipeline concept:

```text
Get-Service |
Where-Object {$_.Status -eq "Running"} |
Select-Object -First 2 |
Format-List
```

```text
Get data -> Filter -> Select -> Format
```

In a large Windows environment, this same pattern combined with scripting is what makes enumeration and administration scalable across many machines.

### Part 3 Mental Model

```text
PowerShell
    |
    v
Cmdlets
    |
    v
Verb-Noun
    |
    v
Parameters
    |
    v
Objects
    |
    v
Pipeline
    |
    v
Filtering
    |
    v
Automation
```

**Most important 5 cmdlets from this part:**

```text
Get-Service
Get-Process
Get-ChildItem
Get-Alias
Get-Help
```

---

## Part 4 — Aliases and the Help System (Expanded)

### What Is a PowerShell Alias?

A short alternative name for an existing PowerShell cmdlet.

```text
ls   -> Get-ChildItem
cd   -> Set-Location
gci  -> Get-ChildItem
?    -> Where-Object
%    -> ForEach-Object
```

### Checking Aliases

**All aliases:**

```powershell
Get-Alias
```

**Alias for a specific command:**

```powershell
Get-Alias -Definition Get-ChildItem
```

**Meaning of a specific alias:**

```powershell
Get-Alias -Name ls
```

```text
ls -> Get-Alias -> Get-ChildItem
```

### Creating a Custom Alias

```powershell
New-Alias -Name "Show-Files" Get-ChildItem
```

Running `Show-Files` afterward executes `Get-ChildItem`.

```text
Show-Files -> alias -> Get-ChildItem
```

### The Help System, In Depth

If a cmdlet's name is known but its syntax, parameters, or usage are not:

```powershell
Get-Help Get-ChildItem
```

**Online help**, if local help is incomplete:

```powershell
Get-Help Get-AppPackage -Online
```

**Updating local help files:**

```powershell
Update-Help
```

```text
Update-Help -> Help files download/update -> Get-Help -> Detailed local help
```

### Question-Decoding Practice

| Question | Answer |
|---|---|
| "What command can be used to view all available aliases?" | `Get-Alias` |
| "What cmdlet can be used to create a new alias?" | `New-Alias` |
| "How can you get help for a specific cmdlet?" | `Get-Help <cmdlet>` |
| "How can you open online help for a PowerShell cmdlet?" | `Get-Help <cmdlet> -Online` |
| "Which cmdlet downloads/updates local help files?" | `Update-Help` |

### Alias vs. Cmdlet — An Important Distinction

```text
ls  -> NOT a cmdlet, it is an alias for Get-ChildItem
cd  -> NOT a cmdlet, it is an alias for Set-Location
```

This is exactly the kind of distinction that can appear in an interview as well as an exam. In professional documentation, writing out `Get-ChildItem` rather than `ls` is clearer for the reader.

### Discovering Unknown Commands via Help

**Approach when a command isn't known:**

```text
Requirement -> Guess cmdlet (using Verb-Noun pattern) -> Get-Help -> Syntax -> Parameters -> Use command
```

**Worked example:** needing to "retrieve something about processes" -> guess `Get-Process` -> confirm with `Get-Help Get-Process`.

This discovery skill matters directly in cybersecurity enumeration: on an unfamiliar Windows system, needing "services information" leads to guessing `Get-Service`, confirming via `Get-Help Get-Service`, and exploring parameters via `Get-Service -<Tab>`.

### Part 4 Mental Model

```text
PowerShell
    |
    v
Cmdlet
    |
    v
Verb-Noun
    |
    v
Parameters
    |
    v
Alias
    |
    v
Get-Alias
    |
    v
Need documentation?
    |
    v
Get-Help
    |
    v
Need latest/local help?
    |
    v
Update-Help
```

**Must-remember commands:**

```text
Get-Alias     -> view aliases
New-Alias     -> create an alias
Get-Help      -> get help
Update-Help   -> update local help
-Online       -> online help
```

---

## Part 5 — PowerShell Scripts and Execution Policy

### PowerShell ISE

The **PowerShell ISE (Integrated Scripting Environment)** allows writing PowerShell scripts on the fly, with autocomplete/lookup for commands, and allows writing and running scripts in the same console for quick debugging.

### Running Scripts

Scripts can be run in various ways — for example, locally or after loading into memory via a download cradle:

```powershell
.\PowerView.ps1;Get-LocalGroup |fl
```

**Example output:**

```text
Description     : Users of Docker Desktop
Name            : docker-users
SID             : S-1-5-21-674899381-4069889467-2080702030-1004
PrincipalSource : Local
ObjectClass     : Group

Description     : VMware User Group
Name            : __vmware__
SID             : S-1-5-21-674899381-4069889467-2080702030-1003
PrincipalSource : Local
ObjectClass     : Group

Description     : Members of this group can remotely query authorization attributes and permissions for resources on
                  this computer.
Name            : Access Control Assistance Operators
SID             : S-1-5-32-579
PrincipalSource : Local
ObjectClass     : Group

Description     : Administrators have complete and unrestricted access to the computer/domain
Name            : Administrators
SID             : S-1-5-32-544
PrincipalSource : Local
```

### Importing Scripts as Modules

A common way to work with a script is to import it, making all of its functions available in the current session:

```powershell
Import-Module .\PowerView.ps1
```

Loaded modules and their commands can then be listed:

```powershell
Get-Module | select Name,ExportedCommands | fl
```

**Example output:**

```text
Name             : Appx
ExportedCommands : {[Add-AppxPackage, Add-AppxPackage], [Add-AppxVolume, Add-AppxVolume], [Dismount-AppxVolume,
                   Dismount-AppxVolume], [Get-AppxDefaultVolume, Get-AppxDefaultVolume]...}

Name             : Microsoft.PowerShell.LocalAccounts
ExportedCommands : {[Add-LocalGroupMember, Add-LocalGroupMember], [Disable-LocalUser, Disable-LocalUser],
                   [Enable-LocalUser, Enable-LocalUser], [Get-LocalGroup, Get-LocalGroup]...}

Name             : Microsoft.PowerShell.Management
ExportedCommands : {[Add-Computer, Add-Computer], [Add-Content, Add-Content], [Checkpoint-Computer,
                   Checkpoint-Computer], [Clear-Content, Clear-Content]...}

Name             : Microsoft.PowerShell.Utility
ExportedCommands : {[Add-Member, Add-Member], [Add-Type, Add-Type], [Clear-Variable, Clear-Variable], [Compare-Object,
                   Compare-Object]...}

Name             : PSReadline
ExportedCommands : {[Get-PSReadLineKeyHandler, Get-PSReadLineKeyHandler], [Get-PSReadLineOption,
                   Get-PSReadLineOption], [Remove-PSReadLineKeyHandler, Remove-PSReadLineKeyHandler],
                   [Set-PSReadLineKeyHandler, Set-PSReadLineKeyHandler]...}
```

### Execution Policy

A security feature that attempts to prevent execution of malicious scripts.

| Policy | Description |
|---|---|
| **AllSigned** | All scripts (local and remote) must be signed by a trusted publisher. A prompt appears before running scripts from a publisher not yet marked trusted or untrusted. |
| **Bypass** | No scripts or configuration files are blocked; no warnings or prompts. |
| **Default** | Sets the default policy — `Restricted` for Windows desktop machines, `RemoteSigned` for Windows servers. |
| **RemoteSigned** | Scripts can run, but scripts downloaded from the internet require a digital signature. Locally written scripts do not require one. |
| **Restricted** | Allows individual commands but not script execution. Blocks all script file types, including `.ps1xml` configuration files, `.psm1` module scripts, and `.ps1` PowerShell profiles. |
| **Undefined** | No execution policy set for the current scope. If ALL scopes are Undefined, the default policy (`Restricted`) applies. |
| **Unrestricted** | Default (and unchangeable) policy on non-Windows computers. Allows unsigned scripts to run but warns before running scripts not from the local intranet zone. |

### Checking the Current Execution Policy

**All scopes:**

```powershell
Get-ExecutionPolicy -List
```

**Example output:**

```text
        Scope ExecutionPolicy
        ----- ---------------
MachinePolicy       Undefined
   UserPolicy       Undefined
      Process       Undefined
  CurrentUser       Undefined
 LocalMachine    RemoteSigned
```

### Execution Policy Is Not a Hard Security Boundary

This is one of the most important security concepts in this entire section: **the execution policy is not meant to be a security control that restricts user actions.** It can easily be bypassed by:

- Typing the script contents directly into the PowerShell window
- Downloading and invoking the script directly
- Specifying the script as an encoded command
- Adjusting the execution policy itself, if the user has sufficient rights
- Setting the execution policy for the **current process scope** — which almost any user can do, since it requires no configuration change and only applies for the duration of that session

### Changing the Execution Policy for the Current Process

```powershell
Set-ExecutionPolicy Bypass -Scope Process
```

**Example interaction:**

```text
Execution Policy Change
The execution policy helps protect you from scripts that you do not trust. Changing the execution policy might expose
you to the security risks described in the about_Execution_Policies help topic at
https:/go.microsoft.com/fwlink/?LinkID=135170. Do you want to change the execution policy?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"): Y
```

**Verifying the change:**

```powershell
Get-ExecutionPolicy -List
```

```text
        Scope ExecutionPolicy
        ----- ---------------
MachinePolicy       Undefined
   UserPolicy       Undefined
      Process          Bypass
  CurrentUser       Undefined
 LocalMachine    RemoteSigned
```

> Critical Concept: because `-Scope Process` requires no elevated configuration change and only lasts for the current session, nearly any user — including a low-privileged one, in many environments — can bypass the execution policy for their own working session. This is precisely why Execution Policy should be understood as a safety guard against *accidental* script execution, not as an actual barrier against a determined attacker or tester.

---

## Complete Section Summary

| Concept | What It Is | What It's Used For | Cybersecurity Relevance |
|---|---|---|---|
| **GUI** | Graphical interface | Operating Windows by clicking | System administration, AD, IIS, databases |
| **RDP** | Remote Desktop Protocol | Remotely accessing another Windows computer's desktop | Remote administration, authorized pentesting |
| **CMD** | Windows command shell | Executing commands | Enumeration, troubleshooting, administration, automation |
| **help** | CMD help command | Viewing command information | Understanding unknown commands/options |
| **/?** | Command help option | Viewing a command's options | Understanding command syntax |
| **PowerShell** | Advanced shell + scripting environment | Deeply controlling/automating Windows | Enumeration, automation, administration, security operations |
| **Cmdlet** | PowerShell's built-in command | Performing a specific function | Collecting Windows information |
| **Alias** | Short name for a cmdlet | Typing commands quickly | Productivity |
| **Get-Help** | PowerShell help | Understanding a cmdlet's syntax/usage | Investigating unknown cmdlets |
| **.ps1** | PowerShell script file | Automating multiple commands | Security automation/scripting |
| **Execution Policy** | Policy controlling script execution | Allowing/restricting scripts | Security investigation/context (not a hard boundary) |

### Complete Structural Map

```text
                 WINDOWS
                    |
        +-----------+-----------+
        |                       |
       GUI                  COMMAND LINE
        |                       |
       RDP                 +----+----+
        |                  |         |
     3389                 CMD    PowerShell
                                      |
                                +-----+-----+
                                |     |     |
                            Cmdlets Alias Scripts
                                |           |
                           Get-Help    Execution Policy
```

---

## Master Question-Decoding Cheat Sheet

| Question Clue | Answer/Command |
|---|---|
| Remote graphical Windows access | RDP |
| RDP port | 3389 |
| Windows command shell | CMD |
| CMD available commands | `help` |
| Command options | `command /?` |
| List directory (PowerShell) | `Get-ChildItem` |
| List services | `Get-Service` |
| List processes | `Get-Process` |
| List aliases | `Get-Alias` |
| Create alias | `New-Alias` |
| PowerShell help | `Get-Help` |
| Online help | `Get-Help ... -Online` |
| Update local help | `Update-Help` |
| PowerShell script file type | `.ps1` |
| Import script/module | `Import-Module` |
| Loaded modules | `Get-Module` |
| Check execution policy | `Get-ExecutionPolicy` |
| All policy scopes | `Get-ExecutionPolicy -List` |
| Current-process-only policy | `-Scope Process` |

### The 10 Most Important Facts to Lock In

```text
RDP         -> port 3389
CMD         -> cmd.exe
help        -> CMD's built-in help
/?          -> command-specific options/help

PowerShell    -> Windows scripting/command environment
Get-Service   -> services
Get-Process   -> processes
Get-ChildItem -> files/directories
Get-Alias     -> aliases
Get-Help      -> PowerShell help
```

**Core cybersecurity mindset for this section:** learn to work from CMD, but learn to deeply enumerate and automate Windows from PowerShell.

---

## What This Section Taught

Every part of this section builds toward one transferable skill: converting a question's wording into the exact command needed to answer it. GUI and RDP established *how* a human or remote operator interacts with Windows at all; CMD introduced the base command-line interaction model and its self-documenting `help`/`/?` pattern; PowerShell introduced the far more powerful Verb-Noun cmdlet system built on objects and pipelines; aliases and `Get-Help` showed how to navigate that system even without memorizing every cmdlet; and Execution Policy closed the loop with a specific, important security lesson — that a policy meant to prevent *accidental* script execution is trivially bypassed by anyone willing to set it for their own process scope, and should never be mistaken for a real security control.



.
.
.
..

.

<div align="center">

# Windows Management Instrumentation (WMI)

###  "Introduction to Windows" Learning Series (Consolidated)

`WMI Theory & Components` -> `WMIC Command Line` -> `PowerShell: Get-WmiObject / Invoke-WmiMethod` -> `Practical Question Solved`

</div>

---

## Overview

This document consolidates the full WMI section: what WMI is and how it's architected, interacting with it from the command line via `WMIC`, interacting with it from PowerShell via `Get-WmiObject` and `Invoke-WmiMethod`, and a worked practical question (finding a system's serial number via WMI) that was solved and answered within this learning series.

> Note: This document also revisits and properly explains the `Get-WmiObject -Class win32_OperatingSystem | select Version,BuildNumber` command that was run against the actual HTB target much earlier in this series (Section 1) — at that point it was used without full explanation of what each piece did; this section closes that loop.

---

## Objective

1. Understand what WMI is, its purpose, and its core architectural components.
2. Learn to interact with WMI from the command line using `WMIC`, including its alias/verb/adverb structure.
3. Learn to interact with WMI from PowerShell using `Get-WmiObject` and `Invoke-WmiMethod`.
4. Understand WMI's dual relevance to both Blue Team (monitoring/investigation) and Red Team (enumeration/lateral movement) work.
5. Apply this knowledge to solve a real question: finding a system's serial number via WMI.

---

## Part 1 — WMI Foundation & Theory

### What Is WMI?

**WMI (Windows Management Instrumentation)** is a subsystem that provides system administrators with powerful tools for system monitoring. Its goal is to **consolidate device and application management across corporate networks**. WMI is a core part of the Windows operating system and has come pre-installed since **Windows 2000**.

### WMI Components

| Component | Description |
|---|---|
| **WMI service** | The core WMI process, running automatically at boot, acting as an intermediary between WMI providers, the WMI repository, and managing applications |
| **Managed objects** | Any logical or physical component that WMI can manage |
| **WMI providers** | Objects that monitor events/data related to a specific object |
| **Classes** | Used by WMI providers to pass data to the WMI service |
| **Methods** | Attached to classes; allow actions to be performed (e.g., starting/stopping processes on remote machines) |
| **WMI repository** | A database storing all static data related to WMI |
| **CIM Object Manager** | Requests data from WMI providers and returns it to the requesting application |
| **WMI API** | Enables applications to access the WMI infrastructure |
| **WMI Consumer** | Sends queries to objects via the CIM Object Manager |

### What WMI Is Used For

- Status information for local/remote systems
- Configuring security settings on remote machines/applications
- Setting and changing user and group permissions
- Setting/modifying system properties
- Code execution
- Scheduling processes
- Setting up logging

These tasks can be performed through a combination of PowerShell and the **WMI Command-Line Interface (WMIC)**.

---

## Part 2 — WMIC (Command Line)

### What Is WMIC?

**WMIC = Windows Management Instrumentation Command-line.** It is the interface for interacting with WMI directly from the Command Prompt.

```text
WMI -> WMIC -> WMI information/operations from CMD
```

WMIC can be run in two ways:

```cmd
wmic
```

Opens an interactive WMIC shell, or a command can be run directly, such as:

```cmd
wmic computersystem get name
```

### WMIC Help

```cmd
wmic /?
```

**Output:**

```text
WMIC is deprecated.

[global switches] <command>

The following global switches are available:
/NAMESPACE           Path for the namespace the alias operate against.
/ROLE                Path for the role containing the alias definitions.
/NODE                Servers the alias will operate against.
/IMPLEVEL            Client impersonation level.
/AUTHLEVEL           Client authentication level.
/LOCALE              Language id the client should use.
/PRIVILEGES          Enable or disable all privileges.
/TRACE               Outputs debugging information to stderr.
/RECORD              Logs all input commands and output.
/INTERACTIVE         Sets or resets the interactive mode.
/FAILFAST            Sets or resets the FailFast mode.
/USER                User to be used during the session.
/PASSWORD            Password to be used for session login.
/OUTPUT              Specifies the mode for output redirection.
/APPEND              Specifies the mode for output redirection.
/AGGREGATE           Sets or resets aggregate mode.
/AUTHORITY           Specifies the <authority type> for the connection.
/?[:<BRIEF|FULL>]    Usage information.
```

> Technical Note: **"WMIC is deprecated"** means Microsoft no longer recommends it for future use — newer Windows administration favors PowerShell/WMI APIs directly. However, understanding WMIC remains useful for fundamentals and for working with older Windows environments.

Global switches worth recognizing at this stage without memorizing in depth: `/NODE`, `/USER`, `/PASSWORD`, `/OUTPUT`, `/RECORD`, `/TRACE`, `/?`.

### wmic computersystem get name

```cmd
wmic computersystem get name
```

Retrieves the computer's hostname/name.

```text
wmic -> computersystem -> get -> name
```

**Meaning:** "retrieve the `name` property from the `computersystem` object."

**Question-decoding:** "Which WMIC command retrieves the computer name?" -> clues "computer name" + "retrieve" -> `wmic computersystem get name`.

### wmic os list brief

```cmd
wmic os list brief
```

Displays basic operating system information.

**Example output:**

```text
BuildNumber  Organization  RegisteredUser  SerialNumber             SystemDirectory      Version
19041                      Owner           00123-00123-00123-AAOEM  C:\Windows\system32  10.0.19041
```

> Note: this output's `Version` (`10.0.19041`) and `BuildNumber` (`19041`) directly match what was previously observed via `Get-WmiObject` on the actual HTB target in Section 1 of this series — the same underlying data, retrieved through a different interface (WMIC here, versus PowerShell's `Get-WmiObject` there).

### GET vs. LIST

| Verb | Function |
|---|---|
| **GET** | Retrieve a specific property |
| **LIST** | Show/list information about an object |

```text
wmic computersystem get name  -> GET: a specific property
wmic os list brief             -> LIST: general information, filtered by adverb
```

### BRIEF (Adverb)

```cmd
wmic os list brief
```

`BRIEF` limits output to core/basic properties only.

```text
LIST  = verb (show/list data)
BRIEF = adverb (only core/basic properties)
```

### WMIC's Structure

```text
wmic
  |
  v
ALIAS
  |
  v
VERB
  |
  v
ADVERB / SWITCH
```

**Worked example — `wmic os list brief`:**

| Segment | Role |
|---|---|
| `wmic` | WMIC itself |
| `os` | Alias (represents a WMI class/object) |
| `list` | Verb |
| `brief` | Adverb |

> Technical Note: WMIC's "aliases" (e.g., `computersystem`, `os`) are a different concept from PowerShell aliases (e.g., `ls` for `Get-ChildItem`) — in WMIC, an alias represents a WMI class/object being targeted, not a shortcut for a cmdlet name.

### Cybersecurity Relevance of WMIC/WMI

WMI/WMIC can be used for system information, remote systems, configuration, permissions, processes, and logging — making it directly relevant to a security analyst or pentester, who should be able to recognize:

```text
wmic -> WMI interaction -> System information / management
```

Later material in this learning path uses WMI in the context of enumeration and lateral movement.

### Question-Decoding Practice

| Question | Answer |
|---|---|
| "Which command displays the hostname?" | `wmic computersystem get name` |
| "Which WMIC command displays basic operating system information?" | `wmic os list brief` |
| "Which WMIC option displays help?" | `wmic /?` |

### GET vs. LIST — Critical Distinction

```text
GET  -> Specific property retrieval    (e.g., wmic computersystem get name)
LIST -> Object information listing     (e.g., wmic os list brief)
BRIEF -> Core/basic properties only (adverb, used with LIST)
```

### Part 2 Cheat Sheet

```text
wmic                          -> Interactive WMIC shell
wmic /?                       -> WMIC help
wmic computersystem get name  -> Computer/hostname
wmic os list brief            -> Basic OS information

GET   -> Retrieve property
LIST  -> List information
BRIEF -> Core/basic properties
```

**Question-solving formula:**

```text
Question -> WMIC mentioned? -> Identify the object -> Identify the property/action -> GET or LIST? -> BRIEF/switch if needed
```

---

## Part 3 — WMI via PowerShell: Get-WmiObject

### The Cmdlet

```powershell
Get-WmiObject -Class <ClassName>
```

**Example:**

```powershell
Get-WmiObject -Class Win32_OperatingSystem
```

| Segment | Meaning |
|---|---|
| `Get-WmiObject` | Retrieve information from WMI |
| `-Class` | Which WMI class to target |
| `Win32_OperatingSystem` | The operating system information class |

### Revisiting the Command Used Earlier in This Series

The following command was run against the actual HTB target much earlier (Section 1):

```powershell
Get-WmiObject -Class win32_OperatingSystem | select Version,BuildNumber
```

**Output at the time:**

```text
Version      BuildNumber
-------      -----------
10.0.19041   19041
```

**Now fully explained, piece by piece:**

| Segment | Function |
|---|---|
| `Get-WmiObject` | Retrieve WMI information |
| `-Class Win32_OperatingSystem` | Target the OS class |
| `\|` | Pipe output forward |
| `select Version,BuildNumber` | Keep only these two properties |

> Note: this closes the loop on a command that was used correctly early in this learning series without a full breakdown at the time — now that WMI, WMIC, and `Get-WmiObject` have all been covered, every piece of that original command is fully understood.

### Win32_OperatingSystem

This WMI class provides Windows operating system information. Example broader query:

```powershell
Get-WmiObject -Class Win32_OperatingSystem | select SystemDirectory,BuildNumber,SerialNumber,Version
```

**Example output:**

```text
SystemDirectory     BuildNumber SerialNumber            Version
---------------     ----------- ------------            -------
C:\Windows\system32 19041       00123-00123-00123-AAOEM 10.0.19041
```

**Properties available from this class include:** `SystemDirectory`, `BuildNumber`, `SerialNumber`, `Version`.

---

## Part 4 — WMI via PowerShell: Invoke-WmiMethod

### Beyond Reading — Taking Action

WMI is not limited to reading information — WMI classes expose **methods** that can perform actions. The PowerShell cmdlet for this is:

```powershell
Invoke-WmiMethod
```

**Meaning:** invoke/call a method on a WMI object.

### Worked Example — Renaming a File

```powershell
Invoke-WmiMethod -Path "CIM_DataFile.Name='C:\users\public\spns.csv'" -Name Rename -ArgumentList "C:\Users\Public\kerberoasted_users.csv"
```

**Output:**

```text
__GENUS          : 2
__CLASS          : __PARAMETERS
__SUPERCLASS     :
__DYNASTY        : __PARAMETERS
__RELPATH        :
__PROPERTY_COUNT : 1
__DERIVATION     : {}
__SERVER         :
__NAMESPACE      :
__PATH           :
ReturnValue      : 0
PSComputerName   :
```

**Command structure:**

| Segment | Meaning |
|---|---|
| `Invoke-WmiMethod` | Execute a WMI method |
| `-Path` | Target WMI object |
| `CIM_DataFile.Name='...'` | The specific file object being targeted |
| `-Name Rename` | The method being called (Rename) |
| `-ArgumentList` | The new filename |

### ReturnValue

```text
ReturnValue : 0 -> Operation completed successfully
```

> Note: the exact example filenames used here (`spns.csv` renamed to `kerberoasted_users.csv`) hint at a Kerberoasting-related workflow, though this specific section's material focused purely on the mechanics of `Invoke-WmiMethod` itself, not on Kerberoasting as an attack technique.

---

## Cybersecurity Perspective

WMI's power comes from being usable against both local and remote systems, making it relevant to both sides of security work.

**Blue Team:**

```text
WMI -> System information -> Configuration -> Monitoring -> Investigation
```

**Red Team:**

Later material in this learning path uses WMI for:

```text
Enumeration + Lateral Movement
```

WMI is simultaneously a legitimate administration mechanism and a significant attack surface/technique — recognizing both sides is the point of this section.

---

## Complete WMI Cheat Sheet

| Concept | Command/Meaning |
|---|---|
| WMI | Windows Management Instrumentation — Windows management/information framework |
| WMIC | `wmic` — command-line interface for WMI |
| WMIC help | `wmic /?` |
| Computer name | `wmic computersystem get name` |
| OS information | `wmic os list brief` |
| PowerShell WMI read | `Get-WmiObject -Class Win32_OperatingSystem` -> retrieve information from a WMI class |
| PowerShell WMI action | `Invoke-WmiMethod` -> call a method/action on a WMI object |

### Final Mental Model

```text
                    WMI
                     |
          +----------+----------+
          |                     |
        WMIC                PowerShell
          |                     |
     +----+----+          +-----+------+
     |         |          |            |
   GET       LIST    Get-WmiObject  Invoke-WmiMethod
     |         |          |            |
 Information  List       Retrieve      Perform
 (property)  (broader)  information     action
```

### Question-Decoding Practice

| Question | Answer |
|---|---|
| "Get information about the operating system using WMI" | `Get-WmiObject -Class Win32_OperatingSystem` |
| "Which command can retrieve the hostname using WMIC?" | `wmic computersystem get name` |
| "Which WMIC option provides basic/core properties?" | `BRIEF` |
| "Which PowerShell command invokes a WMI method?" | `Invoke-WmiMethod` |
| "What does ReturnValue 0 indicate?" | Successful completion |

---

## Practical Question Solved — Finding the System Serial Number via WMI

### The Question

> "Use WMI to find the serial number of the system."

### Reasoning Chain

```text
WMI                -> PowerShell equivalent: Get-WmiObject
System/OS info      -> Class: Win32_OperatingSystem
Serial number       -> Property: SerialNumber
```

### Command

```powershell
Get-WmiObject -Class Win32_OperatingSystem | Select-Object SerialNumber
```

**Short form (equivalent):**

```powershell
Get-WmiObject -Class Win32_OperatingSystem | select SerialNumber
```

### Expected Output Format

```text
SerialNumber
------------
00123-00123-00123-AAOEM
```

> Note: the actual `SerialNumber` value returned from the specific HTB target is what gets submitted as the answer — the value shown above is the module's illustrative example format, not a guaranteed match for every target.

### Question-Solving Formula (Reusable Pattern)

```text
WMI            -> Get-WmiObject
System/OS      -> Win32_OperatingSystem
Serial number  -> SerialNumber
```

This exact pattern — identify the PowerShell cmdlet family (`Get-WmiObject`), identify the relevant class (`Win32_OperatingSystem`), identify the specific property needed (`SerialNumber`) — is the same reasoning chain used throughout this section for every WMI-related question, whether asked via WMIC or PowerShell.

---

## What This Section Taught

WMI is one underlying framework accessible through two different interfaces — `WMIC` from the command line and `Get-WmiObject`/`Invoke-WmiMethod` from PowerShell — and the same information (OS version, build number, serial number, hostname) can be reached through either path. The recurring skill this section reinforced is decomposing any WMI-related question into three parts: which interface, which class/object, and which specific property or method — a reasoning chain flexible enough to answer questions about information never explicitly covered, simply by recognizing the pattern (as demonstrated directly in the serial number question, which required no new command syntax, only correctly reapplying `Get-WmiObject` + `Win32_OperatingSystem` with a different target property).
