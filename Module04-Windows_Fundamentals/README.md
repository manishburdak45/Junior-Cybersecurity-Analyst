# Windows Fundamentals

> Hack The Box — Junior Cybersecurity Analyst (JCA)

## 📌 Module Overview

Windows is one of the most widely used operating systems in enterprise environments.  
Understanding its file system, permissions, command-line tools, management interfaces, networking components, and security architecture is essential for both offensive and defensive cybersecurity work.

This module focuses on understanding Windows from a cybersecurity perspective rather than simply memorizing commands.

---

## 🎯 Learning Objectives

By completing this module, I learned how to:

- Understand the basic structure of Windows
- Navigate the Windows file system
- Understand NTFS permissions
- Understand Windows network/share permissions
- Work with SMB
- Understand interactive and non-interactive Windows sessions
- Use CMD and PowerShell
- Understand PowerShell aliases and execution policy
- Use WMI and WMIC for system information
- Understand Microsoft Management Console (MMC)
- Understand Windows Subsystem for Linux (WSL)
- Compare Server Core and Desktop Experience
- Understand important Windows security concepts
- Work with SIDs, ACLs, ACEs, DACLs and SACLs
- Understand UAC and Registry settings
- Understand application whitelisting and AppLocker
- Understand the role of Group Policy and Windows Defender

---

# 1. Windows Operating System Structure

The Windows boot partition normally starts at:

Important directories include:

Directory	Purpose
Program Files	64-bit applications on 64-bit Windows
Program Files (x86)	32-bit applications on 64-bit Windows
ProgramData	Application data shared between users
Users	User profiles
Default	Template for newly created user profiles
Public	Shared files accessible to users
AppData	Per-user application data and settings
Windows	Main Windows operating system files
System32	Core Windows components and APIs
SysWOW64	32-bit system components on 64-bit Windows
WinSxS	Windows component store
Important User Directories

A user's profile can contain:

C:\Users\<username>\

Inside it:

AppData\
Desktop\
Documents\
Downloads\
Pictures\

AppData contains application-specific user data.

It has three important subdirectories:

AppData
├── Roaming
├── Local
└── LocalLow
2. Exploring the Windows File System
dir

CMD can be used to list directory contents:

dir C:\ /a

/a displays files and directories including hidden/system items.

Recursive directory listing
tree C:\ /f

/f displays files as well as folders.

For large output:

tree C:\ /f | more
3. Windows File Systems

Windows supports several file systems:

FAT12
FAT16
FAT32
NTFS
exFAT

Modern Windows systems primarily use NTFS.

FAT32

FAT32 is commonly found on removable storage such as:

USB drives
SD cards
Other portable storage
Advantages
Broad device compatibility
Cross-platform compatibility
Limitations
Maximum individual file size is less than 4 GB
Limited built-in security features
No native file encryption mechanism comparable to NTFS
4. NTFS

NTFS = New Technology File System

NTFS is the default Windows file system and provides features such as:

File and folder permissions
Large partition support
Journaling
Improved reliability
Metadata support
NTFS Permissions

Important basic permissions:

Permission	Meaning
Full Control	Read, write, modify, delete and change permissions
Modify	Read, write and delete
Read & Execute	Read and execute files
List Folder Contents	List folders/files and execute files
Read	Read files/folders
Write	Create/write files and folders
Special Permission

Traverse Folder

Allows a user to move through a directory structure to reach another permitted object even when they cannot normally list the parent directory.

5. NTFS Permission Inheritance

NTFS permissions are normally inherited from parent directories.

Example:

C:\
└── Users\
    └── Bob\
        └── Documents\

Permissions can flow from:

C:\
 ↓
Users\
 ↓
Bob\
 ↓
Documents\

Inheritance can be disabled when custom permissions are required.

6. ICACLS

icacls is a Windows command-line utility used to view and manage NTFS permissions.

View permissions
icacls C:\Windows
Grant permissions

Example:

icacls C:\Users /grant joe:f
Remove permissions
icacls C:\Users /remove joe
Common ICACLS Permission Codes
Code	Meaning
F	Full access
M	Modify
RX	Read & Execute
R	Read
W	Write
D	Delete
N	No access
Inheritance Flags
Flag	Meaning
(CI)	Container inherit
(OI)	Object inherit
(IO)	Inherit only
(NP)	Do not propagate inherit
(I)	Inherited permission


