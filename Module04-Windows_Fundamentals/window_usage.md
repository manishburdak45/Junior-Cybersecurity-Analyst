# Microsoft Management Console (MMC)

> `Console` -> `Snap-ins` -> `Local/Remote Management` -> `Saved .msc Console`

---

## What Is MMC?

**MMC (Microsoft Management Console)** is an administrative framework that lets Windows' different management tools be grouped and used together inside a single console — to manage hardware, software, and network components on a Windows host.

```text
MMC
 |-- Services
 |-- Event Viewer
 |-- Computer Management
 `-- Other administrative tools
```

It lets individual administrative tools be organized into one customized console rather than opened separately.

**Some background:** MMC has been available since Windows Server 2000 and runs on all Windows versions. It can also be used to build custom tools and distribute them to users.

---

## Snap-ins

The most important term in this topic: a **snap-in**.

A snap-in is an administrative tool/component that can be added inside MMC.

```text
MMC
  |
  v
Add/Remove Snap-ins
  |
  v
Services
```

Once added, that Services snap-in becomes available inside MMC.

**Mental model:**

```text
MMC     = Container / Console
Snap-in = A tool added inside that container
```

MMC's snap-in system allows administrators to build a customized console containing only the specific administrative tools needed to manage a given set of services — and these snap-ins can manage both local and remote systems.

---

## Local vs. Remote Computer

When adding a snap-in, Windows may prompt:

```text
Manage:
○ Local computer
○ Another computer
```

The same administrative tool can therefore be pointed at either:

```text
Local system
```

or

```text
Remote Windows system
```

This matters for cybersecurity because centralized administration environments commonly involve administrators managing remote systems through exactly this kind of tooling.

---

## Opening MMC

From the Start menu, type:

```text
mmc
```

Or via the Run dialog:

```text
Win + R
```
then:
```text
mmc
```

When opened for the first time, the console will be blank — showing an empty `Console Root`.

---

## Adding a Snap-in

```text
File
  |
  v
Add or Remove Snap-ins
  |
  v
Select Snap-in
  |
  v
Add
```

**Example:** adding the **Services** snap-in. Windows may then prompt:

```text
Local computer
      OR
Another computer
```

Once added, snap-ins appear on the left-hand side of the MMC window.

---

## Saving a Console — the .msc File

A customized MMC console — with its configured snap-ins — can be saved for reuse.

**File extension:**

```text
.msc
```

**Example:** a saved console named `management.msc`.

```text
MMC
 |
 v
Snap-ins configured
 |
 v
Save
 |
 v
management.msc
 |
 v
Opens directly next time
```

By default, saved `.msc` files are placed in the **Windows Administrative Tools** directory under the Start menu, and can be reloaded directly the next time MMC is needed.

---

## Cybersecurity Relevance

MMC itself is not an attack tool — it is primarily a Windows administration framework. Its relevance comes from what its snap-ins expose for management and investigation.

**Example:**

```text
MMC
 |
 v
Services
 |
 v
Service configuration
```

or:

```text
MMC
 |
 v
Event-related administrative tools
 |
 v
Logs / events investigation
```

Because of this, a basic understanding of MMC is useful for both SOC analysts and Windows administrators — it's the common container through which many of the administrative and investigative tools covered elsewhere (Services, Event Viewer, Computer Management) are actually accessed.

---

## Question-Decoding Practice

| Question | Answer |
|---|---|
| "What does MMC stand for?" | Microsoft Management Console |
| "What are the administrative tools added to MMC called?" | Snap-ins |
| "What file extension is used when saving a customized MMC console?" | `.msc` |
| "Which command can be used to open Microsoft Management Console?" | `mmc` |

---

## Final Cheat Sheet

```text
MMC
  |
  v
Microsoft Management Console
  |
  v
Organizes/manages administrative tools inside a single console
```

```text
Snap-in
  |
  v
Administrative tool/component added to MMC
```

```text
Local / Remote
  |
  v
A snap-in can manage either the local machine or another computer
```

```text
.msc
  |
  v
A saved MMC console
```

```text
mmc
  |
  v
Opens MMC
```

**One-line summary:** MMC = container, Snap-ins = tools, `.msc` = saved console.




# Windows Subsystem for Linux (WSL)

> `Windows` -> `WSL` -> `Linux Environment` -> `Bash + Native Linux Tools`

---

## What Is WSL?

**WSL = Windows Subsystem for Linux.**

WSL is a feature that allows Linux binaries to run natively on Windows 10 and Windows Server 2019 — providing a Linux command-line environment directly inside Windows.

```text
Windows
   |
   v
  WSL
   |
   v
Linux environment
   |
   v
