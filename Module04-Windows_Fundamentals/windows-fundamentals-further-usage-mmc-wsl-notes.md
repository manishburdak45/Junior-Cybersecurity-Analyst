# Windows Fundamentals – Further Windows Usage: MMC & WSL

> Personal study notes for the **Hack The Box (HTB) Junior Cybersecurity Analyst** path.
> Scope: **Microsoft Management Console (MMC)** and **Windows Subsystem for Linux (WSL)**.

---

## How to Read These Notes

| Tag | Meaning |
|-----|---------|
| `[HTB-ALIGNED]` | Concepts that belong to the two HTB topics (MMC, WSL) at HTB's beginner learning level. |
| `[EXTRA]` | Additional practical cybersecurity knowledge that goes beyond the HTB section (attacker/defender views, logging, ATT&CK, etc.). |
| `[SAMPLE OUTPUT]` | Illustrative output written by me for learning. **It is not real output from any machine.** Your output will differ. |
| `[MY NOTES]` | Blank fields for my own observations and lab results. |

**Important notes on sources**

- HTB-aligned content here is based on the *topic scope* (MMC and WSL). I did not copy HTB text. Before publishing, compare these notes with the actual HTB section and adjust wording or order to match what the module teaches.
- Commands and menu names can differ between Windows 10, Windows 11, and Windows Server, and between WSL versions. Verify on your own system with `wsl --help` and Microsoft's documentation.
- All hands-on work must be done **only on my own machine or in authorized labs.**

---

## Table of Contents