7. NTFS vs Share Permissions

These are not the same thing.

NTFS Permissions

Apply directly to files/folders on the Windows file system.

They apply when accessing the resource locally as well as when accessed remotely.

Share Permissions

Apply when accessing a shared resource through a network share, commonly using SMB.

8. SMB

SMB = Server Message Block

SMB is used by Windows to share network resources such as:

Files
Folders
Printers

Typical communication:

Client
  |
  | SMB request
  v
Windows Server
  |
  v
Shared Resource
Share Permissions
Permission	Meaning
Full Control	Full share access including changing permissions
Change	Read, edit, delete and add
Read	View contents

When a resource is accessed remotely, both share permissions and NTFS permissions can affect the resulting access.

9. Windows Sessions

Windows sessions can be broadly divided into:

Interactive Sessions

A user authenticates and interacts directly with the system.

Examples:

Local login
RDP
runas
Non-Interactive Sessions

Used by Windows services/applications without normal user interaction.

Important accounts:

Local System
NT AUTHORITY\SYSTEM

Very powerful Windows account used for operating-system-level tasks.

Local Service
NT AUTHORITY\LocalService

A lower-privileged service account.

Network Service
NT AUTHORITY\NetworkService

Has limited local privileges and can authenticate to network resources in certain situations.

10. Windows Command Prompt

CMD executable:

C:\Windows\System32\cmd.exe

CMD allows users to:

Execute commands
Manage files
Troubleshoot systems
Perform administration
Automate tasks
Create scripts/batch files
CMD Help
help

Specific command help:

help schtasks

Another common syntax:

ipconfig /?
11. PowerShell

PowerShell is Microsoft's powerful command-line and scripting environment.

It is especially useful for:

System administration
Automation
Security administration
WMI interaction
Scripting

PowerShell is based on the .NET framework.

12. PowerShell Cmdlets

PowerShell commands generally follow:

Verb-Noun

Example:

Get-ChildItem

This lists files/directories.

Useful PowerShell Aliases
ls

→ Get-ChildItem

cd

→ Set-Location

cat

→ Get-Content

?

→ Where-Object

Aliases can be viewed with:

Get-Alias
Finding an Alias

Example:

Get-Alias

If the target system has:

ifconfig -> ipconfig.exe

then:

ifconfig

is an alias for:

ipconfig.exe
13. PowerShell Execution Policy

Execution Policy controls how PowerShell scripts are handled.

Important policies:

Policy	Meaning
Restricted	Scripts cannot run
RemoteSigned	Downloaded scripts generally require signatures
AllSigned	Scripts require trusted signatures
Bypass	Scripts/configurations are not blocked
Unrestricted	Unsigned scripts can run with warnings
Undefined	No policy set at that scope

Check policies:

Get-ExecutionPolicy -List

Example:

MachinePolicy
UserPolicy
Process
CurrentUser
LocalMachine
Changing Policy for Current Process
Set-ExecutionPolicy Bypass -Scope Process

This changes the policy only for the current PowerShell process/session.

14. Windows Management Instrumentation (WMI)

WMI = Windows Management Instrumentation

WMI provides Windows management and monitoring capabilities.

It can be used for:

System information
Remote management
Security configuration
User/group management
System properties
Code execution
Process scheduling
Logging
15. WMIC

WMIC provides a command-line interface to WMI.

Help
wmic /?
Computer Name
wmic computersystem get name
Operating System Information
wmic os list brief

LIST lists information and BRIEF limits output to core properties.

16. PowerShell + WMI
Retrieve OS information
Get-WmiObject -Class Win32_OperatingSystem
Retrieve selected properties
Get-WmiObject -Class Win32_OperatingSystem |
Select-Object Version,BuildNumber,SerialNumber

Example properties:

Version
BuildNumber
SerialNumber
SystemDirectory
17. Invoke-WmiMethod

Invoke-WmiMethod is used to call methods associated with WMI objects.

Example concept:

Invoke-WmiMethod

A WMI method can perform an action on an object.

In the training example, a WMI method was used to rename a file.

A successful operation returned:

ReturnValue : 0
18. Microsoft Management Console (MMC)

MMC = Microsoft Management Console

MMC provides a framework for grouping Windows administrative tools.

