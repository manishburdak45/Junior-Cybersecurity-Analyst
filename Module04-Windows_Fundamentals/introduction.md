<div align="center">

# Introduction to Windows

### Practical Walkthrough

`Theory` → `VPN Setup` → `Target Verification` → `RDP Access` → `Enumeration Question Solved`

</div>

---

## Objective

Build a working understanding of core Windows fundamentals required for penetration testing — OS versioning, WMI-based enumeration, and remote access via RDP — then apply that understanding to connect to a live HTB target and answer a module question correctly.

> ⚠️ This is a **learning module walkthrough**, not a full machine compromise. No exploitation, privilege escalation, or flag capture occurred in this session — the scope was module theory + RDP connectivity + one enumeration question.

---

## Environment / Target Information

| Item | Value |
|---|---|
| Target IP | `10.129.152.128` |
| Access Method | RDP (port 3389) |
| Provided Credentials | `htb-student` / (password provided by HTB, redacted here) |
| Connectivity | HTB Academy VPN (OpenVPN, `.ovpn` config) |
| Target OS (confirmed) | Windows 10, Build `19041` |

---

## Part 1 — Windows Fundamentals (Theory)

### Windows Desktop vs Windows Server

Windows exists in two primary product lines relevant to penetration testing:

- **Windows Desktop** — end-user operating system (Windows XP, Vista, 7, 8, 10, 11). Runs on individual workstations/laptops.
- **Windows Server** — infrastructure operating system (Server 2003, 2008, 2012, 2016, 2019). Hosts services such as Active Directory, file sharing, and web infrastructure.

Microsoft introduced Windows on **November 20, 1985**, as a graphical shell over MS-DOS. Windows 95 was the first full DOS/Windows integration and introduced built-in internet support along with Internet Explorer. Windows Server debuted in 1993 with **Windows NT 3.1 Advanced Server**. Windows Server 2000 introduced **Active Directory**, originally built to help administrators manage file sharing, data encryption, and VPNs.

### Legacy / Unsupported Windows — Security Relevance

As new Windows versions ship, older versions reach **End of Life (EOL)** and stop receiving security updates (unless an organization purchases extended/long-term support).

- Windows Server 2008 and Server 2012 reached EOL for security updates on **January 14, 2020**.
- Only Server 2012 R2 and later remain in mainstream support.
- Microsoft has issued **out-of-band patches** for otherwise-unsupported Windows versions in the past — most notably for the critical **SMBv1 vulnerability known as EternalBlue**.

> **Why this matters for an assessor:** organizations frequently keep legacy Windows systems running to support critical applications or due to budget/operational constraints. Understanding the differences between versions — and the misconfigurations/vulnerabilities specific to each — is a core assessment skill.

### Windows Version Numbers

Windows marketing names (e.g., "Windows 10") map to internal **NT version numbers**. This mapping is important because multiple different products can share the same NT version number.

| Operating System Name(s) | NT Version Number |
|---|---|
| Windows NT 4 | 4.0 |
| Windows 2000 | 5.0 |
| Windows XP | 5.1 |
| Windows Server 2003, 2003 R2 | 5.2 |
| Windows Vista, Server 2008 | 6.0 |
| Windows 7, Server 2008 R2 | 6.1 |
| Windows 8, Server 2012 | 6.2 |
| Windows 8.1, Server 2012 R2 | 6.3 |
| Windows 10, Server 2016, Server 2019 | 10.0 |

> **Note:** Windows 10, Server 2016, and Server 2019 all share NT version **10.0**. The version number alone cannot distinguish between them — the `Caption` field (or build number context) is needed to tell Desktop from Server.

### WMI Enumeration — Get-WmiObject

`Get-WmiObject` is a PowerShell cmdlet used to query **Windows Management Instrumentation (WMI)** classes for system information.

Key classes covered in this module:

| WMI Class | Purpose |
|---|---|
| `Win32_OperatingSystem` | OS version and build number |
| `Win32_Process` | Listing of running processes |
| `Win32_Service` | Listing of installed/running services |
| `Win32_BIOS` | BIOS (firmware) information |

