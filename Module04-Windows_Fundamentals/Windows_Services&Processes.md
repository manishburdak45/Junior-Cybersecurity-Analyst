<div align="center">

# HTB Academy — Windows Services, Service Permissions & Sessions

### Section 6 of the "Introduction to Windows" Learning Series

`Services Fundamentals` -> `sc.exe Enumeration` -> `Service Security Descriptors (SDDL)` -> `Get-Acl (ACL/SID)` -> `Interactive vs Non-Interactive Sessions`

</div>

---

## Overview

This document combines four related parts of the Windows Services & Processes section into a single reference: what a Windows Service is and how to manage it via `services.msc`, `sc.exe`, and PowerShell's `Get-Service`; how to inspect and modify service configuration and security descriptors using `sc.exe`; how to read the same permission information in a more readable form using PowerShell's `Get-Acl`; and finally, the distinction between interactive logon sessions and non-interactive service accounts (`SYSTEM`, `LocalService`, `NetworkService`).

> Note: Part 2 (Processes + LSASS) of this section was announced but not yet covered in the source material — it will be documented separately once that content is available. No exploitation occurred in this session; this is theory and guided command-line practice.

---

## Objective

1. Understand what a Windows Service is, how it's managed, and why its configuration matters for security.
2. Use `sc.exe` to query, modify, and inspect the security descriptor of a service from the command line.
3. Decode a Service Security Descriptor (SDDL) at a basic level.
4. Use PowerShell's `Get-Acl` to read the same permission data in human-readable form, plus SID information.
5. Understand the difference between an interactive logon session and a non-interactive service account, and know the three non-interactive account types by name and privilege level.

---

## Part 1 — Windows Services Fundamentals

### What Is a Windows Service?

A Windows Service is a **long-running background process** that performs a system function without waiting for a user to manually open an application.

```text
Windows Start/Boot
      |
      v
Service starts automatically
      |
      v
Runs in the background
      |
      v
User logs out
      |
      v
Service can continue running regardless
```

Services can start automatically at boot and keep running even after a user logs out.

### Where Services Are Used

Services handle a wide range of Windows functions:

- Networking
- System diagnostics
- User credential management
- Windows Updates
- Other core operating-system functions

Application developers can also install their own software as a service — the module's example is a network monitoring application running as a service on a server.

### Service Control Manager (SCM) and services.msc

Windows services are managed by the **Service Control Manager (SCM)**. The GUI tool for this is:

```text
services.msc
```

**Opening it:** `Win + R` -> type `services.msc` -> Enter.

**Information shown per service:**

```text
Service
 |-- Name
 |-- Description
 |-- Status
 |-- Startup Type
 `-- Runs under -> User/service account
```

> Concept: a pentester needs to know which service is running and under which user's privileges — service configuration misconfigurations are a common Windows privilege escalation vector.

### Service Status Values

| Status | Meaning |
|---|---|
| Running | Currently active |
| Stopped | Currently inactive |
| Paused | Temporarily paused |
| Starting / Stopping | Transition states |

### Startup Types

```text
Boot
 |
 |-- Automatic           -> starts automatically
 |-- Automatic (Delayed) -> starts after a delay
 `-- Manual               -> starts only when triggered
```

### The Three Service Categories

- Local Services
- Network Services
- System Services

### Who Can Modify Services?

Creating, modifying, or deleting a service normally requires **administrative privileges**, precisely because of this risk:

```text
Low-privileged user
       |
       v
Modifies a service's configuration
       |
       v
Service runs with higher privileges
       |
       v
Potential privilege escalation
```

> Critical Concept: service permission misconfigurations are a common privilege escalation vector on Windows systems. This was introduced conceptually here; actual exploitation is deferred to a dedicated later module/lab.

### Important Windows Services/Processes

| Component | Basic Role |
|---|---|
| `smss.exe` | Session Manager Subsystem |
| `csrss.exe` | Client Server Runtime |
| `wininit.exe` | Windows initialization |
| `logonui.exe` | User login interface |
| `lsass.exe` | User logon/authentication & security policy |
| `services.exe` | Manages starting/stopping of services |
| `winlogon.exe` | Logon, user profile loading, workstation locking |
| `System` | Windows kernel-related system process |
| `svchost.exe` | Hosts DLL-based services |

`lsass.exe`, `services.exe`, `svchost.exe`, and `winlogon.exe` are flagged as the priority items to recognize now; `lsass.exe` receives dedicated coverage in the Processes part of this section.

