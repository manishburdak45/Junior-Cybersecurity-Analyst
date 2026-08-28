<div align="center">

# Operating System Structure

### Section 2 of the "Introduction to Windows" Learning Series

`Theory: Filesystem Layout` -> `Practical Enumeration` -> `Methodology Mistake` -> `Correction` -> `Flag Recovered`

</div>

---

## Overview

This document covers Section 2 of the Windows fundamentals learning module: the structure of the Windows filesystem, and a practical enumeration exercise on an HTB target that required finding a "non-standard directory" and submitting the contents of a flag file inside it.

Unlike Section 1, this session included a genuine methodology mistake — chasing a suspicious-looking directory name instead of systematically enumerating the full filesystem first — which is documented here in detail because the lesson is more valuable than the final command itself.

> Note: This is a learning-module walkthrough, not a full machine compromise. No exploitation or privilege escalation occurred in this session. The objective was filesystem enumeration and locating a flag file.

---

## Objective

1. Understand the standard Windows directory layout well enough to recognize what is normal and what is not.
2. Use `dir` and `tree` to enumerate a Windows target's filesystem from the command line.
3. Identify a "non-standard" directory on the C: drive and recover the flag stored inside it.

---

## Foundation & Theory — Windows Filesystem Structure

### The Root Directory

On Windows, the filesystem begins at a drive letter — most commonly:

```text
C:\
```