`Get-WmiObject` also supports a `ComputerName` parameter to query **remote** systems, and can be used to start/stop services on local or remote machines.

### Local vs Remote Access

- **Local access** — direct interaction with a machine via keyboard/trackpad/mouse and a display. The most common form of access to any computer.
- **Remote access** — accessing a computer over a network. Local access to *some* machine is always required first, in order to reach a remote one.

Common remote access technologies mentioned in the module:

- Virtual Private Networks (VPN)
- Secure Shell (SSH)
- File Transfer Protocol (FTP)
- Virtual Network Computing (VNC)
- Windows Remote Management / PowerShell Remoting (WinRM)
- Remote Desktop Protocol (RDP) — the focus of this module

### Remote Desktop Protocol (RDP)

RDP uses a **client/server architecture**:

- The **client** specifies a target IP address or hostname to connect to.
- The **server** is the target machine with RDP access enabled.
- RDP listens by default on **logical port 3389**.

**IP address vs port analogy (as covered in the module):** a network subnet is like a street; an IP address is like a house on that street; logical ports are like the doors/windows used to reach specific applications inside that house.

By default, **remote access is not enabled** on Windows out of the box — it must be explicitly turned on. HTB Academy pre-configures its Windows targets to permit RDP once connected via the Academy VPN.
<img width="1920" height="1080" alt="window 1 " src="https://github.com/user-attachments/assets/b22399a6-6ac5-4290-90a7-05f7a6d2fabc" />

**RDP client tools referenced:**

- `mstsc.exe` — built-in Windows RDP client (Remote Desktop Connection). Supports saving connection profiles, which can also save credentials — a detail with pentesting relevance, since **saved `.rdp` files** may be found on compromised systems during an engagement.
- `xfreerdp` — Linux-based RDP client, used for connecting to Windows targets from a Linux attack host (e.g., Pwnbox).
- Other clients mentioned: **Remmina**, **rdesktop**.

---

## Part 2 — Practical Walkthrough: VPN & RDP Connectivity

### Problem Encountered

Ping to the target (`10.129.152.128`) showed 100% packet loss, and the `tun0` interface was not visible — indicating the VPN itself was not properly connected (not a target-side issue).

### Step 1 — Verify OpenVPN Connection

```bash
sudo openvpn ~/Downloads/your-file.ovpn
```

Check the terminal output for the following confirmation line:

```text
Initialization Sequence Completed
```

If this line is absent, the VPN has not successfully connected.

### Step 2 — Confirm the tun0 Interface

In a new terminal:

```bash
ip a
```

or specifically:

```bash
ip a show tun0
```

**Purpose:** Confirms whether the VPN tunnel interface (`tun0`) exists. If it does not appear, the issue is with the VPN connection itself — not the target machine.

### Step 3 — Verify RDP Port Is Open

Once `tun0` is confirmed present, ping can be ignored (ICMP may be filtered) — the actual RDP service should be checked directly:

```bash
nmap -Pn -p 3389 10.129.152.128
```

**Expected output:**

```text
3389/tcp open
```

**Purpose:** `-Pn` skips host discovery (useful when ICMP is blocked) and checks specifically whether the RDP port is open and reachable.

### Step 4 — Connect via xfreerdp

```bash
xfreerdp /v:10.129.152.128 /u:htb-student /p:'YOUR_PASSWORD'
```

**Purpose:** Connects directly to the target's RDP service using the provided HTB Academy credentials, as demonstrated in the module's own example.

> ⚠️ Password value was redacted in the original conversation and is represented here as a placeholder only.

---

## Part 3 — Enumeration Question Solved

### Module Question

> Which Windows NT version is installed on the workstation? (i.e. Windows X — case sensitive)

### Approach

The module itself does not directly state the interactive target's NT version — it only shows an **example** machine (Windows 10, build 19041) for illustration. To get the real answer, the target itself had to be queried directly.

### Command Executed

