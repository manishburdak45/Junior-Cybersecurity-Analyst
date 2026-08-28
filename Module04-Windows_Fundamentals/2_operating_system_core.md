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