MMC
 |
 +-- Snap-in
 +-- Snap-in
 `-- Snap-in
Snap-ins

Snap-ins are administrative components that can be added to MMC.

Examples include management tools such as:

Services
Other Windows administrative components

Snap-ins can often be configured to manage:

Local computer
Remote computer
Saving MMC Consoles

A customized MMC console can be saved as:

.msc

Example:

management.msc
19. Windows Subsystem for Linux (WSL)

WSL = Windows Subsystem for Linux

WSL allows Linux binaries and a Linux environment to run on Windows.

It can provide access to Linux tools such as:

bash
sed
awk
grep
WSL 2

WSL 2 introduced a real Linux kernel using a subset of Hyper-V functionality.

Starting Bash

From Windows:

bash
Linux File System

Inside WSL:

ls /

provides the Linux directory structure.

Windows Drives in WSL

Windows drives can be accessed through:

/mnt

For example:

/mnt/c

represents the Windows C: drive.

Check Linux System Information
uname -a
20. Desktop Experience vs Server Core
Server Core

Server Core is a minimal Windows Server installation designed for server functionality without the normal desktop GUI.

Benefits
Smaller attack surface
Less disk usage
Less memory usage
Lower management requirements
Server-focused environment
Management

Server Core is mainly managed using:

CMD
PowerShell
Remote management
MMC
RSAT
Server Core Is Not Completely GUI-Free

Some graphical applications remain supported, including:

Registry Editor
Notepad
System Information
Windows Installer
Task Manager
PowerShell

Some Sysinternals tools are also supported.

21. Sconfig

Sconfig is a text-based interface used for initial Server Core configuration.

It can be used for:

Network configuration
Windows Updates
Account management
Remote management
Windows activation

Technically, Sconfig is a VBScript executed through WScript.

22. Server Core vs Desktop Experience
Server Core
Minimal installation
       |
       v
Less resources
       |
       v
Smaller attack surface
       |
       v
CLI-focused management
Desktop Experience
Full GUI
   |
   v
More management tools
   |
   v
Easier GUI-based administration

Important:

Smaller attack surface does not automatically mean secure. Proper configuration, patching, authentication, monitoring and service security are still required.

23. Windows Security — SID

SID = Security Identifier

A SID uniquely identifies a Windows security principal.

Example:

S-1-5-21-...-1002

Important command for the current user's SID:

whoami /user
RID

RID = Relative Identifier

The final portion of a SID is commonly associated with the specific account identifier within the relevant security authority/domain context.

24. SAM

SAM = Security Accounts Manager

SAM is associated with Windows local account/security information.

Simplified model:

User Account
     |
     v
    SAM
     |
     v
Security/account information
25. ACL and ACE
ACL

ACL = Access Control List

An ACL contains access-control entries that determine permissions.

ACE

ACE = Access Control Entry

An ACE is an individual entry inside an ACL.

Example:

ACL
 |
 +-- Bob -> Read
 +-- Alice -> Modify
 `-- Guest -> Deny
26. DACL and SACL
DACL

DACL = Discretionary Access Control List

Controls whether access is allowed or denied.