### Command-Line Service Management — sc.exe and Get-Service

```cmd
sc
```
Windows' native command-line utility for querying/managing services via the SCM.

```powershell
Get-Service
```
Retrieves service information from PowerShell.

**Filtering to running services:**

```powershell
Get-Service | ? {$_.Status -eq "Running"} | select -First 2 | fl
```

| Segment | Function |
|---|---|
| `Get-Service` | Retrieve all service information |
| `\|` | Pipe output to the next command |
| `? {$_.Status -eq "Running"}` | Filter to only `Running` services (`?` = `Where-Object`) |
| `select -First 2` | Take only the first 2 results |
| `\| fl` | Display using `Format-List` (full detail per item) |

```text
Get-Service -> All services -> Filter Running -> Take first 2 -> Display detailed list
```

### Cybersecurity Enumeration Mindset (Services)

```text
Which services are installed?
        |
        v
Which are running?
        |
        v
Which start automatically?
        |
        v
Which account does each run under?
        |
        v
Is the configuration secure?
        |
        v
Any permission misconfiguration?
```

| Layer | Focus |
|---|---|
| Fundamental | What is a service? |
| Practical | `services.msc`, `Get-Service`, `sc.exe` |
| Security | Inspecting service account + configuration |
| Pentesting | Identifying misconfigured services as escalation paths |
| Blue Team | Identifying/investigating unexpected/suspicious services |

**Core chain to remember:** `Service -> SCM -> services.msc -> Get-Service -> service misconfiguration = potential security risk.`

---

## Part 2 — Examining Services Using sc.exe

`sc.exe` (Service Control) enables command-line work with services: viewing configuration and status, starting/stopping, changing configuration, and viewing permissions/security descriptors — enabling quick enumeration and scripting instead of manual GUI inspection.

### sc qc — Query Configuration

```cmd
sc qc wuauserv
```

| Part | Meaning |
|---|---|
| `sc` | Service Control |
| `qc` | Query Configuration |
| `wuauserv` | Service name |

**Output:**

```text
SERVICE_NAME: wuauserv
TYPE               : 20  WIN32_SHARE_PROCESS
START_TYPE         : 3   DEMAND_START
ERROR_CONTROL      : 1   NORMAL
BINARY_PATH_NAME   : C:\WINDOWS\system32\svchost.exe -k netsvcs -p
LOAD_ORDER_GROUP   :
TAG                : 0
DISPLAY_NAME       : Windows Update
DEPENDENCIES       : rpcss
SERVICE_START_NAME : LocalSystem
```

**Reading the output like a security analyst:**

| Field | Value | Meaning |
|---|---|---|
| `SERVICE_NAME` | `wuauserv` | Internal service name |
| `DISPLAY_NAME` | `Windows Update` | Human-readable name |
| `BINARY_PATH_NAME` | `C:\WINDOWS\system32\svchost.exe -k netsvcs -p` | Executable/command run when the service starts — an extremely important enumeration field |
| `SERVICE_START_NAME` | `LocalSystem` | The account context the service runs under — `LocalSystem` is a high-privilege account |
| `DEPENDENCIES` | `rpcss` | This service depends on `rpcss` |

```text
Service -> LocalSystem -> High privileges
```

If a service's configuration/permissions are weak, the security impact can be serious given the privilege level it runs at.

> Question Clue: `SERVICE_NAME` vs `DISPLAY_NAME` is a distinction worth remembering precisely, since it comes up directly in module questions.

### Querying a Remote Machine

```cmd
sc \\hostname_or_ip query ServiceName
```

```text
Your machine -> sc -> Remote Windows machine -> Service
```

Useful for authorized remote administration/enumeration.

### sc stop

```cmd
sc stop wuauserv
```

**Observed result for a normal (non-elevated) user:**

```text
[SC] OpenService FAILED 5:

Access is denied.
```

**Why:** the account lacked the permissions required to stop the service. An elevated/administrative context would be permitted.

```text
Command correct + Insufficient privilege -> Access is denied
```

This exact pattern — correct syntax, insufficient permission — is a very common troubleshooting scenario.

### sc config — Modifying Configuration

```cmd
sc config wuauserv binPath=C:\Winbows\Perfectlylegitprogram.exe
```

In the module's example, this successfully changed the configuration:

```text
BINARY_PATH_NAME : C:\Winbows\Perfectlylegitprogram.exe
```

**Security meaning:**

```text
Service -> BINARY_PATH_NAME -> Program to execute
```