Bash + Linux commands
```

It was originally intended for developers who needed to run Bash, Ruby, and native Linux command-line tools such as `sed`, `awk`, and `grep` directly on their Windows workstation, without needing a separate Linux machine or virtual machine.

---

## WSL 1 vs. WSL 2

**WSL 1** — a Linux-compatibility approach for running Linux binaries on Windows.

**WSL 2** — released in May 2019, introduced a **real Linux kernel**, built using a subset of Hyper-V features.

```text
WSL 1 -> Linux compatibility approach
WSL 2 -> Real Linux kernel
```

The underlying implementation details of how each version achieves this aren't the priority here — the key distinction to retain is that WSL 2 runs an actual Linux kernel rather than only translating Linux system calls.

---

## Enabling / Installing WSL

From an **Administrator** PowerShell session:

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

**Breakdown:**

| Segment | Meaning |
|---|---|
| `Enable-WindowsOptionalFeature` | Enables a Windows optional feature |
| `-Online` | Targets the currently running Windows installation |
| `-FeatureName` | Specifies which feature to enable |
| `Microsoft-Windows-Subsystem-Linux` | The WSL feature itself |

> Important: this command must be run with Administrator privileges.

Once WSL is enabled, a Linux distribution can either be downloaded and installed from the Microsoft Store, or manually downloaded, unpacked, and installed from the command line.

---

## Starting Bash

WSL installs an application called **`Bash.exe`**. Once WSL is enabled, typing:

```text
bash
```

into a Windows console spawns a Bash shell.

```text
Windows CMD/PowerShell
        |
        v
      bash
        |
        v
WSL Linux shell
```

---

## A Full Linux-Like Environment

Inside the Bash shell, the standard Linux directory structure is present:

```text
/bin
/dev
/home
/lib
/media
/mnt
/opt
/proc
/root
/tmp
/usr
/var
```

**Example:**

```bash
ls /
```

**Output:**

```text
bin dev home lib lLib64 media opt root sbin srv tmp var
boot etc init 1lib32 Libx32 mnt proc run Snap sys usr
```

This gives the full look and feel of a genuine Linux host from within Windows.

---

## Accessing Windows Drives from WSL

This is one of the most important practical facts in this topic.

Windows drives are accessible from within WSL through the **`/mnt`** directory.

```text
Windows C:
    |
    v
   WSL
    |
    v
  /mnt/c
```

Generally:

```text
/mnt/c  -> Windows C: drive
/mnt/d  -> Windows D: drive
/mnt/e  -> Windows E: drive
```

**The key point:** the transition between the WSL environment and the underlying Windows host OS is seamless — Windows volumes (including the `C$` volume) are reachable directly through `/mnt`, and once inside the Bash shell, WSL can be interacted with exactly like any other Linux-based operating system: running commands, installing updates/packages, and so on.

---

## Checking System Information — uname -a

```bash
uname -a
```

**Example output:**

```text
Linux WS01 4.4.0-18362-Microsoft #476-Microsoft Frit Nov 01 16:53:00
PST 2019 x86_64 x86_64 x86_64 GNU/Linux
```

The `Linux` at the start of this output confirms the shell is running inside WSL's Linux environment, not native Windows.

| Part | Meaning |
|---|---|
| `uname` | Displays system information |
| `-a` | Displays all available system information |

---

## Cybersecurity Perspective

WSL is interesting from a security engineering standpoint because a single Windows machine can now genuinely host **two coexisting environments**:

```text
Windows
   +
Linux environment
```

This means a security analyst needs to account for both layers when monitoring or investigating a system:

```text
Windows host
     |
     v
    WSL
     |
     v
Linux processes/files/tools
```

Both environments — the native Windows side and the WSL Linux side — can be relevant during security monitoring and investigation, since activity, files, and tools can exist within either one.

---

## Question-Decoding Practice

| Question | Answer |
|---|---|
| "What does WSL stand for?" | Windows Subsystem for Linux |
| "Which command starts a Bash shell from Windows?" | `bash` |
| "Where can Windows drives be accessed from within WSL?" | `/mnt` (e.g., `/mnt/c` for the C: drive) |
| "Which command displays Linux system information?" | `uname -a` |
| "What kernel does WSL 2 use?" | A real Linux kernel |

---

## Final Cheat Sheet

```text
WSL
  |
  v
Windows Subsystem for Linux
  |
  v
A Linux environment inside Windows
```

```text
WSL 2
  |
  v
Real Linux kernel
```

```text
bash
  |
  v
Starts the WSL Bash shell
```

```text
ls /
  |
  v
Lists the Linux root directory structure
```

```text
/mnt
  |
  v
Windows drives accessible from within WSL
```

```text
/mnt/c
  |
  v
The Windows C: drive
```

```text
uname -a
  |
  v
Linux system information
```

```text
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
  |
  v
Enables the WSL feature
```

**One-line summary:** WSL provides a Linux environment inside Windows, where Bash and native Linux tools can be used directly, and Windows drives remain reachable through `/mnt`.