DACL
 |
 +-- User A -> Read
 +-- User B -> Modify
 `-- User C -> Deny
SACL

SACL = System Access Control List

Used for auditing access attempts/actions.

Memory trick:

DACL -> Access decision
SACL -> Auditing

27. Access Token

An access token represents the security context associated with a user/process.

It can contain information such as:

User SID
Group membership
Security privileges

Simplified flow:

User Login
    |
    v
Access Token
    |
    v
Security Context
    |
    v
Authorization
28. UAC

UAC = User Account Control

UAC helps control privilege elevation in Windows.

Simplified:

Application
     |
     v
Needs elevated privileges?
     |
     v
UAC
     |
     v
Elevation

UAC is intended to reduce unnecessary use of administrative privileges.

29. EnableLUA

EnableLUA is a Registry value related to UAC configuration.

It is stored as:

REG_DWORD

Registry interaction can be performed from CMD using:

reg.exe

Query values:

reg query

Modify/add values:

reg add
30. Windows Registry

The Windows Registry is a centralized configuration database used by Windows and applications.

Important Registry hives include:

HKEY_LOCAL_MACHINE
HKEY_CURRENT_USER
HKEY_CLASSES_ROOT
HKEY_USERS
HKEY_CURRENT_CONFIG
HKCU

HKEY_CURRENT_USER

Short form:

HKCU

Stores settings associated with the currently logged-on user.

HKLM

HKEY_LOCAL_MACHINE

Short form:

HKLM

Contains machine/system-wide configuration.

31. Registry Value Types

Important types:

Type	Meaning
REG_SZ	String
REG_DWORD	32-bit number
REG_QWORD	64-bit number
REG_BINARY	Binary data
REG_MULTI_SZ	Multiple strings
Important HTB examples
EnableLUA -> REG_DWORD
Multiple text strings -> REG_MULTI_SZ
32. Registry Startup Locations

Two important Registry locations associated with startup execution are:

Run
RunOnce

Simplified:

User/System startup
       |
       v
Registry
       |
   +---+---+
   |       |
  Run   RunOnce
   |       |
Execute  One-time execution
Security relevance

Attackers can abuse startup locations for persistence.

However:

A Run/RunOnce entry is not automatically malicious. Legitimate applications may use these locations as well.

33. Application Whitelisting

Application whitelisting is a security approach where only approved applications are allowed to execute.

Application
     |
     v
Approved?
   /   \
 YES    NO
  |      |
Run    Block

This can reduce the ability of unauthorized software or malware to execute.

34. AppLocker

AppLocker provides Windows application execution control through rules.

Conceptually:

Application
     |
     v
AppLocker Rule
     |
  +--+--+
  |     |
Allow  Block

Rules can be used to control which applications/scripts are permitted.

35. Group Policy

Group Policy allows organizations to centrally configure Windows users and computers.

Example:

Domain
  |
  +-- Computer 1
  +-- Computer 2
  +-- Computer 3
  `-- Computer 4

Instead of configuring each system individually:

Central Group Policy
        |
        v
Multiple systems/users

This is particularly important in enterprise Windows environments.

36. Windows Defender

Microsoft Defender provides built-in protection against threats.

Simplified:

Files
Processes
Applications
     |
     v