If an unauthorized user can modify a service's configuration, they can potentially manipulate what executes when that service runs — this is exactly why service permission enumeration matters for privilege escalation analysis.

### sc sdshow — Service Security Descriptor

```cmd
sc sdshow wuauserv
```

**Output:**

```text
D:(A;;CCLCSWRPLORC;;;AU)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;SY)
```

This is the **Service Security Descriptor**. Windows securable objects have a security descriptor containing:

- Owner
- Primary group
- DACL
- SACL

**DACL vs SACL:**

| | DACL | SACL |
|---|---|---|
| Full name | Discretionary Access Control List | System Access Control List |
| Purpose | Who is allowed/denied access | Which access attempts get audited/logged |

This module focuses mainly on the DACL.

### SDDL — Security Descriptor Definition Language

The format `sc sdshow` outputs is called **SDDL**. It should not be read left-to-right blindly — it's decoded in parts.

**Decoding `D:(A;;CCLCSWRPLORC;;;AU)` at a basic level:**

| Segment | Meaning |
|---|---|
| `D:` | Introduces the DACL section |
| `A` | Allow (access is allowed) |
| `AU` | Security principal = Authenticated Users |

**Permission codes seen in this example** (not necessary to memorize at this stage — the point is recognizing SDDL as a compressed representation of service permissions):

```text
CC -> SERVICE_QUERY_CONFIG
LC -> SERVICE_QUERY_STATUS
SW -> SERVICE_ENUMERATE_DEPENDENTS
RP -> SERVICE_START
LO -> SERVICE_INTERROGATE
RC -> READ_CONTROL
```

**Security Principal:** the segment at the end (e.g., `;;;AU`) identifies which user/group/security principal the permissions apply to. Security principals can be users or groups.

### sc.exe Command Cheat Sheet

```text
sc qc SERVICE           -> Query service configuration
sc query SERVICE        -> Query service status
sc stop SERVICE         -> Stop service
sc config SERVICE ...   -> Modify service configuration
sc sdshow SERVICE       -> Show service security descriptor
```

**Question wording -> command mapping:**

| Question Wording | Command |
|---|---|
| query configuration | `sc qc` |
| service status | `sc query` |
| stop service | `sc stop` |
| modify configuration | `sc config` |
| permissions/security descriptor | `sc sdshow` |

### Cybersecurity Workflow for Service Investigation

```text
1. Identify the service
       |
2. sc qc SERVICE
       |
3. What is the executable path?
       |
4. What account does it run as?
       |
5. What are its dependencies?
       |
6. sc sdshow SERVICE
       |
7. Who can control it?
       |
8. Is the configuration secure?
```

`sc` is not just an administration tool — it's directly useful for security enumeration. In a suspected-malware scenario, `sc` is commonly used to quickly search/analyze newly created or targeted services.

**Part 2 core takeaways:** `sc qc` = configuration, `sc query` = status, `sc stop` = stop, `sc config` = modify, `sc sdshow` = security descriptor (output format = SDDL).

---

## Part 3 — Examining Service Permissions Using Get-Acl

So far this section has covered: `services.msc` (GUI), `sc qc` (configuration), `sc sdshow` (security descriptor). This part covers reading the same kind of permission data via PowerShell.

### What Get-Acl Does

```powershell
Get-Acl
```

Retrieves Access Control List (ACL) information for an object/resource. For a service, rather than targeting the service directly, `Get-Acl` targets its **Registry entry**.

### Service Registry Location

```text
HKLM:\System\CurrentControlSet\Services\wuauserv
```

```text
HKLM -> System -> CurrentControlSet -> Services -> wuauserv
```

The `wuauserv` service's configuration is stored at this Registry path.

### Main Command

```powershell
Get-Acl -Path HKLM:\System\CurrentControlSet\Services\wuauserv | Format-List
```

| Part | Function |
|---|---|
| `Get-Acl` | Retrieve ACL/permissions |
| `-Path` | Specify the target path |
| `HKLM:\System\CurrentControlSet\Services\wuauserv` | The Windows Update service's Registry key |
| `\|` | Forward output |
| `Format-List` | Display in readable list format |

### Reading the Output

```text
Owner  : NT AUTHORITY\SYSTEM
Group  : NT AUTHORITY\SYSTEM
```

The Registry object's owner and group are both `SYSTEM`.

```text
Access : BUILTIN\Users Allow  ReadKey
         BUILTIN\Administrators Allow  FullControl
         NT AUTHORITY\SYSTEM Allow  FullControl
```