1. [Module Overview](#1-module-overview)
2. [Microsoft Management Console (MMC)](#2-microsoft-management-console-mmc)
3. [Windows Subsystem for Linux (WSL)](#3-windows-subsystem-for-linux-wsl)
4. [Hands-on Exercises](#4-hands-on-exercises)
5. [Cybersecurity Relevance](#5-cybersecurity-relevance)
6. [Interview Preparation](#6-interview-preparation)
7. [Quick Revision](#7-quick-revision)
8. [Personal Learning and Completion Tracker](#8-personal-learning-and-completion-tracker)
9. [Further Reading](#9-further-reading)

---

# 1. Module Overview

## 1.1 Section Name and Objectives `[HTB-ALIGNED]`

- **Module:** Windows Fundamentals (HTB Academy, Junior Cybersecurity Analyst path)
- **Section:** Further Windows Usage
- **Topics covered in these notes:**
  1. Microsoft Management Console (MMC)
  2. Windows Subsystem for Linux (WSL)

**My learning objectives (my own wording):**

- Explain what MMC is and how snap-ins let one console host many admin tools.
- Open MMC, add snap-ins, and save a custom console.
- Explain what WSL is, how it differs from a normal virtual machine, and how WSL 1 differs from WSL 2.
- Check WSL status, list distributions, and run Linux commands from Windows.
- Describe, at a beginner level, how these tools can be misused and how defenders reduce that risk.

## 1.2 Expected Skills After Completing This Section

| Skill | Level |
|-------|-------|
| Launch `mmc.exe` and build a custom console | Beginner → Practical |
| Recognise common `.msc` tools (Event Viewer, Services, etc.) | Beginner |
| Use `wsl` commands to check status, list distros, and start a shell | Beginner → Practical |
| Access files across Windows and Linux (`/mnt/c`, `\\wsl$`) | Practical |
| Explain MMC and WSL security risks in an interview | Conceptual |
| Troubleshoot "access denied", missing snap-in, and WSL feature errors | Practical |

## 1.3 Why These Topics Matter

**Windows administration**

- Most built-in Windows admin tools (Event Viewer, Services, Device Manager, Disk Management) are MMC snap-ins. Knowing MMC means knowing where to look when investigating a system.
- Admins use MMC to manage local and remote computers from one place.

**Cybersecurity**

- Analysts read logs in Event Viewer, check services and scheduled tasks, and review local users and groups. All of these live in MMC.
- Attackers abuse legitimate admin tools because they are trusted and already installed ("living off the land"). Defenders must know what *normal* looks like.
- WSL gives Windows users a Linux command line (grep, awk, curl, python, etc.), which is very useful for log analysis and scripting. It also introduces a **second environment** that security teams must be aware of and monitor.

> **Hinglish:** MMC aur WSL dono *legitimate* Windows features hain. Inka use admin, developer aur analyst karte hain, lekin attacker bhi kar sakta hai. Isliye "kaise kaam karta hai" aur "kaise detect karein" dono samajhna zaroori hai.

## 1.4 Study Levels Used in These Notes

| Level | Meaning | Example |
|-------|---------|---------|
| **Educational knowledge** | Definitions and concepts | "A snap-in is a module hosted inside MMC." |
| **Practical skill** | Things I can do on a system | Add the Event Viewer snap-in, run `wsl --list --verbose`. |
| **Industry relevance** | How this appears in real jobs | SOC analysts monitoring `mmc.exe` / `wsl.exe` process activity. |

> **Honesty note:** These are beginner-level student notes. Practising on a personal PC is **not** the same as managing an enterprise environment.

---

# 2. Microsoft Management Console (MMC)

## 2.1 What MMC Is and Why It Exists `[HTB-ALIGNED]`

**MMC (Microsoft Management Console)** is a Windows framework/application (`mmc.exe`) that provides a common window for hosting administrative tools. The tools hosted inside it are called **snap-ins**.

**Why it exists:** Before MMC, each admin tool had its own different interface. MMC gives administrators one consistent interface and lets them build **custom consoles** that contain only the tools they need.

**Simple analogy:** MMC is like an empty toolbox. Snap-ins are the tools you put inside. The toolbox does nothing by itself.

> **Hinglish:** MMC ek *container* hai, khud koi kaam nahi karta. Kaam snap-ins karte hain.

## 2.2 Core Terms

| Term | Meaning |
|------|---------|
| **MMC / `mmc.exe`** | The host application that provides the window, menus, and console tree. |
| **Snap-in** | A module that adds a specific management function (e.g., Event Viewer, Services). |
| **Standalone snap-in** | A snap-in that can be added on its own. |
| **Extension snap-in** | A snap-in that extends another snap-in (e.g., extra panels under Computer Management). |
| **Console file (`.msc`)** | A saved MMC configuration listing which snap-ins are loaded and how. |
| **Console tree** | The left pane showing snap-ins and their nodes. |
| **Details pane** | The centre pane showing content for the selected node. |
| **Actions pane** | The right pane listing available actions for the selected node. |
| **Author mode** | Console mode that lets you add/remove snap-ins and change the console. |
| **User mode** | Restricted modes (used to limit what others can change in a saved console). |

## 2.3 How MMC Combines Tools

1. You open `mmc.exe` (blank console).
2. You choose **File → Add/Remove Snap-in** (shortcut: `Ctrl + M`).
3. You pick snap-ins from the list and add them to the console tree.
4. Some snap-ins ask which computer to manage (**Local computer** or **Another computer**).
5. You save the result as a `.msc` file. Opening that file later restores the same set of tools.

Many built-in tools are simply **pre-made `.msc` files**, such as `eventvwr.msc` or `services.msc`. They open MMC with one particular snap-in already loaded.

## 2.4 MMC Itself vs. the Tools Inside It

| Question | MMC (`mmc.exe`) | Snap-in |
|----------|-----------------|---------|
| What is it? | The host/container | The actual tool |
| Does it manage anything alone? | No | Yes |
| Who provides it? | Windows | Windows, Microsoft products (RSAT), or third parties |
| Example | Blank console window | Event Viewer, Services, Device Manager |
| Security consequence | Runs with the token of the user who started it | Capability depends on the snap-in *and* the user's permissions |

## 2.5 Common Snap-ins and `.msc` Shortcuts

| Snap-in | Run command | Purpose | Security relevance | Notes |
|---------|-------------|---------|--------------------|-------|
| Computer Management | `compmgmt.msc` | Hub with Event Viewer, Shared Folders, Local Users and Groups, Device Manager, Disk Management, Services | Central place to review shares, users, services | Can connect to another computer |
| Event Viewer | `eventvwr.msc` | View Windows event logs | Primary source for investigation (logons, service installs, errors) | Security log needs admin rights |
| Services | `services.msc` | Start/stop/configure services | Malicious or tampered services; startup type; "Log on as" account | Changes need admin |
| Device Manager | `devmgmt.msc` | Manage hardware drivers | Unsigned/rogue drivers | |
| Disk Management | `diskmgmt.msc` | Partitions and volumes | Awareness of mounted/attached drives | |
| Local Users and Groups | `lusrmgr.msc` | Manage local accounts and groups | Check who is in local *Administrators* | **Not available on Windows Home** |
| Shared Folders | `fsmgmt.msc` | See shares, sessions, open files | Unexpected shares and sessions | |
| Task Scheduler | `taskschd.msc` | View/create scheduled tasks | Common persistence location | |
| Performance Monitor | `perfmon.msc` | Performance counters and data collection | Spotting unusual resource use | |
| Local Security Policy | `secpol.msc` | Local security settings (audit, user rights) | Audit policy and rights assignment | Not on Home |
| Local Group Policy Editor | `gpedit.msc` | Local group policy settings | Hardening settings | Not on Home |
| Certificates | `certmgr.msc` | Current user certificate stores | Rogue root certificates | |
| Windows Defender Firewall with Advanced Security | `wf.msc` | Detailed firewall rules | Review inbound/outbound rules | |
| Active Directory Users and Computers | `dsa.msc` | Manage AD users, groups, computers | Core AD administration | Needs RSAT / domain controller; `[EXTRA]` |

> **Mistake to avoid:** Not every `.msc` exists on every Windows edition. If `gpedit.msc` or `lusrmgr.msc` is missing, check whether you are on Windows Home. That is an edition limitation, not a broken PC.

## 2.6 Opening MMC `[HTB-ALIGNED]`

**Option A: Run dialog**

1. Press `Win + R`
2. Type `mmc` and press Enter
3. Accept the **UAC** prompt if shown

**Option B: Start menu / search**

1. Search for `mmc`
2. Right-click → **Run as administrator** (when you need admin capability)

**Option C: Command line (Command Prompt or PowerShell)**

```powershell
mmc                         # opens a blank console
mmc.exe C:\Path\to\my.msc   # opens a saved console file
mmc /a                      # opens in author mode (lets you edit a saved console)
eventvwr.msc                # opens a console with only the Event Viewer snap-in
```

**What happens:** `mmc` starts the host process. Specific `.msc` files tell MMC which snap-ins to load.

> Switches such as `/a`, `/32`, and `/64` exist but behaviour can vary. Check with Microsoft documentation if a switch does not work as expected.

## 2.7 Adding and Managing Snap-ins

**Add a snap-in (GUI)**

1. Open `mmc`.
2. **File → Add/Remove Snap-in…** (`Ctrl + M`).
3. In *Available snap-ins*, select one (e.g., **Event Viewer**) → **Add >**.
4. If asked, choose **Local computer** (or another computer, if authorised).
5. Click **OK**.

**Manage snap-ins**

- **Remove:** Add/Remove Snap-in → select in *Selected snap-ins* → **Remove**.
- **Reorder:** Use **Move Up / Move Down**.
- **Edit extensions:** Select a snap-in → **Edit Extensions…** (turn extensions on/off).
- **Rename / organise:** Right-click nodes; use **File → Options** for console title and mode.

**Save the console**

1. **File → Save As…**
2. Name it, e.g., `MyAdminConsole.msc`
3. Open it later by double-clicking or by running `mmc.exe MyAdminConsole.msc`.

**Console modes (File → Options)**

| Mode | Idea |
|------|------|
| Author mode | Full control; can add/remove snap-ins and change the console |
| User mode – full access | Can navigate everything, cannot add/remove snap-ins |
| User mode – limited access, multiple windows | Restricted view with multiple windows |
| User mode – limited access, single window | Restricted view, single window |

User modes are a convenience for limiting what a user can change in a saved console. They are **not** a strong security control by themselves. Real protection comes from permissions and policy.

## 2.8 Administrative Permissions and Remote Management

**Local permissions**

- MMC runs with the **access token of whoever started it**. A standard user can open MMC but many snap-ins will be read-only or will fail.
- **UAC** (User Account Control) separates a normal token from an elevated one even for admin accounts. Use *Run as administrator* only when needed.

**Remote management** `[EXTRA]`

- Some snap-ins can target another computer: in **Computer Management**, right-click the top node → **Connect to another computer…**.
- Usually required: valid **admin rights on the remote computer**, network reachability, name resolution, and **firewall rules** that allow remote management (e.g., rules related to remote event log, remote service management, and WMI). Underlying protocols often involve **RPC/DCOM**.
- If the remote side is not configured, you will see errors like *"The RPC server is unavailable"* or *"Access is denied"*.
- In domain environments, admins often install **RSAT (Remote Server Administration Tools)** to get extra snap-ins such as Active Directory Users and Computers.

**Security consideration:** Every remote-management path is also a **lateral movement path** if credentials are stolen. Remote management should be limited to specific admin accounts and admin workstations.

## 2.9 Practical Example: Reviewing Local Administrators `[EXTRA]`

**Goal:** Check who has administrator rights on my own Windows PC. This is a basic security hygiene task.

**GUI path (Windows Pro/Enterprise/Education)**

1. `Win + R` → `compmgmt.msc` → Enter.
2. Expand **System Tools → Local Users and Groups → Groups**.
3. Double-click **Administrators**.
4. Read the member list.

**Command-line equivalents (to cross-check)**

```cmd
net localgroup Administrators
```

```powershell
Get-LocalGroupMember -Group "Administrators"
```

**Interpretation questions**

- Do I recognise every account?
- Is my daily-use account an admin? (Better: use a standard account daily and elevate only when needed.)
- Are there unknown accounts? On a personal PC this could signal a problem worth investigating.

> On Windows Home, `lusrmgr.msc` is missing, so use the command-line equivalents above.

## 2.10 Common Errors and Troubleshooting

| Problem / message | Likely cause | What to try |
|-------------------|--------------|-------------|
| UAC prompt appears every time | Normal behaviour for admin tools | Confirm it is expected; do not disable UAC just to avoid prompts |
| `Access is denied` in a snap-in | Not elevated, or account lacks rights | Reopen MMC with **Run as administrator**; check group membership |
| Snap-in missing from the list | Edition limitation or feature not installed (e.g., RSAT) | Check Windows edition (`winver`); install the optional feature if relevant |
| `gpedit.msc` / `lusrmgr.msc` not found | Windows Home edition | Use alternatives (`net user`, `Get-LocalUser`, registry/settings) or a Pro edition |
| "Windows cannot find `xyz.msc`" | Typo or file removed | Check spelling; search for the `.msc` file |
| "MMC cannot open the file" | Corrupt, missing, or restricted `.msc`; blocked by policy | Recreate the console; check policy and file permissions |
| "MMC has detected an error in a snap-in" | A snap-in crashed or is incompatible | Choose to unload the snap-in; update or reinstall the related component |
| `The RPC server is unavailable` (remote) | Remote firewall, service stopped, name resolution, or no network path | Check connectivity and firewall rules; confirm authorisation first |
| Changes not saved | Console opened in a user mode, or save location not writable | Use author mode (`mmc /a`) and save to a writable folder |

## 2.11 Attacker Perspective `[EXTRA]`

> Educational only. No exploitation steps are included on purpose.

MMC and the tools in it are **legitimate and trusted**, which makes them attractive for abuse ("living off the land"):

- **Using admin tools after compromise:** An attacker with admin credentials can use the same snap-ins (Services, Computer Management, Event Viewer) for discovery, configuration changes, or log clearing.
- **Remote management used for lateral movement:** Valid admin credentials plus allowed RPC/DCOM/WMI paths can let an attacker manage other machines. A DCOM object related to MMC (`MMC20.Application`) has been publicly documented as a lateral-movement technique that needs remote admin access.
- **Malicious console files:** Researchers have documented crafted `.msc` files used to execute code when a user opens them (for example, the technique known as *GrimResource*). Treat `.msc` files from untrusted sources like any other executable-capable file.
- **Log tampering:** With sufficient rights, event logs can be cleared. This is a high-value signal for defenders (see Event ID 1102 below).
- **MITRE ATT&CK:** MMC-related abuse is catalogued under **System Binary Proxy Execution: MMC** (T1218.014); remote use of DCOM appears under **Remote Services: DCOM** (T1021.003). Verify the current technique IDs on the MITRE ATT&CK website.

**Key idea:** The tool is not "malicious." The **abuse of valid credentials and trusted tools** is the problem.

## 2.12 Defender Perspective `[EXTRA]`

| Control | What it does |
|---------|--------------|
| **Least privilege** | Daily accounts are standard users; admin rights are granted only where needed. |
| **Separate admin accounts** | Admin accounts are not used for email/web browsing. |
| **UAC** | Forces a conscious elevation step. Keep it enabled. |
| **Restrict remote management** | Allow admin protocols only from specific admin hosts and accounts. |
| **Application control** | Tools like AppLocker or WDAC can limit who may run `mmc.exe` or open `.msc` files from user-writable folders. |
| **Audit & logging** | Enable *process creation* auditing with command line (Event ID **4688**) and consider **Sysmon** (Event ID 1). Review logon events (4624/4625), special privileges (4672), service install (7045), account changes (4720, 4732), and log cleared (**1102** for Security log). |
| **Monitoring / detection ideas** | Alert on `mmc.exe` launching a `.msc` from Downloads/Temp; `mmc.exe` spawned by Office or a browser; unexpected remote admin activity; log clearing. |
| **Limitations** | Detection depends on correct logging, tuning, and analyst attention. Admins also use MMC legitimately, so there will be false positives. |

> **Hinglish:** Defender ka goal "MMC ko band karna" nahi hai. Goal hai: kaun use kar raha hai, kahan se, kis account se, aur kya kar raha hai, ye visible rakhna.

## 2.13 MMC: 60-Second Recap

- MMC = container (`mmc.exe`), snap-ins = tools, `.msc` = saved configuration.
- Open with `Win + R → mmc`; add tools with `Ctrl + M`.
- Permissions come from the user's token, not from MMC itself.
- Remote management needs rights, firewall rules, and connectivity.
- Security view: trusted tool → can be abused; defend with least privilege + logging.

---

# 3. Windows Subsystem for Linux (WSL)

## 3.1 What WSL Is and Why People Use It `[HTB-ALIGNED]`

**WSL (Windows Subsystem for Linux)** lets you run a Linux environment (such as Ubuntu or Debian) **directly on Windows**, without a separate dual-boot setup and without manually creating a full virtual machine in VirtualBox or VMware.

**Why developers use it**

- Run Linux-based tools, compilers, and package managers on a Windows laptop.
- Match Linux production environments more closely.

**Why security professionals and students use it**

- Use Linux command-line tools (`grep`, `awk`, `sed`, `jq`, `curl`, `python3`, `openssl`, `sha256sum`, `strings`, `xxd`) for log parsing, scripting, and quick analysis.
- Practise Linux skills needed for HTB, CTF, and SOC work while staying on Windows.

> **Hinglish:** WSL ka matlab hai Windows ke andar hi Linux terminal chalana. Alag se dual boot ya heavy VM setup ki zaroorat nahi hoti.

## 3.2 WSL 1 vs. WSL 2 (Conceptual) `[HTB-ALIGNED]` / `[EXTRA]`

| Feature | WSL 1 | WSL 2 |
|---------|-------|-------|
| Core idea | Translation layer: Linux system calls are translated into Windows calls | Lightweight utility virtual machine running a **real Linux kernel** |
| Linux kernel | No real Linux kernel | Real Linux kernel (provided and updated through Microsoft's WSL package) |
| Linux system-call compatibility | Partial | Much fuller |
| Performance: files stored inside Linux filesystem | Slower | Faster |
| Performance: files stored on Windows drives (`/mnt/c`) | Generally faster | Generally slower |
| Linux filesystem | Files exposed through Windows filesystem | Stored in a virtual disk (`ext4.vhdx`) using ext4 |
| Networking | Shares the host's network stack | Separate virtual network by default (NAT); other modes exist in newer versions |
| Typical choice today | Special cases | Default for new installs (where supported) |

> **Note:** Details like networking modes and defaults change with WSL and Windows updates. Check current Microsoft documentation for your version.

**Beginner memory trick**

- **WSL 1** = translator.
- **WSL 2** = small Linux machine (a VM), but tightly integrated with Windows.

## 3.3 How Linux Distributions Operate on Windows

- A **distribution (distro)** is a packaged Linux environment (Ubuntu, Debian, Kali, etc.).
- You can have **multiple distros** installed, each with its own filesystem and users.
- One distro is the **default**; running `wsl` with no arguments starts it.
- Each distro has its **own Linux users**. Passwords in Linux are separate from your Windows password.
- Windows manages the WSL service and starts/stops the Linux environment. Idle distros may stop automatically.
- Windows and Linux can **call each other's programs** (interoperability), for example `notepad.exe` from inside Linux, or `wsl ls` from PowerShell.

## 3.4 Checking WSL Status and Distros

> **Availability note:** Some commands depend on your Windows and WSL version. If a command is not recognised, check `winver` and run `wsl --help`. Updating WSL (`wsl --update`) may add newer options.

### `wsl --status`

| Item | Detail |
|------|--------|
| **Purpose** | Show general WSL status such as default distribution and default WSL version. |
| **Run in** | PowerShell or Command Prompt |
| **Expected output** | A few lines. Newer builds may show more details (e.g., kernel information). |
| **Interpretation** | Confirms WSL is present, which distro is default, and whether new distros will use WSL 1 or 2. |
| **Security relevance** | Quick way to discover whether WSL exists on a system during triage or audit. |

```text
[SAMPLE OUTPUT - illustrative only, your output will differ]
Default Distribution: Ubuntu
Default Version: 2
```

### `wsl --list --verbose` (short form `wsl -l -v`)

| Item | Detail |
|------|--------|
| **Purpose** | List installed distributions with their state and WSL version. |
| **Expected output** | A table: `NAME`, `STATE` (Running/Stopped), `VERSION` (1 or 2). An asterisk `*` marks the default distro. |
| **Interpretation** | Tells you what is installed, what is running right now, and whether each distro uses WSL 1 or 2. |
| **Security relevance** | An unexpected distro (for example, a pentesting distro on a work machine) may violate policy or indicate misuse. |

```text
[SAMPLE OUTPUT - illustrative only, your output will differ]
  NAME      STATE           VERSION
* Ubuntu    Running         2
  Debian    Stopped         2
```

Related: `wsl --list --online` (or `wsl -l -o`) shows distros available to install.

### `wsl --version`

| Item | Detail |
|------|--------|
| **Purpose** | Show version information for WSL and related components. |
| **Expected output** | WSL version, kernel version, and other component versions, plus the Windows version. |
| **Interpretation** | Helps with troubleshooting and checking whether components are up to date. |
| **Security relevance** | Outdated components can contain known bugs. Version info helps with patch tracking. |
| **Caution** | Only available in newer WSL builds. Older systems may not recognise it. |

```text
[SAMPLE OUTPUT - illustrative only; x = placeholder numbers]
WSL version: 2.x.x.x
Kernel version: x.x.x
WSLg version: x.x.x
Windows version: 10.0.xxxxx.xxxx
(other component lines may appear)
```

### `wsl`

| Item | Detail |
|------|--------|
| **Purpose** | Start the **default** distribution in the current terminal. |
| **Expected output** | The prompt changes to a Linux shell prompt, e.g. `user@PC:/mnt/c/Users/you$`. |
| **Interpretation** | You are now inside Linux. Type `exit` to return to Windows. |
| **Security relevance** | Commands you run now are Linux processes, but they can still reach Windows files (via `/mnt/c`) with **your Windows user's permissions**. |

Useful variants: `wsl -d <DistroName>` (specific distro), `wsl <command>` (run one Linux command from Windows), `wsl -u root` (start as root user).

### `wsl --help`

| Item | Detail |
|------|--------|
| **Purpose** | Show all supported arguments for your installed WSL version. |
| **Expected output** | A usage line followed by lists of options (install, list, set-version, shutdown, terminate, export/import, and so on). |
| **Interpretation** | The authoritative list for **your** system. Use it whenever a command differs from tutorials. |
| **Security relevance** | Shows management and data-movement options (e.g., export/import of distro images) that analysts should know exist. |

```text
[SAMPLE OUTPUT - truncated, illustrative only]
Usage: wsl.exe [Argument] [Options...] [CommandLine]
...
```

### Other commands worth knowing `[EXTRA]`

| Command | Purpose | Caution |
|---------|---------|---------|
| `wsl --install` | Install WSL and a default distro (supported Windows versions; needs admin; restart may be needed) | Run only on a machine you own/manage |
| `wsl --install -d <Distro>` | Install a specific distro | Check available names with `wsl -l -o` |
| `wsl --set-default-version 2` | New distros use WSL 2 | |
| `wsl --set-version <Distro> 2` | Convert an existing distro | Can take time; back up first |
| `wsl --update` | Update the WSL package | |
| `wsl --shutdown` | Stop all distros and the WSL 2 VM | Unsaved work in Linux processes is lost |
| `wsl --terminate <Distro>` | Stop one distro | |
| `wsl --export` / `wsl --import` | Backup or move a distro | Exported files contain the distro's data, so protect them |
| `wsl --unregister <Distro>` | Remove a distro | **Permanently deletes that distro's data. Do not run casually.** |

## 3.5 Installing or Configuring WSL (Where Supported)

**Requirements (high level)**

- A supported Windows 10 or Windows 11 version.
- For WSL 2: CPU virtualization enabled in BIOS/UEFI, and the **Virtual Machine Platform** feature.
- Administrator rights to install features.

**Simple install (supported versions), run PowerShell as Administrator**

```powershell
wsl --install
```

Then restart if asked and complete the Linux username/password setup on first launch.

**Check or enable features manually (if `wsl --install` is not available)**

```powershell
# Check feature state (Admin PowerShell)
Get-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
Get-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
```

You can also use: **Start → "Turn Windows features on or off"** and tick *Windows Subsystem for Linux* and *Virtual Machine Platform*. Restart afterwards.

**Optional configuration `[EXTRA]`**

- **`%UserProfile%\.wslconfig`**: global settings for WSL 2 (memory, processors, networking options on supported versions).
- **`/etc/wsl.conf`** (inside a distro): per-distro settings such as automount and interoperability behaviour.

```ini
# Example /etc/wsl.conf (hardening-oriented, optional)
[interop]
enabled = false            # stops launching Windows programs from Linux
appendWindowsPath = false  # stops adding Windows PATH into Linux PATH
```

> Changing these settings may break workflows that rely on Windows integration. Test in a non-critical distro first, and restart the distro (`wsl --terminate <Distro>`) so changes apply.

## 3.6 Launching a Distro and Running Linux Commands

```powershell
wsl                 # start default distro
wsl -d Ubuntu       # start a specific distro (use the name from wsl -l -v)
wsl ls -la          # run one Linux command from Windows and return
```

Inside Linux (basic commands to practise):

| Command | Purpose |
|---------|---------|
| `whoami` | Current Linux user |
| `hostname` | Name of the environment |
| `pwd` | Current directory |
| `ls -la` | List files, including hidden, with permissions |
| `cd ~` | Go to home directory |
| `cat /etc/os-release` | Show distro name and version |
| `uname -a` | Kernel and system information |
| `id` | User ID and group memberships |
| `echo "hello" > test.txt` | Create a file |
| `cat test.txt` | Read the file |
| `exit` | Return to Windows |

> **Hinglish:** `wsl` likhte hi prompt badal jata hai. Ab aap Linux ke andar ho. `exit` se wapas Windows.

## 3.7 Accessing Files Across Windows and Linux

| Direction | How | Example |
|-----------|-----|---------|
| **Linux → Windows files** | Windows drives are mounted under `/mnt/` | `ls /mnt/c/Users/` |
| **Windows → Linux files** | Network-path style access | `\\wsl$\Ubuntu` or `\\wsl.localhost\Ubuntu` in File Explorer |
| **Open current Linux folder in Explorer** | Run from Linux | `explorer.exe .` |
| **Run Windows program from Linux** | Interop | `notepad.exe` |
| **Run Linux command from Windows** | `wsl <command>` | `wsl cat /etc/os-release` |

**Performance tip (WSL 2):** Keep project files **inside the Linux filesystem** (e.g., `~/projects`) instead of `/mnt/c/...` for better speed.

**Do not** modify Linux distro files by browsing raw VHD files or hidden Windows folders. Use the `\\wsl$` path or work from inside Linux to avoid corruption.

## 3.8 WSL vs. a Traditional Virtual Machine

| Aspect | WSL | Traditional VM (VirtualBox/VMware/Hyper-V VM) |
|--------|-----|----------------------------------------------|
| Setup effort | Very low | Higher (create VM, install OS, configure) |
| Integration with host | Tight: shared files, run each other's programs | Limited unless tools/shared folders are configured |
| Isolation | **Weaker by design** (built for convenience) | Stronger separation from the host |
| Resource use | Lightweight, dynamic | Fixed allocations typically, heavier |
| Full desktop OS | Usually command-line focus (GUI apps supported in newer versions) | Full OS with desktop |
| Snapshots | Not like VM snapshots (export/import exists) | Snapshots are a standard feature |
| Best for | Dev tools, scripting, log analysis, Linux practice | Malware analysis, isolated labs, testing full OSes |
| Safe for detonating malware? | **No** | Yes, with proper isolation and snapshots |

> **Key interview point:** WSL is a **convenience feature**, not a security sandbox.

## 3.9 Security Implications of WSL `[EXTRA]`

**1. Shared file access**

- Linux processes in WSL can read/write Windows files under `/mnt/c` using the **Windows user's permissions**. A malicious Linux script can therefore damage or steal Windows files the user can access.
- Windows users and admins can access Linux distro files, including by starting the distro as root (`wsl -u root`). Do not treat distro files as a secret vault.

**2. Interoperability**

- Linux can launch Windows executables, and Windows can launch Linux commands. This can blur the boundary and help bypass controls that only watch one side.

**3. Visibility gap**

- Security tools and analysts usually focus on Windows processes. Activity inside WSL may be **harder to monitor** unless endpoint tooling and logging cover it. Coverage varies by product and version. Verify what your tools actually see.
- Useful artefacts: `wsl.exe` process creation, WSL-related services, files of a distro in the user profile, and unexpected distro installs.

**4. Untrusted tools and scripts**

- Piping unknown scripts into a shell (for example, `curl ... | bash`) is risky anywhere, including WSL.
- Security tools downloaded from the internet should be verified (source, hash, signature) before use.

**5. Attacker abuse**

- `wsl.exe` is documented in community lists of Windows binaries that can execute other commands (LOLBAS) and relates to ATT&CK **Indirect Command Execution** (T1202). Researchers have also reported malware that uses WSL to run Linux payloads.

**6. Network behaviour**

- In WSL 2 default mode the Linux side sits behind a virtual NAT. This affects how network scans and monitoring appear, and how firewall rules apply. Newer versions offer other networking modes, so confirm your own setup.

**Defender checklist**

| Control | Purpose |
|---------|---------|
| Allow WSL only where there is a business or learning need | Reduces attack surface |
| Keep Windows and WSL updated (`wsl --update`) | Patch known issues |
| Least privilege on the Windows account | Limits what Linux processes can touch via `/mnt/c` |
| Process creation logging (4688 with command line, Sysmon) | Detect `wsl.exe` use and unusual parent processes |
| Application control (AppLocker/WDAC) | Restrict who can run `wsl.exe` where appropriate |
| Review installed distros periodically | Spot unauthorised environments |
| Consider `/etc/wsl.conf` options (disable automount/interop) for sensitive use | Reduce cross-environment access |
| Check vendor documentation for WSL-aware endpoint monitoring | Coverage depends on product and version |

> **Limitation:** None of these are perfect. Hardening settings can be changed by someone with enough rights, and monitoring depends on configuration.

## 3.10 WSL Troubleshooting

| Problem / message | Likely cause | What to try |
|-------------------|--------------|-------------|
| `'wsl' is not recognized` | Very old Windows build or feature not present | Check `winver`; update Windows; enable optional feature |
| "The Windows Subsystem for Linux optional component is not enabled" | Feature disabled | Enable *Windows Subsystem for Linux* and restart |
| "Please enable the Virtual Machine Platform Windows feature and ensure virtualization is enabled in the BIOS" (error code such as `0x80370102`) | Virtual Machine Platform off or virtualization disabled | Enable the feature; enable virtualization in BIOS/UEFI; restart |
| "WSL 2 requires an update to its kernel component" | Old kernel package | Run `wsl --update` |
| Access denied during install/feature change | Not elevated | Open PowerShell as Administrator |
| Distro will not start or hangs | Stuck state or resource issue | `wsl --shutdown`, then retry; check disk space |
| `Permission denied` on `/mnt/c/...` | Windows permissions apply | Check the file's Windows ACLs and your user rights |
| No distro installed | Only the WSL feature present | `wsl --list --online`, then `wsl --install -d <Distro>` |
| Forgot Linux password | Linux accounts are separate | From PowerShell: `wsl -u root`, then `passwd <username>` (shows why WSL is not a secure vault) |
| Network or DNS problems in WSL 2 | Virtual network or VPN interplay | Restart with `wsl --shutdown`; check VPN and DNS settings; consult current docs |

---

# 4. Hands-on Exercises

> **Rules for all exercises**
> - Do these **only on my own computer or an authorized lab.**
> - These are **read-only / low-risk** exercises. Do **not** stop services, delete distros, or change policies.
> - `[SAMPLE OUTPUT]` is illustrative. Write my real observations in the `[MY NOTES]` boxes.

---

## Exercise 1: Open MMC and Explore a Snap-in

**1. Objective**
Open MMC, add the Event Viewer and Services snap-ins to a custom console, and save it.

**2. Steps / Commands**

1. Press `Win + R`, type `mmc`, press Enter. Accept UAC if prompted.
2. Press `Ctrl + M` (File → Add/Remove Snap-in).
3. Add **Event Viewer** → choose **Local computer** → Finish.
4. Add **Services** → choose **Local computer** → Finish. Click **OK**.
5. In Event Viewer, expand **Windows Logs** and click **Application**, **System**, and **Security**.
6. In Services, sort by **Startup Type** and double-click one service to read its properties (do **not** change anything; click **Cancel**).
7. **File → Save As…** and save as `MyFirstConsole.msc` in Documents.
8. Close and reopen by double-clicking the file.

Optional comparison: run `eventvwr.msc` and `services.msc` separately from `Win + R`.

**3. Expected observations**

- The blank console becomes a two-tool console after adding snap-ins.
- Application and System logs open for a normal user; **Security** log may show an access error if MMC is not elevated.
- The saved `.msc` reopens with the same snap-ins.
- `eventvwr.msc` opens MMC with only Event Viewer loaded.

**4. Interpretation**

- MMC is the container; Event Viewer and Services are the tools.
- A `.msc` file remembers the console layout, not your logs or services.
- Access differences show that permissions come from the **user token**.

**5. Security lesson**

- The Security log contains logon and audit events and is protected for a reason.
- A saved `.msc` file is a small file that tells Windows what to load, so be careful with `.msc` files from unknown sources.

**`[MY NOTES]`**

```text
Date: ____________
Windows edition/version: ____________
Could I open the Security log without admin? ____________
Observations: ____________________________________________
```

---

## Exercise 2: Review Local Administrators (MMC + Command Line)

**1. Objective**
Practise a basic account-review task with GUI and command-line methods.

**2. Steps / Commands**

*GUI (Pro/Enterprise/Education):*

1. `Win + R` → `compmgmt.msc`
2. **System Tools → Local Users and Groups → Groups → Administrators**

*Command line (works on all editions):*

```cmd
net localgroup Administrators
```

```powershell
Get-LocalGroupMember -Group "Administrators"
```

**3. Expected observations**

- A list of accounts or groups that have local administrator rights.
- GUI and command-line lists should match.

```text
[SAMPLE OUTPUT - illustrative only]
Alias name     Administrators
Comment        Administrators have complete and unrestricted access...

Members
-------------------------------------------
Administrator
<YourUserName>
The command completed successfully.
```

**4. Interpretation**

- Every member can fully control this computer.
- If my everyday account is a member, malware running as me would also have admin potential (subject to UAC).

**5. Security lesson**

- Least privilege: use a standard account day to day; elevate only when necessary.
- Account review is a routine hygiene and incident-response step.

**`[MY NOTES]`**

```text
Accounts found: ____________________
Anything unexpected? ____________________
```

---

## Exercise 3: Check the Installed WSL Configuration

**1. Objective**
Find out whether WSL is installed and which version/settings are in use.

**2. Steps / Commands**

Open PowerShell (normal user is fine):

```powershell
wsl --status
wsl --version
wsl --help
```

**3. Expected observations**

- `--status`: default distro and default WSL version (if WSL is set up).
- `--version`: version lines for WSL components (newer builds only).
- `--help`: usage and options for my version.
- If WSL is **not installed**, the commands may show help text or an install prompt, or a "not recognized" message on very old builds.

```text
[SAMPLE OUTPUT - illustrative only]
Default Distribution: Ubuntu
Default Version: 2
```

**4. Interpretation**

- Default Version 2 means new distros will be WSL 2 (VM-based).
- No output or errors tell me the feature or distro is missing, so continue to Exercise 5.

**5. Security lesson**

- Confirming whether WSL exists on a machine is part of asset and baseline checking.
- Version info feeds patch management.

**`[MY NOTES]`**

```text
WSL installed? Y / N
WSL version: ____________
Default distro: ____________
Default version: ____________
```

---

## Exercise 4: List Installed Linux Distributions

**1. Objective**
See which distros exist and their state and version.

**2. Steps / Commands**

```powershell
wsl --list --verbose
wsl --list --online
```

**3. Expected observations**

- A table with `NAME`, `STATE`, `VERSION`; `*` marks the default.
- `--online` lists distros that can be installed (needs internet).

```text
[SAMPLE OUTPUT - illustrative only]
  NAME      STATE           VERSION
* Ubuntu    Stopped         2
```

**4. Interpretation**

- `Stopped` just means idle; `Running` means the distro is active.
- `VERSION 1` vs `2` tells me which architecture the distro uses.

**5. Security lesson**

- Unknown or extra distros on a managed machine are worth investigating.
- Installing a distro is a policy decision, not just a convenience.

**`[MY NOTES]`**

```text
Distros present: ____________________
States / versions: ____________________
```

---

## Exercise 5: Launch WSL and Run Basic Linux Commands

**1. Objective**
Start a Linux shell, run basic commands, and observe Windows-Linux file access.

**2. Steps / Commands**

```powershell
wsl                       # start default distro
```

Inside Linux:

```bash
whoami
hostname
pwd
cat /etc/os-release
uname -a
id
cd ~
mkdir wsl-notes-test
cd wsl-notes-test
echo "hello from WSL" > hello.txt
cat hello.txt
ls -la
ls /mnt/c                 # list Windows C: drive (read-only exploration)
explorer.exe .            # open this Linux folder in Windows File Explorer
exit
```

**3. Expected observations**

- Prompt changes to a Linux prompt; `whoami` shows my Linux username.
- `/etc/os-release` shows the distro name; `uname -a` shows a Linux kernel.
- `ls /mnt/c` shows Windows folders.
- File Explorer opens a `\\wsl$\...` or `\\wsl.localhost\...` path.

**4. Interpretation**

- I am in a Linux environment, but Windows files are reachable.
- Linux username and Windows username are separate identities.

**5. Security lesson**

- Cross-environment access means a bad script in WSL can touch Windows files I can access.
- WSL is not isolation; do not run untrusted code here assuming it is "contained."

**Cleanup (optional):**

```bash
rm -r ~/wsl-notes-test
```

**`[MY NOTES]`**

```text
Linux distro/version: ____________
Kernel line: ____________
Could I see /mnt/c? Y / N
Explorer path shown: ____________
```

---

## Exercise 6: Troubleshoot Missing Features or Insufficient Permissions

**1. Objective**
Practise diagnosing the most common blockers.

**2. Steps / Commands**

*A. Missing snap-in (e.g., `lusrmgr.msc` or `gpedit.msc`)*

```cmd
winver
```
Check the edition. Home editions lack some tools. Use `net localgroup` / `Get-LocalUser` instead.

*B. MMC "Access denied"*

1. Close MMC. Reopen via Start → right-click **mmc** → **Run as administrator**.
2. Retry the snap-in. Check whether my account is an admin (Exercise 2).

*C. WSL not working or missing*

```powershell
# Run PowerShell as Administrator
Get-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
Get-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
wsl --update
wsl --shutdown
```
Check virtualization: **Task Manager → Performance → CPU** and look for "Virtualization: Enabled".

*D. WSL install attempt (only if I intend to install it on my own PC)*

```powershell
wsl --list --online
wsl --install
```

**3. Expected observations**

- Edition explains missing tools.
- Elevated MMC resolves access errors for admin-capable accounts.
- Feature state shows `Enabled` / `Disabled`; virtualization status appears in Task Manager.

```text
[SAMPLE OUTPUT - illustrative only]
FeatureName : Microsoft-Windows-Subsystem-Linux
State       : Enabled
```

**4. Interpretation**

- Most failures come from **edition, missing feature, disabled virtualization, or lack of elevation**.
- A systematic order (edition → permissions → feature → BIOS/virtualization → update) is faster than guessing.

**5. Security lesson**

- Needing admin rights to install or change these features is a **good control**. Do not bypass it with risky workarounds.
- Do not disable UAC or security settings just to make something work.

**`[MY NOTES]`**

```text
Problem encountered: ____________________
Cause: ____________________
Fix / next step: ____________________
```

---

# 5. Cybersecurity Relevance

## 5.1 Windows Administration and Security Operations `[EXTRA]`

| Area | How MMC / WSL appears |
|------|------------------------|
| Incident response | Event Viewer for logon and service events; Services and Task Scheduler for suspicious persistence; Local Users and Groups for new accounts |
| System hardening | Local Security Policy / Group Policy for audit settings and rights |
| Account review | Local Administrators membership and shares |
| Log analysis | Export logs, then analyse with Linux tools in WSL (`grep`, `awk`, `jq`) |
| Scripting/automation | Python and shell scripts in WSL for parsing data |
| Learning | Practise Linux commands needed for HTB and CTF labs from a Windows laptop |

## 5.2 How Administrators Use MMC

- Build **custom consoles** with just the tools needed for a task or role.
- Connect to remote computers from **Computer Management** (with proper authorisation and rights).
- Use **RSAT** snap-ins to manage Active Directory and other server roles.
- Save and share `.msc` files for standard procedures.

## 5.3 How Analysts Can Use Linux Tools Through WSL

- **Text processing:** `grep`, `awk`, `sed`, `cut`, `sort`, `uniq`
- **Data/logs:** `jq` for JSON logs, `less`, `tail`
- **File triage:** `file`, `strings`, `xxd`, `sha256sum`
- **Networking utilities:** `curl`, `dig`, `whois`, `openssl`
- **Scripting:** `python3`, `bash`

```bash
# Example: count most common failed-logon source IPs from an exported text log (illustrative)
grep "Failed" auth_export.txt | awk '{print $NF}' | sort | uniq -c | sort -nr | head
```

> This is a general example. Real log formats differ, so field positions must be adjusted.

**Limits:** WSL is for **analysis and tooling**, not for safe malware detonation. Use a properly isolated VM or sandbox for that.

## 5.4 Risks

| Risk | Explanation | Control |
|------|-------------|---------|
| **Excessive privileges** | Admin accounts used daily make any compromise much worse | Standard daily account, separate admin account, UAC on |
| **Unsafe file sharing** | `/mnt/c` and `\\wsl$` let tools in one environment touch the other | Keep sensitive files out of casual reach; consider limiting automount/interop; least privilege |
| **Untrusted tools/scripts** | Downloading and running unknown scripts or installers | Verify sources and hashes; test in an isolated VM |
| **Misuse of trusted admin tools** | Stolen credentials + MMC/remote management = lateral movement | Restrict remote admin paths; monitor; use MFA where available |
| **Monitoring gaps** | WSL activity may be less visible | Log process creation; confirm tool coverage |
| **Unmanaged environments** | Users install extra distros and tools | Policy, inventory, periodic review |

## 5.5 Defensive Controls and Their Limitations

| Control | Helps with | Limitation |
|---------|-----------|------------|
| Least privilege | Limits damage | Needs ongoing management; users may request exceptions |
| UAC | Slows silent elevation | Not a complete defence; users can click through prompts |
| Application control (AppLocker/WDAC) | Restrict tool execution | Requires careful rule design; can break workflows |
| Audit logging / Sysmon | Detection and investigation | Needs storage, tuning, and someone reviewing alerts |
| Patching / `wsl --update` | Fix known issues | Does not stop misuse with valid credentials |
| Policy on WSL use | Reduce unmanaged risk | Needs enforcement and awareness |
| MMC user modes | Prevent accidental changes | Not a security boundary |

## 5.6 Educational vs. Practical vs. Industry Relevance

| Level | MMC | WSL |
|-------|-----|-----|
| **Educational knowledge** | Container vs snap-in; `.msc` files; admin rights | WSL 1 vs 2; WSL vs VM; file integration |
| **Practical skill** | Build a custom console; review Event Viewer and local admins | Run `wsl` commands; use Linux tools on Windows files |
| **Industry relevance** | Admin consoles, RSAT, monitoring `mmc.exe` and `.msc` activity | Developer/analyst tooling; policy and monitoring of WSL usage |

> **Honest scope:** These notes build **foundations**. Enterprise-scale monitoring, large-environment hardening, and advanced detection engineering require much more experience than this section covers.

---

# 6. Interview Preparation

> For important questions: **Short answer** → **Detailed explanation** → **Realistic example**.

---

## 6.1 Basic Definitions

**Q1. What is MMC?** `[IMPORTANT]`

- **Short answer:** MMC (Microsoft Management Console) is a Windows host application (`mmc.exe`) that displays administrative tools called snap-ins in one consistent interface.
- **Detailed explanation:** MMC does not manage anything itself. It provides the window, menus, and console tree. Snap-ins such as Event Viewer or Services supply the actual functionality. A configuration can be saved as a `.msc` file.
- **Example:** An admin builds one console with Event Viewer, Services, and Task Scheduler, then opens it during troubleshooting instead of launching three separate tools.

**Q2. What is a snap-in?**

- **Short answer:** A snap-in is a tool module that plugs into MMC to add a management function.
- **Example:** Event Viewer (`eventvwr.msc`) is a snap-in loaded in MMC.

**Q3. What is a `.msc` file?**

- **Short answer:** A saved MMC console configuration that lists the snap-ins to load.
- **Note:** It contains configuration, not the data the tools manage.

**Q4. What is WSL?** `[IMPORTANT]`

- **Short answer:** Windows Subsystem for Linux lets you run Linux distributions and command-line tools directly on Windows.
- **Detailed explanation:** It removes the need for a dual-boot or manually built VM for many tasks. Users get a Linux shell with access to Windows files, and Windows can launch Linux commands.
- **Example:** An analyst on a Windows laptop runs `grep` and `awk` through WSL to parse exported logs.

**Q5. What is a Linux distribution in WSL?**

- **Short answer:** A packaged Linux environment such as Ubuntu or Debian installed under WSL, each with its own filesystem and users.

---

## 6.2 Conceptual Understanding

**Q6. What is the difference between MMC and a snap-in?** `[IMPORTANT]`

- **Short answer:** MMC is the container; snap-ins are the tools inside.
- **Detailed explanation:** MMC runs with the access token of the user who launched it. What you can do depends on the snap-in and your permissions. A blank MMC window has no management capability.
- **Example:** Opening `mmc` shows an empty console. After adding Services, you can view services; changing them still requires admin rights.

**Q7. How do WSL 1 and WSL 2 differ?** `[IMPORTANT]`

- **Short answer:** WSL 1 translates Linux system calls to Windows; WSL 2 runs a real Linux kernel in a lightweight virtual machine.
- **Detailed explanation:** WSL 2 has better Linux compatibility and faster performance inside its own filesystem, while WSL 1 can be faster for files on Windows drives. WSL 2 uses a virtual disk and a virtual network by default.
- **Example:** A developer who needs Docker-style or kernel-dependent tools chooses WSL 2; someone working mainly with files on `C:\` might consider WSL 1.

**Q8. How is WSL different from a traditional VM?** `[IMPORTANT]`

- **Short answer:** WSL is tightly integrated with Windows and optimised for convenience; a traditional VM is a fully separate machine with stronger isolation.
- **Detailed explanation:** WSL shares files and can run Windows/Linux programs across the boundary. A VM provides snapshots and a clearer isolation boundary, which is why malware analysis belongs in a VM, not WSL.
- **Example:** Use WSL to analyse log files. Use an isolated VM with snapshots to analyse a suspicious executable.

**Q9. Why are admin tools like MMC relevant for attackers?**

- **Short answer:** They are trusted, built in, and powerful, so abuse blends with normal admin activity.
- **Detail:** The problem is typically stolen credentials plus allowed remote management, not a flaw in the tool itself.

**Q10. Do user modes in MMC secure a console?**

- **Short answer:** No. They limit accidental changes. Real security comes from permissions, policy, and monitoring.

---

## 6.3 Practical Commands

**Q11. How do you open MMC and add a snap-in?**

- **Answer:** `Win + R` → `mmc` → `Ctrl + M` → select the snap-in → Add → OK. Save with File → Save As.

**Q12. How do you open Event Viewer or Services directly?**

- **Answer:** `eventvwr.msc` and `services.msc`.

**Q13. How do you check WSL status and list distros?**

- **Answer:** `wsl --status` for default distro and version; `wsl --list --verbose` for names, states, and versions; `wsl --version` for component versions (newer builds); `wsl --help` for options available on that system.

**Q14. How do you start a distro and run a single command?**

- **Answer:** `wsl` starts the default distro; `wsl -d <Name>` starts a specific one; `wsl <command>` runs one Linux command and returns to Windows.

**Q15. How do you access Windows files from Linux and Linux files from Windows?**

- **Answer:** Linux → Windows: `/mnt/c/...`. Windows → Linux: `\\wsl$\<Distro>` or `\\wsl.localhost\<Distro>`.

**Q16. How would you check local administrators from the command line?**

- **Answer:** `net localgroup Administrators` or PowerShell `Get-LocalGroupMember -Group "Administrators"`.

---

## 6.4 Troubleshooting

**Q17. `gpedit.msc` or `lusrmgr.msc` is not found. Why?** `[IMPORTANT]`

- **Short answer:** Most likely a Windows Home edition.
- **Detailed explanation:** Some management consoles are not included in Home. Check with `winver`, then use command-line alternatives or a different edition.
- **Example:** On Home, use `net user` and `net localgroup Administrators` instead of `lusrmgr.msc`.

**Q18. A snap-in shows "Access is denied." What do you check?**

- **Answer:** Is MMC elevated? Is the account in the right group? Is it a remote connection (firewall/rights)? Is a policy blocking it?

**Q19. `wsl --install` fails or WSL 2 will not start. What do you check?** `[IMPORTANT]`

- **Short answer:** Elevation, Windows features, and virtualization.
- **Detailed explanation:** Run as Administrator; confirm *Windows Subsystem for Linux* and *Virtual Machine Platform* are enabled; confirm virtualization is enabled in BIOS/UEFI; run `wsl --update`; restart.
- **Example:** An error about virtualization after `wsl --install` usually means virtualization is off in firmware or the Virtual Machine Platform feature is disabled.

**Q20. A remote Computer Management connection says "RPC server is unavailable." Why?**

- **Answer:** Possible firewall blocking, remote service not running, name resolution problems, or wrong network path. Confirm authorisation before troubleshooting.

**Q21. `wsl` is not recognised. What now?**

- **Answer:** Check Windows version (`winver`), update Windows, confirm the optional feature, and run `wsl --help` once available.

---

## 6.5 Security Scenarios

**Q22. During triage you see `mmc.exe` opened a `.msc` file from the Downloads folder, launched from an email attachment. What do you do?** `[IMPORTANT]`

- **Short answer:** Treat it as suspicious: preserve evidence, isolate if needed, and investigate the file and process tree.
- **Detailed explanation:** Admin `.msc` files rarely come from Downloads or email. Check the parent process, command line, file hash, user, and any child processes or network connections. Review related logons and persistence. Escalate per incident process.
- **Example:** The parent is a mail client, the `.msc` came from an external sender, and `mmc.exe` spawns a command shell. That pattern justifies escalation and containment.

**Q23. You find `wsl.exe` being launched by a Word process on a workstation where nobody uses WSL. What is your concern?** `[IMPORTANT]`

- **Short answer:** Possible malicious use of WSL to run Linux payloads, likely after a malicious document executed.
- **Detailed explanation:** Unusual parent/child relationships and a user with no business need for WSL are strong indicators. Collect the command line, check installed distros (`wsl -l -v`) and files, review network activity, and contain the host if needed.
- **Example:** `winword.exe → wsl.exe → bash -c "curl ... | sh"` is a high-priority alert.

**Q24. A colleague wants admin rights on their daily account "to use MMC." What do you advise?**

- **Answer:** Use a standard account normally and elevate only for specific tasks, or provide a separate admin account. Explain least privilege and the risk of a compromised daily account.

**Q25. Is it safe to run a downloaded exploit or malware sample inside WSL?** `[IMPORTANT]`

- **Short answer:** No.
- **Detailed explanation:** WSL is integrated with Windows and can access Windows files and run Windows programs. It is not an isolation boundary. Use a dedicated, isolated VM with snapshots, and no shared folders.
- **Example:** Analysing a suspicious script safely: copy it into an isolated VM with network access restricted, not into WSL on the daily workstation.

**Q26. How would you reduce the risk of WSL on corporate laptops?**

- **Answer:** Allow it only where needed; patch it; apply least privilege; log process creation (4688/Sysmon); use application control where appropriate; review distro installs; check vendor tooling for WSL visibility; consider limiting interop/automount for sensitive groups.

---

## 6.6 Advanced Follow-up Questions

**Q27. How can you detect misuse of MMC in an environment?**

- **Answer:** Collect process creation with command line; alert on `mmc.exe` loading `.msc` from user-writable paths or being spawned by Office, browsers, or scripts; monitor remote admin activity; alert on security log clearing (Event ID 1102); baseline normal admin behaviour. Expect false positives from legitimate admins, so tune alerts.

**Q28. Why is "just disable MMC/WSL" not a complete defence?**

- **Answer:** They are legitimate tools with business value, and attackers have many alternatives. Better controls combine least privilege, hardening, logging, and detection. Blocking may be reasonable for specific user groups.

**Q29. Explain how WSL file access works in both directions and its security impact.**

- **Answer:** Windows drives are mounted under `/mnt/<drive>`; Windows reaches Linux files via `\\wsl$` / `\\wsl.localhost`. The Linux side acts with the Windows user's permissions on Windows files, so malicious Linux code can affect those files. Admins and the Windows user can also access distro data.

**Q30. What are the limits of your knowledge here?** *(honesty question)*

- **Answer:** I understand the concepts, can perform the basic tasks in my own lab, and can describe common security implications. I have not managed large enterprise environments, so I would rely on documentation, team standards, and senior colleagues for production-scale decisions.

**Q31. Name MITRE ATT&CK areas related to these tools.**

- **Answer:** MMC-related execution (T1218.014), DCOM remote services (T1021.003), indirect command execution (T1202) for `wsl.exe`-style abuse. I would verify IDs on the ATT&CK website before quoting them in a report.

---

# 7. Quick Revision

## 7.1 Key Definitions

| Term | One-line definition |
|------|---------------------|
| MMC | Host console (`mmc.exe`) that displays admin snap-ins |
| Snap-in | Tool module loaded inside MMC |
| `.msc` | Saved MMC console file |
| Author mode | Console mode that allows editing |
| RSAT | Remote Server Administration Tools for extra admin snap-ins |
| WSL | Run Linux distros on Windows |
| Distro | Packaged Linux environment (Ubuntu, Debian…) |
| WSL 1 | Syscall translation layer |
| WSL 2 | Lightweight VM with a real Linux kernel |
| Interop | Running Windows programs from Linux and vice versa |
| `/mnt/c` | Windows C: drive as seen from Linux |
| `\\wsl$` | Windows path to Linux files |

## 7.2 Important Commands

| Command | Purpose |
|---------|---------|
| `mmc` | Open blank MMC console |
| `mmc /a` | Open console in author mode |
| `eventvwr.msc` | Event Viewer |
| `services.msc` | Services |
| `compmgmt.msc` | Computer Management |
| `lusrmgr.msc` | Local Users and Groups (not on Home) |
| `taskschd.msc` | Task Scheduler |
| `net localgroup Administrators` | List local admins |
| `Get-LocalGroupMember -Group "Administrators"` | PowerShell equivalent |
| `wsl --status` | Default distro/version info |
| `wsl --list --verbose` | Installed distros, state, version |
| `wsl --list --online` | Installable distros |
| `wsl --version` | Component versions (newer builds) |
| `wsl --help` | Options for this system |
| `wsl` / `wsl -d <Name>` | Start default / specific distro |
| `wsl --update` | Update WSL |
| `wsl --shutdown` | Stop all distros and the WSL 2 VM |
| `wsl --unregister <Name>` | **Deletes** a distro. Handle with extreme care |

## 7.3 MMC vs. Snap-ins

| | MMC | Snap-in |
|--|-----|---------|
| Role | Container | Tool |
| Works alone? | No | Needs MMC (host) |
| Example | `mmc.exe` | Event Viewer |
| Permissions | Uses the launching user's token | Depends on snap-in + user rights |

## 7.4 WSL vs. a Traditional VM

| | WSL | Traditional VM |
|--|-----|----------------|
| Setup | Easy | More work |
| Integration | Tight with Windows | Limited by default |
| Isolation | Weaker | Stronger |
| Snapshots | Export/import only | Native snapshots |
| Malware analysis | **Not suitable** | Suitable with proper isolation |

## 7.5 Common Mistakes

- Thinking MMC is the tool rather than the host for tools.
- Assuming every `.msc` exists on every Windows edition.
- Forgetting to run MMC elevated when admin rights are needed.
- Treating WSL as a sandbox or secure vault.
- Running `wsl --unregister` without a backup (permanent data loss).
- Assuming WSL commands behave the same on every Windows/WSL version.
- Storing project files in `/mnt/c` under WSL 2 and wondering why it is slow.
- Copying `curl | bash` commands from untrusted sources.
- Disabling UAC to "fix" prompts.
- Presenting basic exercises as enterprise-level experience.

## 7.6 Last-Minute Revision Checklist

- [ ] MMC = container, snap-ins = tools, `.msc` = saved config
- [ ] `Win + R` → `mmc` → `Ctrl + M`
- [ ] Remember at least five snap-ins and their `.msc` names
- [ ] Permissions come from the user token; use *Run as administrator* when needed
- [ ] Remote management = rights + firewall + connectivity
- [ ] WSL 1 = translation; WSL 2 = lightweight VM with real kernel
- [ ] `wsl --status`, `wsl --list --verbose`, `wsl --version`, `wsl`, `wsl --help`
- [ ] `/mnt/c` and `\\wsl$` for cross-access
- [ ] WSL is not a sandbox; use a VM for malware
- [ ] Attacker view: trusted tools + valid credentials
- [ ] Defender view: least privilege, UAC, logging (4688/Sysmon), monitoring, application control

---

# 8. Personal Learning and Completion Tracker

## 8.1 Concepts Understood

- [ ] What MMC is and why it exists
- [ ] Snap-in vs MMC vs `.msc`
- [ ] Common snap-ins and their purposes
- [ ] Console modes (author vs user modes)
- [ ] Admin permissions and UAC in relation to MMC
- [ ] Remote management requirements
- [ ] What WSL is
- [ ] WSL 1 vs WSL 2
- [ ] WSL vs traditional VM
- [ ] How Windows and Linux files are accessed across the environment
- [ ] WSL interoperability

## 8.2 Hands-on Exercises Completed

- [ ] Exercise 1: Open MMC and explore snap-ins
- [ ] Exercise 2: Review local administrators
- [ ] Exercise 3: Check WSL configuration
- [ ] Exercise 4: List installed distributions
- [ ] Exercise 5: Launch WSL and run Linux commands
- [ ] Exercise 6: Troubleshooting practice

## 8.3 Commands Practiced

- [ ] `mmc`
- [ ] `eventvwr.msc` / `services.msc` / `compmgmt.msc`
- [ ] `net localgroup Administrators`
- [ ] `Get-LocalGroupMember -Group "Administrators"`
- [ ] `wsl --status`
- [ ] `wsl --list --verbose`
- [ ] `wsl --list --online`
- [ ] `wsl --version`
- [ ] `wsl --help`
- [ ] `wsl` and basic Linux commands (`whoami`, `pwd`, `ls -la`, `cat /etc/os-release`, `uname -a`)

## 8.4 Security Knowledge Acquired

- [ ] How trusted admin tools can be abused
- [ ] Least privilege and UAC
- [ ] Logging concepts (4688, Sysmon, Security log, 1102)
- [ ] Risks of WSL file sharing and interoperability
- [ ] Why WSL is not a malware sandbox
- [ ] Basic defensive controls and their limitations
- [ ] Awareness of MITRE ATT&CK mapping (verified on the official site)

## 8.5 Interview Questions Practiced

- [ ] Basic definitions (Q1–Q5)
- [ ] Conceptual understanding (Q6–Q10)
- [ ] Practical commands (Q11–Q16)
- [ ] Troubleshooting (Q17–Q21)
- [ ] Security scenarios (Q22–Q26)
- [ ] Advanced follow-ups (Q27–Q31)
- [ ] Can explain MMC and WSL in under 60 seconds each

## 8.6 GitHub Documentation Completed

- [ ] Notes reviewed and aligned with the HTB section
- [ ] My own lab observations added (no copied HTB text)
- [ ] Screenshots added (no sensitive or personal data visible)
- [ ] Machine names, usernames, and IP addresses redacted
- [ ] Commit message written clearly
- [ ] File pushed to GitHub

## 8.7 Personal Lab Log

| Date | Task | Result | Lesson learned |
|------|------|--------|----------------|
|      |      |        |                |
|      |      |        |                |
|      |      |        |                |

## 8.8 Personal Reflection

```text
What I understood well:


What I found confusing:


What I want to revisit:


Questions for further study:

```

---

# 9. Further Reading

> Verify details against current official documentation, as features and commands change between versions.

- Microsoft Learn: documentation on Microsoft Management Console and WSL
- Hack The Box Academy: Windows Fundamentals module (the original source for this section)
- MITRE ATT&CK: techniques T1218.014, T1021.003, T1202 (verify current IDs)
- LOLBAS Project: catalogue of legitimate Windows binaries that can be abused

---

*Last updated: ____________ &nbsp;|&nbsp; Author: ____________*