Threat Detection
     |
     +-- Detect
     +-- Block
     `-- Remediate
Application Control vs Defender

Application control asks:

Is this application allowed to execute?

Defender asks:

Is this file/activity malicious or suspicious?

They are complementary security controls.

37. Cybersecurity Perspective

Windows Fundamentals provides the foundation for both offensive and defensive security work.

Attacker Perspective

An attacker may investigate:

Users
Permissions
Services
Processes
Registry
Shares
WMI
Configuration

The goal may be to identify:

Excessive privileges
Weak permissions
Exposed services
Sensitive files
Misconfigurations
Persistence locations
Defender Perspective

A defender needs to understand:

Who?
 ↓
SID / Account

What can they access?
 ↓
ACL / DACL

What are they doing?
 ↓
Process / Token / Logs

Can they elevate?
 ↓
UAC / Privileges

What executes at startup?
 ↓
Registry / Application Control

Is something malicious?
 ↓
Defender / Logging
38. Important Commands
CMD
dir C:\ /a

List files/directories including hidden/system items.

tree C:\ /f

Display directory structure including files.

ipconfig

Display network configuration.

whoami /user

Display current user's SID.

wmic /?

WMIC help.

wmic computersystem get name

Display computer name.

wmic os list brief

Display basic OS information.

reg query

Query Registry.

reg add

Add/modify Registry values.

icacls C:\Users

View NTFS permissions.

net share

View network shares.

PowerShell
Get-ChildItem

List files/directories.

Get-Alias

List aliases.

Get-ExecutionPolicy -List

Display execution policies.

Get-WmiObject -Class Win32_OperatingSystem

Retrieve OS information through WMI.

Get-WmiObject -Class Win32_OperatingSystem |
Select-Object Version,BuildNumber,SerialNumber

Retrieve selected OS properties.

Get-LocalUser

List local users.

Get-LocalUser -Name "bob.smith" |
Select-Object Name,SID

Find the SID of a specific local user.

39. Question-Decoding Method

Instead of memorizing every HTB question, identify the keywords.

Example 1

Find the SID of the current user.

current user
+
SID

Think:

whoami /user
Example 2

Find the SID of bob.smith.

specific user
+
SID

Think:

Get-LocalUser -Name "bob.smith" | Select-Object SID
Example 3

Find the serial number using WMI.

WMI
+
serial number

Think:

Get-WmiObject -Class Win32_OperatingSystem |
Select-Object SerialNumber
Example 4

Find the currently logged-on user's Registry hive.

currently logged-on user
+
Registry hive

Answer:

HKEY_CURRENT_USER
Example 5

What Registry data type stores multiple text strings?

multiple
+
text strings
+
Registry

Answer:

REG_MULTI_SZ

40. Industry Relevance
Topic	Main Security Area
Windows File System	Security Engineering / Blue Team
NTFS Permissions	Windows Security / Privilege Escalation
SMB	Network Security / Pentesting / Blue Team
CMD	General Windows Administration
PowerShell	Security Engineering / SOC / Pentesting
WMI	Administration / Detection / Pentesting
MMC	Windows Administration
WSL	Development / Security Engineering
Server Core	System Administration / Hardening
SID / ACL	Windows Security
UAC	Privilege Management
Registry	Endpoint Security / Persistence Analysis
AppLocker	Defensive Security
Group Policy	Enterprise Security
Defender	Blue Team / Endpoint Security
41. Interview Questions
Basic
What is NTFS?

Short answer:
NTFS is the primary Windows file system that supports features such as permissions, journaling, metadata and large partitions.

What is SMB?

Short answer:
SMB is a network protocol used by Windows to share resources such as files and printers.

What is PowerShell?

Short answer:
PowerShell is Microsoft's command-line shell and scripting environment used for administration, automation and system management.

What is WMI?

Short answer:
WMI is a Windows management infrastructure used to retrieve information and perform management operations locally or remotely.

Practical
How do you check NTFS permissions?
icacls C:\Users
How do you find the current user's SID?
whoami /user
How do you find OS version/build using WMI?
Get-WmiObject -Class Win32_OperatingSystem |
Select-Object Version,BuildNumber
How do you check PowerShell execution policies?
Get-ExecutionPolicy -List
Security
What is the difference between ACL and ACE?

ACL is the access-control list.
ACE is an individual entry inside that list.

Difference between DACL and SACL?

DACL controls access decisions.
SACL is used for auditing.

Why is Server Core considered to have a smaller attack surface?

Because it contains fewer components and does not include the normal full desktop GUI, reducing the number of components that could potentially be attacked.

What is UAC?

UAC is a Windows security mechanism that helps control privilege elevation.

Why is the Registry important to security?

The Registry contains many Windows and application configuration settings and can also contain startup locations that may be relevant when investigating persistence.

42. Common Mistakes / Lessons
Mistake 1 — Confusing PowerShell aliases with WMIC aliases

PowerShell aliases:

ls -> Get-ChildItem

are different from WMIC's alias system.

Mistake 2 — Assuming NTFS and Share Permissions are the same

They are different permission layers.

NTFS permissions
+
Share permissions

can both affect remote access.

Mistake 3 — Assuming Server Core means zero GUI

Server Core does not have the normal desktop GUI, but some graphical applications remain supported.

Mistake 4 — Using whoami /user for every SID question

whoami /user is useful for the current user.

For a specific account, query that account directly.

Mistake 5 — Treating every Registry startup entry as malware

Run and RunOnce can be legitimate.

Always investigate:

Path
Publisher
File
Hash
User
Context
Behavior

before determining whether something is malicious.

43. Practical Skill Checklist
Navigate Windows directories
Understand Windows directory structure
Understand FAT32 and NTFS
Read NTFS permissions with icacls
Understand SMB
Understand share permissions
Understand Windows sessions
Use CMD
Use PowerShell
Understand aliases
Understand Execution Policy
Use WMI/WMIC
Understand MMC
Understand WSL
Understand Server Core
Understand SIDs
Understand ACL/ACE/DACL/SACL
Understand Access Tokens
Understand UAC
Understand Registry hives and values
Understand Run/RunOnce
Understand AppLocker
Understand Group Policy
Understand Windows Defender
44. Final Mental Model
                         WINDOWS
                            |
       +--------------------+--------------------+
       |                    |                    |
   File System           Management          Security
       |                    |                    |
     NTFS              CMD / PowerShell        SID
       |                    |                    |
  Permissions               WMI               Token
       |                    |                    |
  ACL / ACE                 MMC               ACL
       |                    |                    |
  DACL / SACL               WSL              DACL/SACL
                            |                    |
                       Server Core              UAC
                                                 |
                                              Registry
                                                 |
                                          Run / RunOnce
                                                 |
                                          AppLocker
                                                 |
                                           Defender
```text
C:\