| Principal | Access |
|---|---|
| `BUILTIN\Users` | Read access |
| `BUILTIN\Administrators` | Full Control |
| `NT AUTHORITY\SYSTEM` | Full Control |

### The Access Field — Most Important Part

Example: `BUILTIN\Administrators Allow FullControl`

| Segment | Meaning |
|---|---|
| `BUILTIN\Administrators` | Security principal |
| `Allow` | Permission allowed |
| `FullControl` | Complete control |

If a question asks "Who has FullControl?" — this is the field to check.

### SDDL Is Also Present in Get-Acl Output

```text
Sddl : O:SYG:SYD:AI(A;ID;KR;;;BU)...
```

```text
sc sdshow  -> SDDL only
Get-Acl    -> Readable ACL + SDDL + SID information
```

`Get-Acl` output can include security principal SIDs embedded within the SDDL — information not directly surfaced by `sc`'s output.

### SID — Security Identifier

**SID** uniquely identifies a Windows security principal.

Example: `S-1-15-3-1024-...`

```text
User / Group -> Security Principal -> SID -> Unique identifier
```

### sc sdshow vs Get-Acl — Summary

| Tool | Main Use |
|---|---|
| `services.msc` | GUI service management |
| `sc qc` | Service configuration |
| `sc query` | Service status |
| `sc sdshow` | Service security descriptor (SDDL) |
| `Get-Acl` | ACL/permissions information (readable + SDDL + SID) |

### Why Command-Line Knowledge Matters

```text
GUI:          services.msc -> Manually inspect -> One/few machines
Command line: sc / PowerShell -> Script -> Many services -> Many machines
```

This scripting/scaling capability becomes much more valuable in large network/domain environments.

### Complete Service Permissions Mental Model

```text
Windows Service
      |
      |-- Service Name
      |-- Executable Path
      |-- Service Account
      |-- Configuration
      |-- Recovery
      `-- Permissions
             |
             |-- sc sdshow -> SDDL
             `-- Get-Acl   -> ACL + SID + SDDL
```

**Security analysis chain:**

```text
Service -> Who can control it? -> What executable does it run? -> Who does it run as?
-> Can a low-privileged user modify something? -> Potential security risk
```

### Question-Solving Clues

| Question | Answer |
|---|---|
| Which command queries a service configuration? | `sc qc` |
| Which command displays the service security descriptor? | `sc sdshow` |
| Which PowerShell cmdlet can examine permissions? | `Get-Acl` |
| What language is the security descriptor written in? | SDDL |
| What identifies a security principal uniquely? | SID |
| Where is `wuauserv` stored in the Registry? | `HKLM:\System\CurrentControlSet\Services\wuauserv` |

**This completes the foundation chain:** `services -> processes -> service accounts -> permissions -> sc.exe -> SDDL -> ACL -> SID`.

---

## Part 4 — Windows Sessions: Interactive vs. Non-Interactive

### Interactive Sessions

An interactive (local) logon session is initiated when a user authenticates to a local or domain system by entering credentials.

```text
User -> Username + Password -> Windows -> Interactive Session
```

**Three common ways an interactive logon is initiated:**

1. **Direct login** — entering username/password directly on the machine.
2. **runas** — starting a process/session in another account's context from the command line:
   ```cmd
   runas /user:username cmd
   ```
   ```text
   Current User -> runas -> Other User -> New logon context
   ```
3. **Remote Desktop (RDP)** — logging in via RDP is also an interactive logon. This is the same category as the `htb-student` RDP session used earlier in this learning series:
   ```text
   Linux -> RDP -> Windows -> htb-student -> Interactive session
   ```

### Non-Interactive Accounts

Non-interactive accounts differ from standard user accounts in that they **do not require login credentials**. Windows uses them automatically to start services and applications without user interaction. There are three types:

- Local System Account
- Local Service Account
- Network Service Account

These accounts have no password associated with them and are typically used to start services at boot or to run scheduled tasks.

#### Local System Account

**Name:** `NT AUTHORITY\SYSTEM` (often shortened to `SYSTEM`)

This is the **most powerful account** in Windows. It handles OS-related tasks, including starting Windows services, and is **more powerful than accounts in the local Administrators group**.

```text
NT AUTHORITY\SYSTEM -> Very high privileges -> OS-level operations
```

#### Local Service Account

**Name:** `NT AUTHORITY\LocalService`

A less-privileged version of SYSTEM, with privileges similar to a local standard user account. Granted limited functionality and can start some services.

```text
SYSTEM       -> High privilege
LocalService -> Lower privilege
```