```powershell
Get-WmiObject -Class win32_OperatingSystem | select Version,BuildNumber
```

### Actual Output

```text
PS C:\Users\htb-student> Get-WmiObject -Class win32_OperatingSystem | select Version,BuildNumber

Version    BuildNumber
-------    -----------
10.0.19041 19041
```

### Reasoning

- `Version` returned as `10.0.19041` → NT version `10.0`.
- Per the NT version mapping table (Part 1), NT version `10.0` corresponds to **Windows 10 / Server 2016 / Server 2019**.
- Since the target is confirmed as a **workstation** (Desktop OS, not Server), the correct mapped name is **Windows 10**.

### Answer

```text
Windows 10
```

> Case-sensitive as required by the question: capital `W`, followed by `indows 10` exactly as shown.

---

## What Actually Happened

1. VPN connectivity was initially broken (`tun0` missing, 100% ping loss to target).
2. VPN connection was verified via the `Initialization Sequence Completed` message and confirmed via `ip a show tun0`.
3. RDP port `3389` was confirmed open on the target using `nmap -Pn`.
4. Successful RDP connection was established using `xfreerdp` with provided HTB Academy credentials.
5. The target's OS version was enumerated using `Get-WmiObject -Class win32_OperatingSystem`.
6. The returned version string (`10.0.19041`) was mapped to its correct marketing name (`Windows 10`) using the NT version reference table from module theory.

---

## Mistakes & Dead Ends

| Issue | Cause | Resolution |
|---|---|---|
| 100% packet loss pinging target | VPN not actually connected (`tun0` absent) | Verified OpenVPN log for `Initialization Sequence Completed`, then re-checked `tun0` |
| Uncertainty on exact answer format | Module only provides an **example** OS (Windows 10, build 19041), not the actual target's confirmed version | Queried the live target directly via `Get-WmiObject` instead of relying on the module's example |

---

## Technical Concepts Recap

- **NT version numbers** are internal identifiers that do not always map 1:1 with marketing names — Windows 10, Server 2016, and Server 2019 all report NT version `10.0`.
- **WMI (`Get-WmiObject`)** is a core Windows enumeration mechanism, usable both locally and remotely (`ComputerName` parameter).
- **RDP** is a client/server protocol defaulting to port `3389`; disabled by default on Windows and must be explicitly enabled.
- **`.rdp` files** can retain saved connection profiles/credentials — relevant during post-compromise file discovery on an engagement.

---

## Lessons Learned

- Always verify the VPN tunnel interface (`tun0`) before troubleshooting target-side connectivity issues — a "target unreachable" symptom is frequently a local VPN problem.
- When a module gives only an **example** value, do not assume it is the actual answer — always enumerate the live target directly.
- `-Pn` in `nmap` is essential when ICMP (ping) is blocked or unreliable but the actual service port still needs verification.

---

## Quick Reference / Cheat Sheet

**VPN Troubleshooting**
```bash
sudo openvpn ~/Downloads/your-file.ovpn      # look for "Initialization Sequence Completed"
ip a show tun0                                # confirm VPN interface exists
```

**RDP Connectivity**
```bash
nmap -Pn -p 3389 <target>                     # confirm RDP port is open
xfreerdp /v:<target> /u:<username> /p:'<password>'   # connect via RDP
```

**Windows Version Enumeration**
```powershell
Get-WmiObject -Class win32_OperatingSystem | select Version,BuildNumber
Get-WmiObject -Class win32_OperatingSystem | select Caption
```

**NT Version Quick Map**
```text
5.1  → Windows XP
5.2  → Server 2003 / 2003 R2
6.0  → Vista / Server 2008
6.1  → Windows 7 / Server 2008 R2
6.2  → Windows 8 / Server 2012
6.3  → Windows 8.1 / Server 2012 R2
10.0 → Windows 10 / Server 2016 / Server 2019
```

**Key WMI Classes**
```text
Win32_OperatingSystem  → version/build
Win32_Process          → running processes
Win32_Service          → services
Win32_BIOS             → firmware info
```
