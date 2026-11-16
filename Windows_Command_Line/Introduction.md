# Windows Command Line — Introduction, CMD vs PowerShell

**Module:** HTB Junior Cybersecurity Analyst
**Topic:** Windows Command Line 
**Document Purpose:** Revision, Practical Reference, and Interview Preparation

---

## Table of Contents
1. [Introduction to Windows Command Line](#1-introduction-to-windows-command-line)
2. [CMD vs PowerShell — Detailed Comparison](#2-cmd-vs-powershell--detailed-comparison)
3. [Opening and Accessing Both Shells](#3-opening-and-accessing-both-shells)
4. [CMD Practical Commands](#4-cmd-practical-commands)
5. [PowerShell Practical Commands](#5-powershell-practical-commands)
6. [PowerShell Cmdlet Naming Convention](#6-powershell-cmdlet-naming-convention)
7. [PowerShell Pipeline — Most Important Concept](#7-powershell-pipeline--most-important-concept)
8. [Important PowerShell Operators](#8-important-powershell-operators)
9. [CMD vs PowerShell Command Comparison](#9-cmd-vs-powershell-command-comparison)
10. [CMD Command Operators and Redirection](#10-cmd-command-operators-and-redirection)
11. [Cybersecurity Applications](#11-cybersecurity-applications)
12. [HTB Lab Connection](#12-htb-lab-connection)
13. [Hands-on Practice](#13-hands-on-practice)
14. [Common Mistakes and Troubleshooting](#14-common-mistakes-and-troubleshooting)
15. [Interview Preparation](#15-interview-preparation)
16. [Personal Learning and Revision](#16-personal-learning-and-revision)

---

## 1. Introduction to Windows Command Line

**What is a command-line interface?**
A Command-Line Interface (CLI) is a text-based user interface used to interact with a computer system. Instead of clicking icons with a mouse (GUI), users type commands to perform tasks. *(Hinglish: CLI ek aisa text-based interface hai jahan hum computer ko commands likh kar instructions dete hain, bina mouse use kiye.)*

**What is Windows Command Prompt (CMD)?**
Command Prompt (cmd.exe) is the original, legacy command-line interpreter for Windows systems. It was designed to provide a text-based interface similar to MS-DOS and is primarily used for basic file execution, network troubleshooting, and running simple batch files.

**What is PowerShell?**
PowerShell is a modern, powerful task automation framework and configuration management program developed by Microsoft. It consists of a command-line shell and an associated scripting language built on the .NET framework.

**Why does Windows provide command-line interfaces?**
GUI applications consume resources and can be slow for repetitive tasks. Command-line interfaces allow for automation, faster execution, scripting, and remote management where GUI is not available.

**How are they used in system administration?**
System administrators use these shells to automate daily tasks, configure system settings, manage Active Directory, manipulate network configurations, and deploy software across multiple machines efficiently.

**Their importance in cybersecurity and penetration testing:**
For attackers (Red Team), CLIs are essential for enumerating a compromised host, finding privilege escalation vectors, and executing payloads without triggering GUI alerts. For defenders (Blue Team), CLIs are crucial for investigating suspicious processes, reviewing logs, and hunting for persistence mechanisms.

---

## 2. CMD vs PowerShell — Detailed Comparison

| Feature | Command Prompt (CMD) | PowerShell |
| :--- | :--- | :--- |
| **Introduction & History** | Introduced in 1987 (as cmd.exe in Windows NT). | Introduced in 2006, open-sourced in 2016. |
| **Primary Purpose** | Basic system interaction, legacy script execution. | Advanced task automation, config management. |
| **Command Execution** | Runs built-in commands and Windows executables. | Runs cmdlets (pronounced "command-lets"), executables, and scripts. |
| **Command Output** | **Text-based output** (Strings). | **Object-based output** (.NET Objects). |
| **Command Aliases** | Limited/No true alias system. | Extensive alias system (e.g., `ls` aliases to `Get-ChildItem`). |
| **Pipelines** | Passes raw text to the next command. | Passes structured .NET objects to the next cmdlet. |
| **Scripting Capabilities** | Basic sequential execution. | Advanced programming logic, loops, error handling. |
| **File Extensions** | `.bat`, `.cmd` | `.ps1`, `.psm1` |
| **.NET Integration** | None. | Deep integration (access to .NET libraries/classes). |
| **Cross-Platform Support** | Windows only. | Windows, Linux, macOS (PowerShell Core). |
| **Administrative Capabilities**| Limited complex management. | Can manage AD, Exchange, Azure, and entire domains. |
| **Cybersecurity Applications** | Initial enumeration, basic stealth. | Post-exploitation, memory injection, detailed forensics. |

**The Most Important Difference:**
CMD primarily works with raw text. If you want to filter output, you have to parse strings. PowerShell works with structured objects. *(Hinglish: CMD sirf plain text return karta hai jise filter karna mushkil hai. PowerShell objects return karta hai, yani har data ke paas properties hoti hain, jisse filtering aur data manipulation bahut aasan ho jata hai.)*

---

## 3. Opening and Accessing Both Shells

**How to open CMD in Windows:**
Press `Win + R`, type `cmd`, and press Enter.

**How to open PowerShell in Windows:**
Press `Win + R`, type `powershell`, and press Enter.

**How to identify the current shell:**
- CMD typically displays a simple prompt like `C:\Users\Username>`.
- PowerShell typically prefixes the prompt with `PS`, like `PS C:\Users\Username>`.

**How to launch PowerShell from CMD:**
Simply type `powershell` and press Enter.

**How to execute CMD commands from PowerShell:**
You can run standard executables normally, but to explicitly run a CMD command string, use:
```powershell
cmd /c dir
```
*(Explanation: `cmd` launches the interpreter, `/c` tells it to carry out the command that follows (`dir`) and then immediately terminate.)*

**Running PowerShell commands from CMD:**
```cmd
powershell -Command "Get-Process"
```
*(Explanation: Calls the PowerShell interpreter from CMD, executes the cmdlet inside the quotes, and returns the output to the CMD window.)*

---

## 4. CMD Practical Commands

| Command | Purpose | Syntax |
| :--- | :--- | :--- |
| `cd` | Display or change directory | `cd C:\Windows` |
| `dir` | List files and directories | `dir` or `dir /a` (hidden) |
| `mkdir` | Create directory | `mkdir FolderName` |
| `echo` | Display text (often redirected to a file) | `echo Hello` |
| `type` | Display file contents | `type filename.txt` |
| `cls` | Clear screen | `cls` |
| `ipconfig` | Network configuration | `ipconfig` |
| `ipconfig /all`| Detailed network configuration | `ipconfig /all` |
| `tasklist` | Display running processes | `tasklist` |
| `systeminfo` | Display system information | `systeminfo` |
| `net user` | Enumerate local user accounts | `net user` |
| `netstat -ano` | Display network connections and associated PIDs | `netstat -ano` |

**Complete Practical Example:**
```cmd
mkdir C:\CyberLab
cd C:\CyberLab
echo My first Windows lab > test.txt
dir
type test.txt
```
**Execution Sequence and Interpretation:**
1. `mkdir C:\CyberLab`: Creates a new folder named CyberLab in the C: drive.
2. `cd C:\CyberLab`: Changes the current working directory to the newly created folder.
3. `echo My first Windows lab > test.txt`: Prints the text and uses `>` to redirect (save) it into a new file named `test.txt`.
4. `dir`: Lists the contents of the current directory. You will see `test.txt` in the output.
5. `type test.txt`: Reads and prints the contents of `test.txt` to the screen.

---

## 5. PowerShell Practical Commands

*(Note: Availability can depend on the Windows version, PowerShell version, and installed modules.)*

- **Get-Location**: Displays the current working directory.
  - *Example*: `Get-Location`
  - *Cyber Relevance*: Verifying your current path during an engagement.
- **Get-ChildItem**: Lists files and folders.
  - *Example*: `Get-ChildItem -Hidden`
  - *Cyber Relevance*: Finding hidden files, flags, or configuration files.
- **Get-Process**: Lists running processes.
  - *Example*: `Get-Process`
  - *Cyber Relevance*: Identifying security software (AV/EDR) or malicious processes.
- **Get-Service**: Lists Windows services.
  - *Example*: `Get-Service`
  - *Cyber Relevance*: Checking for vulnerable services or disabled defense services.
- **Get-LocalUser**: Lists local user accounts.
  - *Example*: `Get-LocalUser`
  - *Cyber Relevance*: Enumerating targets for privilege escalation.
- **Get-LocalGroup**: Lists local groups.
  - *Example*: `Get-LocalGroup`
  - *Cyber Relevance*: Checking members of the Administrators group.
- **Get-NetIPAddress**: Detailed IP configuration.
  - *Example*: `Get-NetIPAddress -AddressFamily IPv4`
  - *Cyber Relevance*: Network reconnaissance to find other subnets.
- **Get-ComputerInfo**: Comprehensive system specifications.
  - *Example*: `Get-ComputerInfo`
  - *Cyber Relevance*: Identifying OS version and architecture for exploit matching.
- **Get-ScheduledTask**: Lists tasks scheduled to run automatically.
  - *Example*: `Get-ScheduledTask`
  - *Cyber Relevance*: Finding persistence mechanisms left by attackers.
- **Get-Alias**: Shows what an alias stands for.
  - *Example*: `Get-Alias ls`
  - *Cyber Relevance*: Understanding obfuscated or shorthand scripts.
- **Clear-Host**: Clears the terminal screen.
  - *Example*: `Clear-Host` (or simply `clear`).

---

## 6. PowerShell Cmdlet Naming Convention

PowerShell strictly follows a **Verb-Noun** naming convention.
- **Verb**: The action you want to perform (e.g., Get, Set, New, Remove).
- **Noun**: The object or resource you are interacting with (e.g., Process, Service, LocalUser).

**Examples:**
- `Get-Process` (Action: Get, Resource: Process)
- `Get-Service` (Action: Get, Resource: Service)
- `Get-NetIPAddress` (Action: Get, Resource: Network IP Address)

**Why this matters:**
It makes PowerShell highly intuitive and predictable. If you need to stop a process, you can easily guess the cmdlet might be `Stop-Process`.

---

## 7. PowerShell Pipeline — Most Important Concept

**What is a pipeline?**
A pipeline is a series of commands connected together. 

**What does the pipe symbol (`|`) do?**
It takes the output of the command on the left and passes it as the input to the command on the right. 
*(Hinglish: Pipeline ka kaam hai pehle command ka result utha kar seedha doosre command ko de dena, taaki aap chain bana sakein.)*

**How objects travel / What is an object?**
In CMD, the pipeline passes raw text strings. In PowerShell, the pipeline passes **Objects**. An object is a data structure that contains **Properties** (attributes like Name, ID, CPU usage) and Methods (actions you can perform on it).

**What is the meaning of `$_`?**
`$_` represents the **current object** in the pipeline. When filtering a list, `$_` looks at one item at a time. *(Hinglish: Jaise programming ke loop mein 'i' hota hai, waise hi pipeline mein '$_' current item ko darshata hai).*

**Key Pipeline Cmdlets:**
- `Where-Object`: Filters objects based on a condition.
- `Select-Object`: Selects specific properties (columns) of an object.
- `Sort-Object`: Sorts objects by property values.
- `Measure-Object`: Calculates properties (count, average, sum).
- `Export-Csv`: Exports the objects into a CSV file format.

**Visual Text-Based Pipeline Diagram:**
```text
[Get-Process] --> Output: 100 Process Objects
      |
    (Pipe)
      ↓
[Where-Object] -> Filters down to 20 Objects (e.g., running from specific paths)
      |
    (Pipe)
      ↓
[Sort-Object] --> Sorts the 20 Objects by CPU usage
      |
    (Pipe)
      ↓
[Select-Object] -> Extracts only the Name and ID properties of those 20 Objects
```

**Practical Pipeline Examples:**

```powershell
Get-Service | Where-Object {$_.Status -eq "Running"}
```
*Explanation*: Fetches all services. Pipes them to `Where-Object`. For each service (`$_`), it checks if the Status property is equal to (`-eq`) "Running".

```powershell
Get-Process | Select-Object ProcessName, Id
```
*Explanation*: Fetches all processes. Instead of showing all data, it extracts only the ProcessName and Id columns.

```powershell
Get-Process | Where-Object {$_.ProcessName -like "*chrome*"}
```
*Explanation*: Fetches all processes. Filters for processes where the name contains the word "chrome" (the asterisks act as wildcards).

```powershell
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5 ProcessName, CPU
```
*Explanation*: Fetches all processes. Sorts them by CPU usage from highest to lowest. Finally, selects only the top 5, displaying just their Name and CPU usage.

---

## 8. Important PowerShell Operators

| Operator | Meaning | Example |
| :--- | :--- | :--- |
| `-eq` | Equal to | `Where-Object {$_.Status -eq "Running"}` |
| `-ne` | Not equal to | `Where-Object {$_.Status -ne "Stopped"}` |
| `-gt` | Greater than | `Where-Object {$_.Id -gt 1000}` |
| `-lt` | Less than | `Where-Object {$_.Id -lt 500}` |
| `-like` | Pattern matching (uses wildcards `*`) | `Where-Object {$_.Name -like "*win*"}` |
| `-and` | Logical AND (both true) | `Where-Object {$_.Status -eq "Running" -and $_.Name -like "*sql*"}` |
| `-or` | Logical OR (either true) | `Where-Object {$_.Name -eq "cmd" -or $_.Name -eq "powershell"}` |
| `-not` | Logical NOT (reverses logic) | `Where-Object {-not ($_.Status -eq "Running")}` |

---

## 9. CMD vs PowerShell Command Comparison

| Task | CMD Command | PowerShell Cmdlet |
| :--- | :--- | :--- |
| **Current directory** | `cd` | `Get-Location` |
| **List files** | `dir` | `Get-ChildItem` |
| **Processes** | `tasklist` | `Get-Process` |
| **Services** | `sc query` | `Get-Service` |
| **IP information** | `ipconfig` | `Get-NetIPAddress` |
| **System information**| `systeminfo` | `Get-ComputerInfo` |
| **Local users** | `net user` | `Get-LocalUser` |

*Note: Traditional commands like `ipconfig` and `ping` work perfectly fine inside PowerShell. Also, PowerShell has built-in aliases, meaning typing `dir` or `cd` in PowerShell will silently execute `Get-ChildItem` and `Set-Location` in the background.*

---

## 10. CMD Command Operators and Redirection

| Operator | Action | Explanation |
| :---: | :--- | :--- |
| `&` | Sequential Execution | Execute command 2 regardless of whether command 1 succeeds or fails. (e.g., `dir & echo Done`) |
| `&&` | Logical AND | Execute command 2 **only if** command 1 succeeds. (e.g., `mkdir test && cd test`) |
| `\|\|` | Logical OR | Execute command 2 **only if** command 1 fails. (e.g., `cd wrongpath \|\| echo Failed to open`) |
| `\|` | Pipe | Send the output of command 1 as input to command 2. |
| `>` | Redirect / Overwrite | Redirect output to a file. If the file exists, it overwrites it. |
| `>>` | Redirect / Append | Redirect output to a file. If the file exists, it adds data to the end of it. |
| `<` | Redirect Input | Use a file as the input for a command. |

*(Important Note: While PowerShell uses `>` and `>>` similarly for output redirection, PowerShell does NOT natively support `&&` and `||` in older versions, though they were introduced in PowerShell 7. `&` in PowerShell is the call operator, not a sequential execution operator).*

---

## 11. Cybersecurity Applications

### Attacker Perspective (Red Team)
- **Windows host enumeration:** Discovering OS version, patches, and architecture.
- **User enumeration:** Finding admins or active sessions.
- **Process enumeration:** Identifying installed EDRs, Antivirus, or vulnerable applications.
- **Service enumeration:** Finding weak service permissions or unquoted service paths.
- **Network reconnaissance:** Mapping internal routing, active connections, and listening ports.
- **File and directory discovery:** Hunting for hardcoded passwords in configuration files.
- **PowerShell abuse:** Attackers use PowerShell to download payloads directly into memory (fileless malware), bypassing disk-based antivirus.

### Defender Perspective (Blue Team)
- **Process investigation:** Looking for anomalous child processes.
- **Service monitoring:** Identifying unauthorized, newly created services (persistence).
- **Network configuration inspection:** Spotting malicious external connections via `netstat`.
- **Suspicious PowerShell execution:** Hunting for encoded commands (e.g., `powershell -enc`).
- **PowerShell Script Block Logging:** Event ID 4104 records the full content of PowerShell scripts executed, defeating obfuscation.
- **Module Logging:** Records pipeline execution details.
- **Process creation auditing:** Event ID 4688 logs command-line arguments.

**Example of Suspicious Execution:**
A Microsoft Word document (`WINWORD.EXE`) spawning `powershell.exe` with a base64 encoded command. 
*Why it requires investigation:* Office applications generally do not need to run PowerShell. This is a classic indicator of a malicious macro executing a downloader payload. However, do not assume 100% compromise immediately; investigate the parent-child relationship and the decoded script, as some poorly designed IT software might behave similarly.

---

## 12. HTB Lab Connection

To interact with HTB target machines securely from your local Linux/Parrot OS terminal, we use SSH (Secure Shell).

**Connection Syntax:**
```bash
ssh htb-student@<IP-Address>
```
- **SSH purpose:** Creates an encrypted remote terminal session to the target machine.
- **Username:** `htb-student` (the user account on the remote machine).
- **Target IP:** The dynamically assigned IP provided by HTB.
- **Host key verification:** On the first connection, you will be asked to verify the server's fingerprint. Type `yes`.
- **Password authentication:** Enter the password provided in the HTB portal. Note that Linux terminals do not show characters while typing passwords for security reasons.

**Default Shell Behavior:**
The default shell you land in depends on the remote SSH server configuration. It might be CMD or PowerShell.
Verify your environment immediately:
```powershell
$PSVersionTable
Get-Location
```
If `$PSVersionTable` returns an error, you are likely in CMD. Type `powershell` to switch.

---

## 13. Hands-on Practice

*Perform these exercises on a local Windows VM or HTB Sandbox.*

**Exercise 1: Create a directory and text file using CMD**
- **Objective**: Use basic CMD file operations.
- **Commands**: 
  ```cmd
  mkdir C:\TempLab
  cd C:\TempLab
  echo Cybersecurity is awesome > notes.txt
  type notes.txt
  ```
- **Expected Result**: Output displaying "Cybersecurity is awesome".
- **Observation**: Ensures you understand output redirection (`>`).
- **Security Relevance**: Attackers often use `echo` and redirection to write payloads or scripts to disk.

**Exercise 2: Enumerate running processes**
- **Objective**: View processes in both shells.
- **Commands**: 
  - CMD: `tasklist`
  - PS: `Get-Process`
- **Expected Result**: A long list of running processes.
- **Observation**: Notice the difference in formatting. CMD is plain text; PowerShell is formatted in objects.

**Exercise 3: Enumerate Windows services**
- **Objective**: List all services using PowerShell.
- **Commands**: `Get-Service`
- **Expected Result**: List of services with Status, Name, and DisplayName.
- **Security Relevance**: Services are prime targets for privilege escalation.

**Exercise 4: Filter running services using Where-Object**
- **Objective**: Find only actively running services.
- **Commands**: `Get-Service | Where-Object {$_.Status -eq "Running"}`
- **Expected Result**: Filtered list showing only running services.
- **Observation**: You applied your first pipeline condition.

**Exercise 5: Display selected process properties using Select-Object**
- **Objective**: Simplify process output.
- **Commands**: `Get-Process | Select-Object ProcessName, Id`
- **Expected Result**: A clean, two-column list.
- **Security Relevance**: Helps defenders parse massive amounts of data easily.

**Exercise 6: Sort processes using Sort-Object**
- **Objective**: Find processes consuming the most memory.
- **Commands**: `Get-Process | Sort-Object WorkingSet -Descending | Select-Object -First 5`
- **Expected Result**: Top 5 memory-heavy applications.
- **Observation**: Notice how data flowed through three different commands seamlessly.

**Exercise 7: Save network configuration to a text file**
- **Objective**: Export output to disk for offline analysis.
- **Commands**: `ipconfig /all > network_info.txt`
- **Expected Result**: No output on screen. A new file is created.
- **Security Relevance**: Attackers redirect command outputs to hidden text files and later exfiltrate them.

---

## 14. Common Mistakes and Troubleshooting

- **Running PS cmdlets directly in CMD:** Typing `Get-Process` in CMD will throw an error (`'Get-Process' is not recognized`). Switch to PowerShell first or use `powershell -c "Get-Process"`.
- **Incorrect paths:** Forgetting spaces in paths or not using quotes around paths with spaces (e.g., `cd "C:\Program Files"`).
- **Permission errors:** Certain commands (like creating users or viewing system processes) require "Run as Administrator".
- **Confusing Select-Object with Where-Object:** 
  - `Where-Object` filters rows (items). 
  - `Select-Object` filters columns (properties).
- **Confusing `>` and `>>`:** `>` destroys the old file and writes new data. `>>` adds to the bottom of the existing file. 
- **Assuming SSH always opens PowerShell:** Some SSH servers default to CMD. Always verify your shell.

---

## 15. Interview Preparation

**1. What is the difference between CMD and PowerShell?**
**Answer:** CMD is a legacy command-line interpreter that processes and outputs plain text. PowerShell is a modern, cross-platform automation framework built on .NET that processes and outputs structured objects, allowing for advanced scripting and automation.

**2. What is a PowerShell cmdlet?**
**Answer:** A cmdlet is a lightweight command utilized in the PowerShell environment. They strictly follow a Verb-Noun naming convention (like `Get-Process`) and perform a single specific function.

**3. What is an object-based pipeline?**
**Answer:** Unlike Linux or CMD pipelines that pass raw text streams, PowerShell passes .NET objects. This means the next command in the pipeline receives data complete with its original properties and methods, eliminating the need for complex text parsing (like `grep` or `awk`).

**4. What is the difference between Select-Object and Where-Object?**
**Answer:** `Where-Object` is used to filter data rows based on a specific condition (e.g., only show Running services). `Select-Object` is used to choose which properties/columns to display (e.g., show only the Name and ID of those services).

**5. Can CMD commands run in PowerShell?**
**Answer:** Yes. Traditional executables like `ping`, `ipconfig`, and `netstat` run natively in PowerShell.

**6. Can PowerShell commands run in CMD?**
**Answer:** Not natively. To run a PowerShell command in CMD, you must invoke the PowerShell interpreter by prefixing the command, like `powershell -c "Get-Service"`.

**7. What is the purpose of `$_` in PowerShell?**
**Answer:** `$_` represents the current object in the pipeline. It is used inside script blocks (like in `Where-Object`) to evaluate properties of each item passing through the pipeline one by one.

**8. How can PowerShell help in Windows enumeration?**
**Answer:** Attackers use built-in cmdlets like `Get-LocalUser`, `Get-Process`, and `Get-Service` to quietly gather system configuration, identify security software, and locate privilege escalation paths without downloading external tools.

**9. What is PowerShell Script Block Logging?**
**Answer:** It is a defensive feature that records the full content of executed PowerShell code in the Windows Event Logs (Event ID 4104). It is highly effective at capturing obfuscated attacker scripts after they de-obfuscate in memory.

**10. Why is PowerShell frequently investigated during security incidents?**
**Answer:** Because PowerShell allows deep system access and can execute payloads directly in memory (fileless malware) without leaving traditional artifacts on the hard drive, making it a favorite tool for advanced attackers.

---

## 16. Personal Learning and Revision

### Key Takeaways
- CMD works with text strings; PowerShell works with .NET objects.
- PowerShell's `Verb-Noun` structure makes guessing commands logical.
- The pipeline (`|`) is the most powerful feature in PowerShell, transferring objects, not just text.
- EDRs and Defenders actively monitor PowerShell logs (Event ID 4104, 4688).

### Important Commands Cheat Sheet
- **Check Shell:** `$PSVersionTable` (Works in PS only)
- **Processes:** `tasklist` (CMD) / `Get-Process` (PS)
- **Services:** `sc query` (CMD) / `Get-Service` (PS)
- **Filter (Rows):** `| Where-Object {$_.Property -eq "Value"}`
- **Filter (Columns):** `| Select-Object Property1, Property2`

### What I Understood
- The fundamental difference between interacting via text (CMD) and interacting via objects (PowerShell).
- How to route outputs via pipelines and redirection operators.
- How an attacker relies on native CLI tools ("Living off the Land") to avoid detection.

### What I Need To Practice More
- Memorizing object properties for complex pipelines.
- Creating advanced logical filters using `-and` / `-or` within `Where-Object`.

### My Practical Observations
*(Leave blank for future lab notes...)*
- [ ] 
- [ ] 

### Module Completion Checklist
- [x] Document basic CLI concepts.
- [x] Compare CMD and PowerShell.
- [x] Document fundamental CMD commands.
- [x] Document fundamental PS Cmdlets.
- [x] Understand and visualize the pipeline.
- [x] Complete local hands-on exercises.
- [x] Study interview Q&A.

---
*Created for HTB Junior Cybersecurity Analyst Path Documentation.*