This is referred to as the **root directory**, and on most systems it is also the **boot partition**, meaning this is the drive Windows itself is installed on. Additional physical or virtual drives are assigned other letters (for example, a secondary data drive might appear as `E:\`).

A simplified view of the top-level layout:

```text
C:\
├── Program Files
├── Program Files (x86)
├── ProgramData
├── Users
├── Windows
└── ...
```

### Program Files vs Program Files (x86)

On a 64-bit Windows system, these two directories are separated by CPU architecture compatibility:

```text
C:\Program Files          -> 64-bit applications
C:\Program Files (x86)    -> 32-bit (and legacy 16-bit) applications
```

> Real-World Understanding: the memory trick used during this session was "x86 = 32-bit" — since `x86` historically refers to the 32-bit instruction set architecture lineage (386, 486, 586...), while 64-bit systems use `x64`/`amd64`. This made it easy to remember which folder holds which type of application without confusing the two.

### ProgramData

```text
C:\ProgramData
```

This is a **hidden folder** by default. It holds data that installed programs need in order to function correctly — and critically, this data is not tied to a single user. Any user running that program can access the shared configuration/data stored here.

> Technical Note: during enumeration, hidden directories should never be skipped. Application configuration and data stored here can be relevant to a security investigation, since it is accessible regardless of which user account is currently logged in.

### Users

```text
C:\Users
```

Contains one profile folder per user who has logged onto the system, for example:

```text
C:\Users\
├── Administrator
├── Public
├── Default
└── htb-student
```

The `htb-student` account used to access the HTB target machine in this session is a direct real-world example of this structure — its profile lives at `C:\Users\htb-student`.

#### Default

```text
C:\Users\Default
```

This is a **template profile**, not an actual personal user account. When a new user is created on the system, their initial profile is generated based on the contents of `Default`.

```text
Default Profile
      |
      v
New User Created
      |
      v
Initial User Profile
```

#### Public

```text
C:\Users\Public
```

Intended for sharing files between users on the same machine. It is accessible to all local users by default, and while it is also shared over the network by default, a valid network account is still required to access it remotely.

> Technical Note: shared/public locations are worth attention during enumeration precisely because multiple users or processes can read from and write to them.

### AppData — Per-User Application Data

```text
C:\Users\htb-student\AppData
```

This is a hidden per-user folder containing application-specific data and settings. It has three important subfolders:

```text
AppData
├── Roaming
├── Local
└── LocalLow
```

| Subfolder | Behavior |
|---|---|
| **Roaming** | Machine-independent data that follows the user profile (e.g., custom dictionaries) — intended to move with the user across machines in networked/domain environments. |
| **Local** | Specific to this one computer; never synchronized across the network. |
| **LocalLow** | Similar to Local, but operates at a **lower data integrity level** — used, for example, by browsers running in a protected/safe mode. |

Memory trick used in this session:

```text
Roaming  -> moves with the user
Local    -> tied to this specific computer
LocalLow -> lower integrity level
```

### Windows, System, System32, SysWOW64

```text
C:\Windows
```

Contains the majority of files required for the Windows operating system itself.

```text
C:\Windows\System
C:\Windows\System32
C:\Windows\SysWOW64
```

These directories hold the DLLs required for Windows' core features and the Windows API. When a program requests to load a DLL without specifying an absolute path, the operating system searches these directories as part of its resolution process.

> Note: the exact architecture-based distinction between `System32` and `SysWOW64` (which one serves 32-bit vs 64-bit binaries on a 64-bit OS) was flagged during this session as something to revisit in more depth later — for this section, the priority was simply recognizing these as core Windows component directories.

### WinSxS — The Windows Component Store

```text
C:\Windows\WinSxS
```

Known as the **Windows Component Store**. It holds copies of Windows components, updates, and service packs.

```text
WinSxS
  |
  v
Components / Updates / Service Packs
```

### Full Structure, Combined

```text
C:\
├── Program Files              (64-bit programs)
├── Program Files (x86)        (32-bit programs)
├── ProgramData                (program-wide data, hidden)
├── Users
│   ├── Default                (template profile)
│   ├── Public                 (shared folder)
│   └── <user>
│       └── AppData
│           ├── Roaming
│           ├── Local
│           └── LocalLow
└── Windows
    ├── System
    ├── System32
    ├── SysWOW64
    └── WinSxS
```

### Why This Matters for Enumeration

On a compromised or target Windows machine, this map becomes a mental checklist:

| Looking for... | Check here |
|---|---|
| Installed applications | `Program Files` |
| 32-bit applications | `Program Files (x86)` |
| User accounts | `C:\Users` |
| User-specific application data | `AppData` |
| Windows system files | `C:\Windows` |
| Windows components/updates | `WinSxS` |

Filesystem knowledge is enumeration skill — knowing what is *supposed* to be there is what makes an anomaly recognizable.

---

## Practical Enumeration — Commands

### Listing a Directory: `dir`

```cmd
dir c:\ /a
```

**Purpose:** Lists the contents of a directory, including files and subdirectories.

**Flag used:** `/a` includes hidden and system files/directories in the listing, which would otherwise be excluded from a plain `dir` output.

The following is the illustrative example output referenced in the module material for this command (not this session's own target output, but the reference example used to explain the command):

```text
C:\htb> dir c:\ /a
 Volume in drive C has no label.
 Volume Serial Number is F416-77BE

 Directory of c:\

08/16/2020  10:33 AM    <DIR>          $Recycle.Bin
06/25/2020  06:25 PM    <DIR>          $WinREAgent
07/02/2020  12:55 PM             1,024 AMTAG.BIN
06/25/2020  03:38 PM    <JUNCTION>     Documents and Settings [C:\Users]
08/13/2020  06:03 PM             8,192 DumpStack.log
08/17/2020  12:11 PM             8,192 DumpStack.log.tmp
08/27/2020  10:42 AM    37,752,373,248 hiberfil.sys
08/17/2020  12:11 PM    13,421,772,800 pagefile.sys
12/07/2019  05:14 AM    <DIR>          PerfLogs
08/24/2020  10:38 AM    <DIR>          Program Files
07/09/2020  06:08 PM    <DIR>          Program Files (x86)
08/24/2020  10:41 AM    <DIR>          ProgramData
06/25/2020  03:38 PM    <DIR>          Recovery
06/25/2020  03:57 PM             2,918 RHDSetup.log
08/17/2020  12:11 PM        16,777,216 swapfile.sys
08/26/2020  02:51 PM    <DIR>          System Volume Information
08/16/2020  10:33 AM    <DIR>          Users
08/17/2020  11:38 PM    <DIR>          Windows
               7 File(s) 51,190,943,590 bytes
              13 Dir(s)  261,310,697,472 bytes free
```

**Interpretation:** This shows a standard C: drive layout — the directories match the theory above (`Program Files`, `Program Files (x86)`, `ProgramData`, `Users`, `Windows`), plus normal system artifacts like `pagefile.sys` and `hiberfil.sys`. Recognizing this as a "normal" listing is exactly what makes an abnormal listing (an unexpected extra directory) stand out later.

### Visualizing Structure: `tree`

```cmd
tree "c:\Program Files (x86)\VMware"
```

**Purpose:** Displays the directory hierarchy of a given path graphically (as a tree), rather than a flat listing.

Reference example output for this command:

```text
Folder PATH listing
Volume serial number is F416-77BE
C:\PROGRAM FILES (X86)\VMWARE
├───VMware VIX
│   ├───doc
│   │   ├───errors
│   │   ├───features
│   │   ├───lang
│   │   │   └───c
│   │   │       └───functions
│   │   └───types
│   ├───samples
│   └───Workstation-15.0.0
│       ├───32bit
│       └───64bit
└───VMware Workstation
    ├───env
    ├───hostd
    │   ├───coreLocale
    │   │   └───en
    │   ├───docroot
    │   │   ├───client
    │   │   └───sdk
    │   ├───extensions
    │   │   └───hostdiag
    │   │       └───locale
    │   │           └───en
    │   └───vimLocale
    │       └───en
    ├───ico
    ├───messages
    │   ├───ja
    │   └───zh_CN
    ├───OVFTool
    │   ├───env
    │   │   └───en
    │   └───schemas
    │       ├───DMTF
    │       └───vmware
    ├───Resources
    ├───tools-upgraders
    └───x64
```

**Interpretation:** `dir` gives a flat listing of one directory level at a time; `tree` gives the full nested hierarchy at a glance — useful for quickly understanding how deep and how complex a directory structure is.

### Full-Drive Tree With File Names: `tree /f`

```cmd
tree c:\ /f | more
```

Breaking this command down:

| Part | Meaning |
|---|---|
| `tree c:\` | Generate the tree structure of the entire C: drive |
| `/f` | Include files in the output, not just directories |
| `\|` | Pipe the output into the next command |
| `more` | Display the output one page/screen at a time |

**Why this matters:** the C: drive can contain a very large number of files. Without `more`, the entire output would scroll past in one uncontrollable burst, making it effectively unreadable. Paging the output makes systematic review possible.

---

## The Actual Task

The module's exercise instruction was:

> "Find the non-standard directory in the C drive. Submit the contents of the flag file saved in this directory."

---

## Mistake, Correction, and Discovery

### What Was Tried

While reviewing the target's C: drive, attention was drawn to a directory with a long, random-looking hexadecimal-style name (beginning with `75afac25577675a9bfafd2405602...`). This directory was immediately treated as "the non-standard directory" simply because its name looked unusual and out of place compared to normal Windows folder names.

### What Was Wrong

The investigation jumped directly to the most visually suspicious-looking name **without first completing a full, systematic enumeration of the entire C: drive**. The actual task required identifying *the* non-standard directory relative to the full, known-standard layout — not just the most attention-grabbing name encountered first.

### How It Was Realized

Running the complete drive enumeration command:

```cmd
tree C:\ /f | more
```

surfaced the real answer directly in the output:

```text
├───Academy
│       flag.txt
```

This revealed a directory named `Academy` — a name that does **not** appear anywhere in the standard Windows directory layout documented in the Foundation & Theory section above (`Program Files`, `ProgramData`, `Users`, `Windows`, etc.). This, not the random-looking hex-named folder, was the actual non-standard directory the exercise was asking for.

> Correction (Self-Acknowledged): the earlier approach treated the random-looking hexadecimal directory name as inherently suspicious and worth prioritizing. The subsequent full-tree output showed that the truly non-standard directory — judged against the actual standard layout — was `Academy`, a plain English word with no obvious "suspicious" appearance at all. The assumption that "unusual-looking name = the answer" was incorrect; the correct method was comparing every directory against the known standard set.

### Correct Solution

```cmd
type C:\Academy\flag.txt
```

**Purpose:** `type` outputs the contents of a text file directly to the console — the standard way to read a flag file's contents from the Windows command line.

**Result:** The flag content displayed in the console was the answer submitted for the exercise.

> Note: The literal flag string itself was not shown in the source material for this write-up and is therefore not reproduced here.

### An Alternative Approach Also Discussed

Before the full-tree method succeeded, a targeted recursive search was also proposed as an option:

```cmd
dir C:\ /s /b *flag*
```

This searches the entire C: drive recursively (`/s`) for any file or folder matching `*flag*`, displaying only bare paths (`/b`). This was noted as a valid alternative, with the caveat that a full recursive search of the entire C: drive can take significant time depending on disk size and file count.

---

## Lesson Learned

The core lesson from this session was methodological, not technical:

```text
WRONG APPROACH (what happened first):
  See an unusual-looking name -> assume it is "the" suspicious item -> investigate it directly

CORRECT APPROACH:
  Read the task carefully
        |
        v
  Identify the actual scope (the entire C: drive)
        |
        v
  Enumerate the complete structure systematically
        |
        v
  Compare every entry against the known-standard layout
        |
        v
  Identify what is genuinely non-standard
        |
        v
  Investigate that specific item
        |
        v
  Recover the flag
```

**Why this matters beyond this one exercise:** in real enumeration and penetration testing work, "most suspicious-looking" is not the same as "correct." A visually unusual name (like a long hex string) can be a legitimate artifact of installed software, a temporary system folder, or unrelated noise — while the actual anomaly can be something as unremarkable-looking as a plain English folder name that simply does not belong in the standard layout. Systematic, complete enumeration before drawing conclusions is what prevents tunnel vision from producing a wrong answer, even when it happens to look convincing.

---

## Technical Concepts Recap

- The Windows filesystem has a **predictable standard layout** (`Program Files`, `ProgramData`, `Users`, `Windows`, etc.) — knowing this layout by heart is what makes deviations detectable.
- `dir /a` reveals hidden/system entries that a plain `dir` would miss.
- `tree` visualizes hierarchy; `tree /f` adds file-level detail; piping either through `more` makes large output reviewable.
- `type <file>` is the direct way to read a text file's contents from the Windows command line, including flag files.
- `dir /s /b *pattern*` performs a recursive, path-only search across an entire directory tree — useful for targeted searches once a broad structural review has already been done.

---

## Key Takeaways

- Knowing the **standard** Windows directory structure is what makes a **non-standard** one recognizable — this is a prerequisite skill, not an optional detail.
- `AppData`'s three subfolders (Roaming/Local/LocalLow) each have distinct synchronization and integrity behavior that matters for both normal system administration and forensic/enumeration work.
- Full, systematic enumeration (`tree C:\ /f`) should generally precede targeted searching (`dir /s /b`), especially when the target of the search is not yet clearly defined.

## Biggest Mistake

Prioritizing a directory based on how "suspicious" its name looked, instead of completing a full comparison against the known-standard Windows layout first. This led to initial tunnel vision on the wrong directory.

## Most Important Discovery

Running the complete, unfiltered `tree C:\ /f | more` enumeration — rather than a narrower, assumption-driven search — is what actually surfaced the `Academy\flag.txt` path.

## Quick Reference

```cmd
dir C:\ /a                          :: list C:\, including hidden/system entries
tree "C:\path\to\folder"            :: visualize a specific directory's hierarchy
tree C:\ /f | more                  :: full-drive tree, including files, paged
dir C:\ /s /b *flag*                :: recursive search for files/folders matching a pattern
type C:\path\to\file.txt            :: print a text file's contents (e.g., a flag file)
```

## Final Attack Chain (Enumeration Chain)

```text
[Task: Find non-standard directory + flag]
              |
              v
[Reviewed standard directory layout (theory)]
              |
              v
[Mistakenly focused on random-looking hex-named directory]
              |
              v
[Ran full enumeration: tree C:\ /f | more]
              |
              v
[Identified "Academy" as the true non-standard directory]
              |
              v
[Located Academy\flag.txt]
              |
              v
[Read flag contents: type C:\Academy\flag.txt]
              |
              v
[Flag submitted]
```

## What This Exercise Taught

Filesystem enumeration is only as good as the enumerator's mental model of what "normal" looks like. This session's mistake was not a technical error — every command used was correct — it was a **methodology** error: drawing a conclusion before completing the systematic step the task actually required. The fix was not a new command, but a change in process: complete the full picture first, then compare, then investigate.







<div align="center">

#  File Systems & NTFS Permissions

### Section 3 of the "Introduction to Windows" Learning Series

`Theory: File Systems` -> `NTFS Permissions` -> `icacls Enumeration` -> `Three Wrong Answers` -> `Correct Answer`

</div>

---

## Overview

This section covers Windows file systems (FAT32, exFAT, NTFS), the NTFS permission model, and the `icacls` command-line utility used to enumerate and manage those permissions. The practical exercise required identifying which user has Full Control over the `C:\Users` directory on a target machine — and this session is a strong example of why reading a question's exact wording matters, since the correct answer was only reached after **two incorrect submissions**.

> Note: This is a learning-module walkthrough. No exploitation or privilege escalation occurred. The task was NTFS permission enumeration and correctly interpreting the exercise question.

---

## Objective

1. Understand the Windows file systems in use today (FAT32, exFAT, NTFS) and why NTFS is the default.
2. Understand the NTFS permission model and its inheritance behavior.
3. Use `icacls` to enumerate permissions on a target directory.
4. Correctly identify which user has Full Control over `C:\Users`, and submit the answer in the exact format the exercise expects.

---

## Foundation & Theory

### Windows File Systems

There are five Windows file systems historically: **FAT12, FAT16, FAT32, NTFS, and exFAT**. FAT12 and FAT16 are obsolete on modern Windows. This module's focus is primarily **NTFS**, with FAT32 and exFAT covered for context.

#### FAT32

FAT32 (File Allocation Table, 32-bit) is widely used on portable storage — USB drives, SD cards — and can also format hard drives. The "32" refers to the number of bits used to identify data clusters on the storage device.

| Pros | Cons |
|---|---|
| Broad device compatibility (cameras, consoles, phones, tablets, computers) | Maximum file size of 4GB |
| Cross-OS compatibility (Windows 95+, macOS, Linux) | No built-in data protection or compression |
| — | Requires third-party tools for file encryption |

#### NTFS

NTFS (New Technology File System) has been the default Windows file system since **Windows NT 3.1**. It resolves FAT32's shortcomings and adds richer metadata support and better performance through improved data structuring.

| Pros | Cons |
|---|---|
| Reliable — can restore filesystem consistency after a crash or power loss | Most mobile devices don't natively support NTFS |
| Granular file/folder permissions (security) | Older media devices (TVs, digital cameras) often lack NTFS support |
| Supports very large partitions | — |
| Built-in journaling — logs file additions, modifications, and deletions | — |

> Technical Note: NTFS's journaling and granular permission system are exactly why it is the file system security assessments care about — both leave an audit trail and a permission structure that can be enumerated and analyzed, unlike FAT32.

### NTFS Permissions

| Permission | Description |
|---|---|
| **Full Control** | Read, write, change, and delete files/folders. |
| **Modify** | Read, write, and delete files/folders. |
| **List Folder Contents** | View/list folders and subfolders, execute files. Inherited by folders only. |
| **Read and Execute** | View/list files and subfolders, execute files. Inherited by both files and folders. |
| **Write** | Add files to folders/subfolders, write to a file. |
| **Read** | View/list folders and subfolders, view a file's contents. |
| **Traverse Folder** | Allows/denies passing *through* a folder to reach files/folders deeper inside it, even without permission to list that intermediate folder's own contents (e.g., reaching `backup_02042020.zip` inside `c:\users\bsmith\documents\webapps\backups\backup\` without being able to list the `webapps` or `backups` folder contents themselves). |

**Inheritance:** files and folders inherit permissions from their parent folder by default, so administrators don't need to manually configure every single file and folder — a massive administrative time-saver. When needed, an administrator can disable inheritance on specific files/folders and set permissions explicitly.

### icacls — Command-Line NTFS Permission Management

While NTFS permissions can be managed through File Explorer's Security tab, `icacls` provides the same control — and finer granularity — from the command line.

**Listing permissions on a directory:**

```cmd
icacls c:\windows
```

Reference example output (from module material):

```text
c:\windows NT SERVICE\TrustedInstaller:(F)
           NT SERVICE\TrustedInstaller:(CI)(IO)(F)
           NT AUTHORITY\SYSTEM:(M)
           NT AUTHORITY\SYSTEM:(OI)(CI)(IO)(F)
           BUILTIN\Administrators:(M)
           BUILTIN\Administrators:(OI)(CI)(IO)(F)
           BUILTIN\Users:(RX)
           BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
           CREATOR OWNER:(OI)(CI)(IO)(F)
           APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES:(RX)
           APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES:(OI)(CI)(IO)(GR,GE)
           APPLICATION PACKAGE AUTHORITY\ALL RESTRICTED APPLICATION PACKAGES:(RX)
           APPLICATION PACKAGE AUTHORITY\ALL RESTRICTED APPLICATION PACKAGES:(OI)(CI)(IO)(GR,GE)

Successfully processed 1 files; Failed processing 0 files
```

**Inheritance flags shown in the output:**

| Flag | Meaning |
|---|---|
| `(CI)` | Container Inherit |
| `(OI)` | Object Inherit |
| `(IO)` | Inherit Only |
| `(NP)` | Do Not Propagate Inherit |
| `(I)` | Permission Inherited From Parent Container |

**Basic access-level codes:**

| Code | Meaning |
|---|---|
| `F` | Full access |
| `D` | Delete access |
| `N` | No access |
| `M` | Modify access |
| `RX` | Read and execute access |
| `R` | Read-only access |
| `W` | Write-only access |

In the example above, `NT AUTHORITY\SYSTEM` has `(OI)(CI)(IO)(F)` — object inherit, container inherit, inherit-only, and full access — meaning this account has full control propagating across the entire directory and everything beneath it.

**Granting and removing permissions:**

```cmd
icacls c:\users /grant joe:f
```

Grants the user `joe` Full Control (`F`) over `c:\users`. Because `(OI)` and `(CI)` were not specified in this command, `joe` only has rights on the `c:\users` folder itself — not on the user subdirectories/files inside it.

```cmd
icacls c:\users /remove joe
```

Revokes the previously granted permission.

> Technical Note: `icacls` is also usable in a domain context to grant/deny permissions to specific users or groups, toggle inheritance, and change file/folder ownership — making it a core tool for both administration and, during an assessment, understanding who effectively controls sensitive directories.

---

## The Actual Task

The exercise question was:

> "What system user has full control over the `c:\users` directory?"

---

## Practical Enumeration

```cmd
icacls c:\Users
```

**Purpose:** List the NTFS permissions currently applied to the `C:\Users` directory on the actual HTB target, to identify who holds Full Control.

**Relevant lines observed in the target's actual output during this session:**

```text
NT AUTHORITY\SYSTEM:(OI)(CI)(F)
BUILTIN\Administrators:(OI)(CI)(F)
WS01\manish:(OI)(CI)(F)
BUILTIN\Users:(RX)
```

**Interpretation:** Multiple entries carry `(F)` — Full Control — on this directory: `NT AUTHORITY\SYSTEM`, `BUILTIN\Administrators`, and `WS01\manish`. This is precisely what made the question ambiguous enough to get wrong twice: several identities technically satisfy "has full control," but the question was asking for one specific type of answer.

---

## Mistakes, Corrections, and Final Answer

This question required three attempts before the correct answer format was identified. Each attempt is preserved below because the reasoning behind each wrong answer — and why it was wrong — is the actual lesson.

### Attempt 1

**What Was Tried:**
`NT AUTHORITY\SYSTEM` was submitted as the answer, reasoning that `SYSTEM` is the highest-privilege built-in account and its permission entry showed `(OI)(CI)(F)` — full control, inherited across the directory.

**What Was Wrong:**
`NT AUTHORITY\SYSTEM` is a built-in Windows security principal, not what the question was actually asking for. The exercise wanted a specific system **user**, not the generic system account that has elevated access on essentially every Windows machine by default.

**How It Was Realized:**
The submitted answer was rejected as incorrect, prompting a re-read of the full `icacls` output line by line rather than stopping at the first `(F)` entry found.

### Attempt 2

**What Was Tried:**
Noticing the additional line `WS01\manish:(OI)(CI)(F)` in the same output, the answer `WS01\manish` was submitted next — reasoning that this was an actual named user account (unlike `SYSTEM`, which is a system principal, or `Administrators`, which is a group) and it also carried Full Control.

**What Was Wrong:**
The user identity was correct (`manish`), but the **format** was wrong. Including the `WS01\` machine-name prefix did not match what the exercise's answer validation expected.

**How It Was Realized:**
This answer was also rejected. At this point, the question's exact wording was re-examined character by character: *"What system user has full control over the `c:\users` directory?"* — the emphasis shifted from *which identity* (already correctly identified as `manish`) to *what exact string format* the platform wanted submitted.

> Correction (Self-Acknowledged): the second interpretation correctly identified the right user but carried over unnecessary formatting (the domain/machine prefix) from the raw `icacls` output instead of submitting just the username portion.

### Correct Solution

```text
manish
```

Submitted without the `WS01\` prefix, this was accepted as correct. Multiple independently referenced walkthroughs for this same HTB exercise also confirm `manish` as the expected answer, without the machine/domain prefix.

### Lesson Learned

```text
ATTEMPT 1: NT AUTHORITY\SYSTEM        -> wrong identity (system principal, not the intended "system user")
ATTEMPT 2: WS01\manish             -> right identity, wrong format (unnecessary domain prefix)
ATTEMPT 3: manish                  -> correct identity, correct format
```

**Why this matters beyond this one exercise:** `icacls` output will often show several entries with the same permission level (`(F)`), including built-in system principals (`SYSTEM`), groups (`Administrators`), and actual named user accounts (`manish`). A question asking for "what system user" is implicitly asking for an **actual named account**, not a built-in system identity or a group — and even after identifying the right account, the exact submission format (with or without a domain/machine prefix) still needs to be verified against what the platform expects, since raw command output formatting and expected answer formatting are not always the same thing.

---

## Technical Concepts Recap

- NTFS is the modern Windows default file system, chosen over FAT32/exFAT specifically for its permission granularity, journaling, and reliability — all of which matter for a security assessment.
- `icacls <path>` lists every security principal with permissions on that path, alongside inheritance flags (`CI`, `OI`, `IO`, `NP`, `I`) and access-level codes (`F`, `M`, `RX`, `R`, `W`, `N`, `D`).
- A single directory can legitimately have multiple entries with the same permission level — distinguishing between a **system principal** (`NT AUTHORITY\SYSTEM`), a **group** (`BUILTIN\Administrators`), and an **actual user account** (`manish`) is essential to answering questions correctly.
- `icacls /grant` and `icacls /remove` allow direct command-line permission changes, useful both for legitimate administration and for understanding how privilege changes on a filesystem can be made or audited.

---

## Key Takeaways

- NTFS permission enumeration frequently surfaces multiple valid-looking "Full Control" entries — correctly answering a question requires distinguishing system principals, groups, and named user accounts from one another.
- Exact question wording (e.g., "system user") can carry a specific technical meaning that differs from the most obvious or highest-privilege entry in the output.
- Answer **format** matters independently of answer **correctness** — identifying the right entity is only half the task; matching the expected submission format is the other half.

## Biggest Mistakes

1. Submitting `NT AUTHORITY\SYSTEM` — correct permission level, wrong category of identity for what the question asked.
2. Submitting `WS01\manish` — correct identity, incorrect format (extraneous domain/machine prefix).

## Most Important Discovery

Re-reading the exact question wording after the first rejection is what shifted the focus from "which entry has Full Control" to "which named user account has Full Control" — and the second rejection is what revealed that formatting, not identity, was the remaining issue.

## Quick Reference

```cmd
icacls c:\windows                 :: list permissions + inheritance flags on a directory
icacls c:\Users                   :: list permissions on the Users directory
icacls c:\users /grant joe:f      :: grant Full Control to user "joe" (folder only, no OI/CI = no propagation)
icacls c:\users /remove joe       :: revoke a previously granted permission
```

**Inheritance flags:** `(CI)` container inherit · `(OI)` object inherit · `(IO)` inherit only · `(NP)` do not propagate · `(I)` inherited from parent

**Access codes:** `F` full · `M` modify · `RX` read/execute · `R` read-only · `W` write-only · `N` no access · `D` delete

## Final Attack Chain (Enumeration Chain)

```text
[Task: Identify system user with Full Control over c:\Users]
              |
              v
[Ran icacls c:\Users on target]
              |
              v
[Multiple (F) entries found: SYSTEM, Administrators, manish]
              |
              v
[Attempt 1: NT AUTHORITY\SYSTEM -- REJECTED (wrong identity type)]
              |
              v
[Re-read question wording]
              |
              v
[Attempt 2: WS01\manish -- REJECTED (wrong format)]
              |
              v
[Attempt 3: manish -- ACCEPTED]
```

## What This Exercise Taught

Correctly enumerating permissions is not the same as correctly answering a question about them. This session needed three attempts, but each rejection carried a distinct, useful signal: the first pointed to a category mismatch (system principal vs. named user), and the second pointed to a formatting mismatch (domain-qualified vs. bare username). Reading the question's exact wording again after every rejection — rather than guessing a variation blindly — was what converged on the correct, precisely-formatted answer.





<div align="center">

#  NTFS vs. Share Permissions (SMB)

### Section 4 of the "Introduction to Windows" Learning Series

`Theory: NTFS vs Share Permissions` -> `SMB Share Creation` -> `smbclient Enumeration` -> `Firewall Troubleshooting` -> `Remote Access Method Confusion` -> `Correction`

</div>

---

## Overview

This section covers the distinction between NTFS permissions and share permissions in Windows — two separate permission systems that often apply to the same shared resource but are frequently confused for being the same thing. It includes creating an SMB share on a Windows 10 target, enumerating and connecting to it with `smbclient` from a Linux attack host, troubleshooting a Windows Defender Firewall block, and mounting the share locally.

This session also contains a genuine conceptual mix-up around **how remote access actually works** — specifically, a misunderstanding about whether running a command locally could make the local Linux terminal "become" the target user, which was corrected by clarifying the actual mechanics of RDP, SSH, and WinRM.

> Note: This is a learning-module walkthrough. No exploitation or privilege escalation occurred. The scope was SMB/NTFS permission theory, share creation and access, and remote access method troubleshooting.

---

## Objective

1. Understand the difference between NTFS permissions and share permissions, and how both apply simultaneously to a shared resource.
2. Create and configure an SMB share on a Windows 10 target.
3. Enumerate and connect to that share from a Linux attack host using `smbclient`.
4. Diagnose and resolve a Windows Defender Firewall block preventing SMB access.
5. Correctly understand the distinction between local command execution and remote shell access, and identify the correct method (RDP) for reaching the target as `htb-student`.

---

## Foundation & Theory

### Why Windows Is a High-Value Malware Target

Microsoft holds over 70% of the global desktop operating system market share. This is a direct business incentive for malware authors: writing malware for the platform with the largest install base maximizes potential impact, which is why Windows is so frequently targeted and often perceived as "less secure" than other operating systems.

> Technical Note: no operating system is immune to malware by design — if software can be written for a platform, malicious software can be written for it too. Windows' reputation is a function of market share and attacker incentive, not an inherent architectural weakness exclusive to it.

A concrete and still-relevant example: the **EternalBlue** vulnerability continues to affect unpatched systems running SMBv1, and remains a common entry point for ransomware.

### SMB — The Protocol Behind File Sharing

The **Server Message Block (SMB)** protocol is what Windows uses to share resources like files and printers across a network, in environments of every size from small business to large enterprise.

```text
[Client] --SMB Request--> [Server]
                              |
                              v
                    [File System / Printer]
                              |
                              v
[Client] <--Directory/File Data-- [Server]
```

### NTFS Permissions vs. Share Permissions — The Core Distinction

These are commonly assumed to be the same thing. **They are not.** Both can apply to the same shared folder simultaneously, but they govern access differently depending on *how* that folder is being accessed.

| Access Path | Which Permissions Apply |
|---|---|
| Accessing the folder over the network via SMB (a network share) | **Both** Share permissions AND NTFS permissions |
| Accessing the folder locally, or via RDP session logged into the machine directly | **Only** NTFS permissions (share permissions are irrelevant here — SMB isn't involved) |

This means NTFS permissions are the more granular, more consistently-enforced control — they apply no matter how the resource is accessed. Share permissions only come into play specifically when SMB is the access method.

#### Share Permissions

| Permission | Description |
|---|---|
| **Full Control** | Everything in Change and Read, plus the ability to change NTFS permissions on files/subfolders. |
| **Change** | Read, edit, delete, and add files and subfolders. |
| **Read** | View file and subfolder contents only. |

#### NTFS Basic Permissions

| Permission | Description |
|---|---|
| **Full Control** | Add, edit, move, delete files/folders; change NTFS permissions on all allowed folders. |
| **Modify** | View and modify files/folders, including adding/deleting. |
| **Read & Execute** | Read file contents and execute programs. |
| **List Folder Contents** | View a listing of files and subfolders. |
| **Read** | Read file contents. |
| **Write** | Write changes to a file, add new files to a folder. |
| **Special Permissions** | Advanced, more granular permission options (below). |

#### NTFS Special Permissions

| Permission | Description |
|---|---|
| **Full Control** | Add, edit, move, delete files/folders; change NTFS permissions on all permitted folders. |
| **Traverse Folder / Execute File** | Access a subfolder deeper in a directory structure even without access to the parent folder's own contents; execute programs. |
| **List Folder / Read Data** | View files/folders within the parent folder; open and view files. |
| **Read Attributes** | View basic attributes (system, archive, read-only, hidden). |
| **Read Extended Attributes** | View program-specific extended attributes. |
| **Create Files / Write Data** | Create files within a folder; modify a file. |
| **Create Folders / Append Data** | Create subfolders; add data to files without overwriting existing content. |
| **Write Attributes** | Change file attributes (does not grant file/folder creation rights). |
| **Write Extended Attributes** | Change program-specific extended attributes. |
| **Delete Subfolders and Files** | Delete subfolders/files, without deleting the parent folder. |
| **Delete** | Delete the parent folder, its subfolders, and files. |
| **Read Permissions** | View the permissions currently set on a folder. |
| **Change Permissions** | Modify the permissions set on a file or folder. |
| **Take Ownership** | Take ownership of a file/folder — the owner automatically gains full permission-changing rights. |

> Real-World Analogy: system administrators effectively hold the keys to what every user and group can or cannot do across an organization's network resources. This is precisely why spear-phishing campaigns are so often aimed at sysadmins and other IT leadership — compromising one such account can grant far more effective control over an environment than compromising a non-technical executive account, even one with a high-ranking title. A hospital's doctors or executives, for instance, will not hold administrative rights over the network — the system administrators will.

**Inheritance:** NTFS permissions are inherited from the parent directory by default. In Windows, `C:\` is effectively the root parent for this inheritance chain unless an administrator explicitly disables inheritance on a specific folder's Advanced Security settings. A gray checkmark next to a permission in the GUI indicates it was inherited rather than explicitly set.

---

## Practical Walkthrough — Creating and Testing an SMB Share

### Creating the Share (GUI)

A new folder ("Company Data") was created on the Windows 10 target's desktop and configured via **Advanced Sharing**. The share name defaulted automatically to the folder's name, and Windows also allows limiting the number of simultaneous connections to the share — a setting real environments typically tune based on actual expected user load.

> Technical Note: in large enterprise environments, shares are normally hosted on a SAN, NAS, or a Windows Server partition — not a desktop OS. Finding a share hosted directly on a desktop machine in the field is a signal worth investigating: it typically indicates either a small business environment or a beachhead system being used to stage/exfiltrate data (by a penetration tester or an actual attacker).

The share's Access Control List (ACL) — its Share Permissions list — was left at its default: the **Everyone** group with **Read** access.

### Enumerating Shares with smbclient

```bash
smbclient -L SERVER_IP -U htb-student
```

**Purpose:** Lists all SMB shares available on the target from the Linux attack host.

**Observed Result:**

```text
Enter WORKGROUP\htb-student's password:

    Sharename       Type      Comment
    ---------       ----      -------
    ADMIN$          Disk      Remote Admin
    C$              Disk      Default share
    Company Data    Disk
    IPC$            IPC       Remote IPC
```

**Interpretation:** The custom `Company Data` share is visible, alongside Windows' built-in administrative shares — `ADMIN$` (remote admin access to `C:\WINDOWS`) and, notably, `C$` (the entire `C:\` drive, shared automatically by Windows at install, without any manual configuration).

> Critical Finding: `C$` being shared by default means the entire C: drive of every Windows system on a network is technically remotely reachable via SMB by anyone with the correct access — this is worth remembering during any Windows-focused assessment, since it is not something an administrator necessarily configured intentionally.

### Connecting to the Share

```bash
smbclient '\\SERVER_IP\Company Data' -U htb-student
```

**Observed Result:**

```text
Password for [WORKGROUP\htb-student]:
Try "help" to get a list of possible commands.

smb: \>
```

### Mistake — Firewall Block

**What Was Tried:** With the share's permissions confirmed correct (`Everyone` group, `Read` access present), a connection attempt was still expected to work without further changes.

**What Was Wrong:** The connection was actually being blocked at the network layer — not by SMB share permissions or NTFS permissions at all.

**How It Was Realized:** The question was explicitly raised — *"What could potentially block us from accessing this share if all our entries are correct?"* — leading to the identification of **Windows Defender Firewall** as the blocking factor, specifically because the Linux-based attack host is not joined to the same Windows workgroup as the target.

**Correct Solution:** Enable the appropriate predefined inbound firewall rule in Windows Defender Firewall's advanced security settings (rather than disabling the firewall outright, which is a common but poor practice in real environments).

**Lesson Learned:** Correct SMB/NTFS permissions do not guarantee network-layer connectivity — firewall rules operate independently of both permission systems and must be checked separately when access unexpectedly fails.

> Technical Note: Windows Defender Firewall enforces separate inbound/outbound rule sets per profile — **Public**, **Private**, and **Domain**. In a Windows Domain environment, these rules can be centrally managed via Group Policy (out of scope for this module).

### Authentication Context — Workgroup vs. Domain

> Concept: when a Windows system is part of a **workgroup**, netlogon requests are authenticated against that system's own local **SAM database**. When a Windows system is joined to a **Windows Domain**, netlogon requests are instead authenticated against the centralized **Active Directory** database. This distinction directly affects how and where the `htb-student` account's credentials are validated when connecting.

### Mounting the Share Locally

```bash
sudo mount -t cifs -o username=htb-student,password=Academy_WinFun! //ipaddoftarget/"Company Data" /home/user/Desktop/
```

**Purpose:** Creates a local mount point on the Linux attack host's desktop, mapping directly to the remote SMB share.

**If this fails:** verify command syntax first; if syntax is correct but it still fails, install the required package:

```bash
sudo apt-get install cifs-utils
```

> ⚠️ Data Accuracy Note: the password `Academy_WinFun!` appears explicitly in the module's own example mount command and is preserved here exactly as it appeared in the source material.

### Monitoring Tools

```cmd
net share
```

**Observed Result:**

```text
Share name   Resource                        Remark

-------------------------------------------------------------------------------
C$           C:\                             Default share
IPC$                                         Remote IPC
ADMIN$       C:\WINDOWS                      Remote Admin
Company Data C:\Users\htb-student\Desktop\Company Data

The command completed successfully.
```

**Interpretation:** Confirms both the manually created `Company Data` share and Windows' default administrative shares (`C$`, `ADMIN$`, `IPC$`), reinforcing that `C:\` is shared automatically without manual setup.

**Additional monitoring tools referenced:**
- **Computer Management** — inspect Shares, Sessions, and Open Files; useful during incident response to understand how an SMB-related breach may have happened and what traces were left behind.
- **Event Viewer** — Windows' logging utility; every action performed against the shared folder (creation, editing, access) generates log entries reviewable here.

---

## Mistake — Confusing Local Command Execution With Remote Access

This is a separate conceptual issue from the firewall problem above, related to how remote access to the target actually works.

### What Was Tried

The goal was to get a shell on the Windows target as the `htb-student` user, such that running `whoami` would return `htb-student`. Without a Pwnbox available, the plan was to use the local Linux machine directly as the attack host — but there was uncertainty about how running a command locally could result in the target's identity being reflected.

### What Was Wrong

There was a misunderstanding that running `whoami` in the local Linux terminal could somehow "become" `htb-student` without first actually establishing a remote connection to the target. Running `whoami` locally will only ever return the **local Linux user**, regardless of any Windows credentials involved — because no connection to the target has been made at that point.

### How It Was Realized

The distinction was clarified directly: OpenVPN's only role is providing **network connectivity** to the HTB lab environment. It does not authenticate as any particular user on any particular machine. Becoming `htb-student` requires actually **authenticating to the target Windows machine itself**, through a remote access protocol — at which point `whoami`, run *inside that remote session*, would correctly reflect `htb-student`.

### Approaches Considered

**RDP (the module's intended method):**

```bash
xfreerdp /v:10.129.152.128 /u:htb-student /p:'YOUR_PASSWORD'
```

This provides a full GUI desktop session as `htb-student` directly.

**Checking for an alternative shell-based method:**

```bash
nmap -Pn 10.129.152.128
```

**Purpose:** Enumerate open ports/services on the target to determine what remote access options actually exist, rather than assuming.

**Ports checked for relevance:**

| Port | Service | Relevance |
|---|---|---|
| 3389 | RDP | GUI remote desktop access |
| 5985 | WinRM (HTTP) | PowerShell remoting |
| 5986 | WinRM (HTTPS) | PowerShell remoting, encrypted |
| 22 | SSH | Terminal-based remote shell (not enabled by default on Windows) |

### A Further Attempted Command

```bash
ssh htb-student@10.129.152.128
```

**What This Command Actually Does:** Attempts an SSH login to the target IP as `htb-student` — it does **not** transform the local Linux shell into the target's identity by itself, and it will only work at all **if the SSH service is actually running and open on the target**.

> Correction: Windows does not enable SSH access by default. Before attempting this command, the correct step is to explicitly verify the port is open:
> ```bash
> nmap -Pn -p 22 10.129.152.128
> ```
> If the result shows `22/tcp open ssh`, the SSH attempt is viable. If port 22 is closed or filtered, SSH cannot be used at all, and the module's intended access method — RDP on port 3389 — is the correct path instead.

### Lesson Learned

```text
whoami (run locally, no connection made)     -> always shows the LOCAL Linux user
ssh htb-student@<target>                     -> only works if port 22 is actually open on the target
xfreerdp /v:<target> /u:htb-student /p:...   -> the module's intended, verified-working method

Correct mental model:
  OpenVPN  = network path to the HTB lab (connectivity only)
  RDP/SSH/WinRM = actual authentication into the target machine
  whoami inside that remote session = reflects the target's identity, not before
```

**Why this matters:** it is easy to conflate "I'm connected to the HTB VPN" with "I'm authenticated as the target user" — they are two entirely separate layers. Network connectivity (via VPN) only makes the target reachable; it does not authenticate anything. Confirming which remote access service is actually open (via `nmap`) before attempting a specific protocol avoids wasted attempts against closed ports.

---

## Technical Concepts Recap

- **NTFS permissions apply everywhere** the resource is accessed (locally, RDP, or via SMB); **share permissions apply only over SMB** — both stack together for network access, but only NTFS matters for local/RDP access.
- Windows automatically shares `C:\` as `C$` at install — this is a default, not a manual misconfiguration, but it is still a real remote access surface.
- Windows Defender Firewall operates independently of file-level permissions — a fully correct permission configuration can still be blocked at the network layer.
- Workgroup authentication uses the local SAM database; Domain authentication uses Active Directory — this determines where credentials are actually validated.
- Network connectivity (VPN) and target authentication (RDP/SSH/WinRM) are separate layers — reaching a target's network does not equate to being logged into it.

---

## Key Takeaways

- The NTFS vs. share permission distinction is not academic — it directly determines which access path (local/RDP vs. SMB) a given permission setting actually controls.
- Default Windows administrative shares (`C$`, `ADMIN$`, `IPC$`) exist without manual configuration and represent a meaningful default attack surface on any Windows host.
- Firewall behavior must be diagnosed separately from permission configuration when access unexpectedly fails.

## Biggest Mistakes

1. Assuming correct share/NTFS permissions alone would guarantee SMB connectivity, without accounting for Windows Defender Firewall as a separate blocking layer.
2. Conflating VPN network connectivity with target authentication — expecting a local `whoami` to somehow reflect a remote target user without first establishing an actual authenticated session.

## Most Important Discoveries

- `smbclient -L` revealing the default `C$`/`ADMIN$`/`IPC$` shares alongside the manually created share — highlighting that `C:\` is shared by default.
- Explicitly running `nmap -Pn` to check for 3389/5985/5986/22 before assuming any specific remote access protocol would work, rather than guessing.

## Quick Reference

```bash
smbclient -L SERVER_IP -U htb-student                          :: list available shares
smbclient '\\SERVER_IP\Share Name' -U htb-student               :: connect to a specific share
sudo apt-get install cifs-utils                                  :: required package for CIFS mounts
sudo mount -t cifs -o username=U,password=P //IP/"Share" /path/  :: mount an SMB share locally
net share                                                         :: (on target) list shares, including default admin shares
nmap -Pn <target>                                                 :: check open ports before choosing a remote access method
nmap -Pn -p 22 <target>                                           :: verify SSH is actually open before attempting ssh
xfreerdp /v:<target> /u:<user> /p:'<password>'                    :: correct, module-intended remote access method
```

## Final Attack Chain (Access & Enumeration Chain)

```text
[Theory: NTFS vs Share permissions understood]
              |
              v
[Share "Company Data" created on Windows 10 target, Everyone:Read]
              |
              v
[smbclient -L from Linux host -- shares enumerated]
              |
              v
[smbclient connection attempt -- BLOCKED]
              |
              v
[Diagnosed as Windows Defender Firewall, not a permission issue]
              |
              v
[Inbound firewall rule enabled -- connection succeeds]
              |
              v
[Share mounted locally via cifs]
              |
              v
[net share / Computer Management / Event Viewer used to review activity]
              |
              v
[Separate question: how to get an htb-student shell without Pwnbox]
              |
              v
[Misunderstanding: local whoami expected to reflect target identity]
              |
              v
[Corrected: VPN = connectivity only, authentication is separate]
              |
              v
[nmap used to confirm which remote access ports are actually open]
              |
              v
[RDP via xfreerdp confirmed as the correct, intended access method]
```

## What This Exercise Taught

Two independent lessons emerged from this session. First, permissions and network reachability are separate concerns — a correctly configured share can still be completely unreachable due to firewall rules, and diagnosing "access denied" style problems requires checking both layers independently rather than assuming a single cause. Second, remote access itself has layers that are easy to conflate: VPN connectivity gets you *to* a network, but actually becoming a specific user on a specific machine requires an authenticated protocol session (RDP, SSH, or WinRM) — and confirming which of those protocols is actually available, via direct port enumeration, is more reliable than assuming a particular method will work.

.
.
..


<div align="center">

#   Windows Registry

###   "Introduction to Windows" Learning Series

`Theory: Registry Structure` -> `regedit GUI Practice` -> `reg.exe Command Line` -> `UAC Enumeration`

</div>

---

## Overview

This section covers the Windows Registry — its structure, the five main hives, how to browse and modify it through `regedit.exe`, and how to query, add, and delete Registry data from the command line using `reg.exe`. It closes with using the Registry to safely enumerate whether UAC (User Account Control) is enabled on a system.

> Note: This section is theory and guided-practice focused. No target output had yet been captured or shared within this conversation, so no mistakes or corrections are documented here — the practical commands below were assigned as the next hands-on step, to be run and reviewed afterward.

---

## Objective

1. Understand what the Windows Registry is and why it matters for both administration and security.
2. Learn the Registry's structural hierarchy: Hive -> Key -> Subkey -> Value.
3. Know the five major Registry hives and what each one governs.
4. Practice creating and modifying Registry entries safely using `regedit.exe`.
5. Learn to query, add, and delete Registry data using `reg.exe` from the command line.
6. Use the Registry to safely check whether UAC is enabled, without modifying anything.

---

## Foundation & Theory

### What Is the Windows Registry?

The Windows Registry is a **hierarchical database** where Windows itself, and every installed application, stores its configuration and settings.

It can contain information about:

- User profiles
- Software
- Hardware
- Services
- Security policies
- Operating system settings

```text
Windows
   |
   +-- Registry
         |-- User settings
         |-- Software settings
         |-- Hardware settings
         |-- Services
         `-- Security policies
```

> Real-World Understanding: two concrete examples were used to make this concept tangible. First, when Windows needs to decide which application should start automatically after a user logs in, that configuration lives in the Registry. Second, whether a particular security feature is enabled or disabled on the system is also typically a Registry-stored setting. This is exactly why the Registry matters for both system administration and security work — it is the single place where "how is this machine configured to behave" actually lives.

### Why Modifying the Registry Requires Care

The Registry holds critical system configuration. An incorrect change can have real consequences:

```text
Wrong Registry change
        |
        v
Application/system component
        |
        v
May stop working
```

**Guidance carried from the module:** understand what a setting actually does before changing it, and ideally test changes in a lab environment first. This mirrors a broader cybersecurity habit: **read-only enumeration first, modification only when necessary and understood.**

### Registry Structure

The Registry can be mentally modeled the same way as a filesystem:

```text
Registry
   |
   v
 Hive
   |
   v
  Key
   |
   v
Subkey
   |
   v
 Value
```

| Level | Analogy | Description |
|---|---|---|
| **Hive** | Drive letter | Top-level section of the Registry |
| **Key** | Folder | A container inside a hive |
| **Subkey** | Subfolder | A key nested inside another key |
| **Value** | File contents | The actual configuration setting |

### Worked Example — Reading a Registry Path

```text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion
```

| Segment | Role |
|---|---|
| `HKEY_LOCAL_MACHINE` | Hive |
| `SOFTWARE` | Key |
| `Microsoft` | Subkey |
| `Windows` | Subkey |
| `CurrentVersion` | Subkey |

Selecting `CurrentVersion` in the Registry Editor displays its associated values in the right-hand pane.

### The Three Parts of a Registry Value

Every Registry value consists of:

| Part | Meaning |
|---|---|
| **Name** | The name of the setting |
| **Type** | What kind of data it holds |
| **Data** | The actual configuration content |

**Example:**

| Name | Type | Data |
|---|---|---|
| `CourseName` | `REG_SZ` | `Windows Fundamentals` |

### The Five Major Registry Hives

| Hive | Short Form | Purpose |
|---|---|---|
| `HKEY_CURRENT_USER` | HKCU | Settings for the currently logged-in user |
| `HKEY_LOCAL_MACHINE` | HKLM | System-wide settings |
| `HKEY_CLASSES_ROOT` | HKCR | File associations / application registration |
| `HKEY_USERS` | HKU | All loaded user profiles |
| `HKEY_CURRENT_CONFIG` | HKCC | Current hardware configuration |

> Concept — HKCU vs. HKLM: this is the single most important distinction in this section. `HKCU` governs the current user only, while `HKLM` governs the entire computer. As a rule, `HKLM` changes typically require administrative privileges, while a standard user can generally modify the parts of `HKCU` relevant to their own account without elevation. This split maps directly onto later privilege-escalation reasoning: a Registry key that a low-privileged user can write to under `HKLM` (rather than the expected `HKCU`-only access) can be a meaningful security finding.

### Registry Data Types

| Type | Meaning |
|---|---|
| `REG_SZ` | Normal text/string |
| `REG_DWORD` | 32-bit number |
| `REG_QWORD` | 64-bit number |
| `REG_MULTI_SZ` | Multiple strings |
| `REG_BINARY` | Raw binary data |

Quick reference for the three most commonly encountered types:

```text
REG_SZ    -> String
REG_DWORD -> 32-bit number
REG_QWORD -> 64-bit number
```

---

## Practical — Registry Editor (GUI)

`regedit.exe` is the built-in graphical tool for browsing and modifying the Registry.

**Opening it:**

- Start Menu -> type `regedit`

or:

```text
Win + R
   |
   v
regedit
   |
   v
Enter
```

**Layout:**

- **Left pane** — the Registry hierarchy (hives, keys, subkeys)
- **Right pane** — the values contained in whatever is selected on the left
- **Address bar** — shows the full current Registry path

### Guided Practice Key

Rather than modifying an existing system setting, the module's safe practice exercise is to create a brand-new key:

**Navigate to:**

```text
HKEY_CURRENT_USER\Software
```

**Create a new key:**

```text
HTB-Academy
```

**Inside it, create two values:**

| Value Name | Type | Data |
|---|---|---|
| `CourseName` | String Value | `Windows Fundamentals` |
| `LabComplete` | DWORD (32-bit) Value | `1` |

**Purpose:** This exercise is designed specifically to make the Hive -> Key -> Value -> Type -> Data hierarchy tangible through hands-on practice, using a safe, newly created key rather than risking any existing system configuration.

---

## Practical — reg.exe (Command Line)

`reg.exe` allows querying and modifying the Registry directly from the command line.

```cmd
reg /?
```

**Purpose:** Lists available operations, including:

```text
QUERY
ADD
DELETE
COPY
SAVE
LOAD
UNLOAD
RESTORE
COMPARE
EXPORT
IMPORT
FLAGS
```

The module highlights `query`, `add`, `delete`, `export`, and `import` as the most commonly used operations.

### reg query — Reading Registry Data

```cmd
reg query "HKCU\Software\HTB-Academy"
```

**Purpose:** Reads and displays the contents of a Registry key.

**Expected output (based on the practice key created above):**

```text
HKEY_CURRENT_USER\Software\HTB-Academy
    CourseName      REG_SZ       Windows Fundamentals
    LabComplete     REG_DWORD    0x1
```

**Interpretation:**

```text
CourseName  -> REG_SZ    -> Windows Fundamentals
LabComplete -> REG_DWORD -> 1   (displayed in output as 0x1, hexadecimal)
```

### Querying a Specific Value

```cmd
reg query "HKCU\Software\HTB-Academy" /v CourseName
```

**Purpose:** `/v` restricts the query to a single named value instead of listing everything under the key.

### Important reg query Options

| Option | Purpose |
|---|---|
| `/v ValueName` | Query a specific value |
| `/ve` | Query the default/empty value |
| `/s` | Recurse through all subkeys and values (conceptually similar to `dir /s`) |
| `/f` | Search for a data/pattern match |
| `/k` | Search within key names |
| `/d` | Search within data |
| `/c` | Case-sensitive search |
| `/e` | Exact matches only |
| `/t` | Restrict to a specific Registry data type |
| `/reg:32` | Use the 32-bit Registry view |
| `/reg:64` | Use the 64-bit Registry view |

### reg add — Creating or Modifying Values

```cmd
reg add "HKCU\Software\HTB-Academy" /v CreatedBy /t REG_SZ /d "reg.exe" /f
```

**Breakdown:**

| Part | Meaning |
|---|---|
| `reg add` | Add a new value, or modify an existing one |
| `/v CreatedBy` | The value's name |
| `/t REG_SZ` | The value's data type |
| `/d "reg.exe"` | The actual data to store |
| `/f` | Skip the confirmation prompt |

**Modifying an existing value** uses the exact same command structure:

```cmd
reg add "HKCU\Software\HTB-Academy" /v LabComplete /t REG_DWORD /d 0 /f
```

This changes `LabComplete`'s data from `1` to `0`. Verify the change afterward with:

```cmd
reg query "HKCU\Software\HTB-Academy"
```

### reg delete — Removing Registry Data

**Deleting a single value only:**

```cmd
reg delete "HKCU\Software\HTB-Academy" /v CreatedBy /f
```

**Deleting an entire key (and everything inside it):**

```cmd
reg delete "HKCU\Software\HTB-Academy" /f
```

**The critical difference:**

```text
/v CreatedBy         -> deletes only that one value
/f without /v         -> deletes the entire key and all its values
```

> Warning: `/f` bypasses the confirmation prompt entirely — the target path must be checked carefully before running any `reg delete` command with `/f`. The module explicitly warns that an incorrect Registry deletion can cause serious system damage.

---

## Registry Permissions

Registry keys carry their own permission model — not every user can modify every key.

```text
HKCU -> Current user settings -> generally easier for the current user to modify
HKLM -> System-wide settings  -> usually requires elevated/administrative privileges
```

Attempting an action without sufficient permission typically produces one of:

```text
Access is denied
```

or:

```text
The requested operation requires elevation
```

---

## UAC — User Account Control

**UAC (User Account Control)** exists to require approval before applications can make administrative-level changes to a system.

**Relevant Registry path:**

```text
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
```

**Key value:**

| Value | Type | Meaning |
|---|---|---|
| `EnableLUA` | `REG_DWORD` | `1` = UAC enabled, `0` = UAC disabled |

### Safely Checking UAC Status (Read-Only)

```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v EnableLUA
```

**Example output:**

```text
EnableLUA    REG_DWORD    0x1
```

**Interpretation:** `0x1` means UAC is currently enabled.

> Technical Note: this command is a pure read/query operation — nothing on the system is being modified by running it. Actually changing UAC's state requires administrative privileges and normally a system restart, and the module specifically restricts disabling UAC to an isolated lab environment rather than a production or shared system.

---

## Cybersecurity Perspective

The Registry matters to both sides of an assessment.

**Attacker perspective** — the Registry can be enumerated for information about:

- OS settings
- User settings
- Installed software
- Services
- Security configuration
- UAC status

**Defender perspective** — suspicious Registry modifications are a key investigative target, especially around:

- Changed security settings
- Changed startup/autorun configuration
- Suspicious software configuration entries
- Changed UAC settings

> Concept: the Registry should be mentally modeled as Windows' own configuration database — anyone trying to understand how a machine is set up to behave, whether for legitimate administration, offense, or defense, ends up looking here.

---

## Complete Mental Model

```text
Windows Registry
       |
       +-- Hives
       |     |-- HKCU
       |     |-- HKLM
       |     |-- HKCR
       |     |-- HKU
       |     `-- HKCC
       |
       +-- Keys
       |
       +-- Subkeys
       |
       `-- Values
             |-- Name
             |-- Type
             `-- Data
```

**Command-line mapping:**

```text
reg
 |
 +-- query  -> Read
 +-- add    -> Create/Modify
 `-- delete -> Delete
```

---

## Assigned Practical Steps

The following read-only commands were assigned to be run against the actual HTB Windows target, with outputs to be reviewed afterward for hive/key/value/type/data identification:

```cmd
reg query "HKCU\Software"
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v EnableLUA
reg /?
```

> Note: These commands were assigned as the next hands-on step in this learning session. Their actual outputs had not yet been captured or reviewed within this conversation, so no interpretation of live target data is documented here — this will be added once those outputs are available.

---

## Technical Concepts Recap

- The Registry follows a strict hierarchy: **Hive -> Key -> Subkey -> Value**, and every value carries a **Name**, **Type**, and **Data**.
- `HKCU` governs the current user; `HKLM` governs the entire machine and typically requires elevated privileges to modify.
- `REG_SZ` (string), `REG_DWORD` (32-bit number), and `REG_QWORD` (64-bit number) are the most commonly encountered data types.
- `reg query`, `reg add`, and `reg delete` map directly to Read, Create/Modify, and Delete operations on the Registry from the command line.
- `EnableLUA` under `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System` controls whether UAC is enabled (`1`) or disabled (`0`), and can be safely checked with a read-only `reg query`.

---

## Quick Reference

```cmd
regedit                                                                    :: open Registry Editor GUI
reg /?                                                                     :: list reg.exe operations

reg query "HKCU\Software\HTB-Academy"                                      :: read all values under a key
reg query "HKCU\Software\HTB-Academy" /v CourseName                        :: read one specific value

reg add "HKCU\Software\HTB-Academy" /v CreatedBy /t REG_SZ /d "reg.exe" /f  :: add/modify a value
reg add "HKCU\Software\HTB-Academy" /v LabComplete /t REG_DWORD /d 0 /f     :: modify an existing DWORD value

reg delete "HKCU\Software\HTB-Academy" /v CreatedBy /f                      :: delete one value only
reg delete "HKCU\Software\HTB-Academy" /f                                   :: delete entire key + values (use with care)

reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v EnableLUA   :: check UAC status (read-only)
```

**Hive quick map:** `HKCU` current user · `HKLM` whole machine · `HKCR` file associations · `HKU` all loaded profiles · `HKCC` current hardware config

**Data type quick map:** `REG_SZ` string · `REG_DWORD` 32-bit · `REG_QWORD` 64-bit · `REG_MULTI_SZ` multiple strings · `REG_BINARY` raw binary

---

## What This Section Taught

The Registry is Windows' single source of truth for configuration — and its structure (Hive -> Key -> Subkey -> Value, each value carrying a Name/Type/Data triplet) is what makes it possible to reason about *any* Windows setting, from UAC to startup behavior to security policy, using the exact same mental model and the exact same three commands (`query`, `add`, `delete`). The recurring caution around `/f` and HKLM-level changes reinforces a theme already seen elsewhere in this module: read-only enumeration comes first, and modification is only done once the target setting, its scope, and its potential impact are fully understood.