#### Network Service Account

**Name:** `NT AUTHORITY\NetworkService`

Similar to a standard domain user account. On the local machine it has privileges similar to `LocalService`, but it can also establish authenticated sessions for certain network services.

```text
NetworkService -> Local machine: limited privileges
NetworkService -> Network: can authenticate for certain services
```

### Comparing the Three Non-Interactive Accounts

| Account | Basic Idea |
|---|---|
| `LocalSystem` | Highest privilege |
| `LocalService` | Lower privilege, similar to a local standard user |
| `NetworkService` | Similar local privileges to LocalService, plus network authentication capability |

Exact account names to remember: `NT AUTHORITY\SYSTEM`, `NT AUTHORITY\LocalService`, `NT AUTHORITY\NetworkService`.

### The Core Distinction Not to Confuse

```text
Interactive:      Human User -> Credentials -> Login -> Interactive Session   (e.g., htb-student)
Non-interactive:  Windows -> Automatically starts -> Service/Application -> Special service account   (e.g., NT AUTHORITY\SYSTEM)
```

### Cybersecurity Perspective

This concept connects directly to the service permissions material covered in Parts 2–3 above:

```text
Service -> Runs as -> NT AUTHORITY\SYSTEM
```

If a low-privileged user can improperly modify that service's configuration:

```text
Low Privileged User -> Modifies Service Configuration -> SYSTEM-level Service -> Potential Privilege Escalation
```

This is exactly why service account, service permissions, and executable path were treated as important enumeration targets earlier in this section.

### Question-Solving Clues

| Question | Answer |
|---|---|
| Which account is the most powerful? | Local System Account |
| What is the name of the Local System account? | `NT AUTHORITY\SYSTEM` |
| Which account is a less privileged version of SYSTEM? | Local Service Account |
| Which account can establish authenticated network sessions? | Network Service Account |
| What type of logon occurs through RDP? | Interactive |
| Which type of account is used to automatically start services? | Non-interactive account |

### One-Line Summary

```text
Interactive     -> A human logs in
Non-interactive -> Windows/a service/a task runs automatically

SYSTEM > LocalService
NetworkService ~ LocalService locally, + network authentication capability
```

---

## Combined Quick Reference

```cmd
services.msc                                                        :: GUI service manager
sc                                                                   :: command-line SCM utility
sc qc SERVICE                                                        :: query service configuration
sc query SERVICE                                                     :: query service status
sc stop SERVICE                                                      :: stop a service (requires sufficient privilege)
sc config SERVICE binPath= "C:\path\to\exe"                          :: modify service configuration
sc sdshow SERVICE                                                    :: show service security descriptor (SDDL)
sc \\host_or_ip query SERVICE                                        :: query a service on a remote machine
```

```powershell
Get-Service                                                          :: list all services
Get-Service | ? {$_.Status -eq "Running"} | select -First 2 | fl     :: first 2 running services, full detail
Get-Acl -Path HKLM:\System\CurrentControlSet\Services\wuauserv | Format-List   :: readable ACL + SID + SDDL for a service's Registry key
```

**Service states:** Running · Stopped · Paused · Starting · Stopping
**Startup types:** Automatic · Automatic (Delayed) · Manual
**Service categories:** Local Services · Network Services · System Services

**Permission tools:** `sc sdshow` = SDDL only · `Get-Acl` = readable ACL + SID + SDDL

**Non-interactive accounts:** `NT AUTHORITY\SYSTEM` (highest) · `NT AUTHORITY\LocalService` (lower, standard-user-like) · `NT AUTHORITY\NetworkService` (LocalService-like locally + network auth)

**Interactive logon methods:** Direct login · `runas` · RDP

---

## What This Section Taught

Across these four parts, one thread ties everything together: a Windows service's **security posture** is fully defined by three linked facts — what executable it runs (`BINARY_PATH_NAME`), what account it runs as (`SERVICE_START_NAME`, often a non-interactive account like `SYSTEM`), and who is allowed to change any of that (its DACL, viewable via `sc sdshow` as SDDL or via `Get-Acl` in readable form with SIDs). The Interactive vs. Non-Interactive session distinction closes the loop: it explains *why* a service running as `NT AUTHORITY\SYSTEM` is such a high-value target — that account has no human sitting behind it to notice something is wrong, no login prompt to bypass, and privileges exceeding even the local Administrators group. A low-privileged user gaining write access to that service's configuration is a direct path from a standard interactive session to SYSTEM-level control.
